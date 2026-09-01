Yes. M3.4 is the point where the allocator + block table become an actual **paged KV-cache execution path**.

We should implement it in the simplest possible way first. **Do not optimize anything yet.**

## What we're building

You currently have:

```text
BlockPool
    ↓
physical KV blocks

BlockTable
    ↓
logical position → (physical_block, offset)
```

M3.4 adds:

```text
logical sequence
      ↓
BlockTable
      ↓
physical blocks
      ↓
gather K,V
      ↓
temporary contiguous K,V
      ↓
attention
```

For a sequence of length TT and block size B=16B=16:

b=⌊p16⌋b = \left\lfloor\frac{p}{16}\right\rfloor o=p mod 16o = p\bmod16

and:

K[p]=Kpool[BlockTable[b],o]K[p] = K_{\text{pool}}[\text{BlockTable}[b],o] V[p]=Vpool[BlockTable[b],o].V[p] = V_{\text{pool}}[\text{BlockTable}[b],o].

The crucial thing is that **logical order determines the gathered order**, not physical block order.

---

# Step 1 — Inspect your current `BlockPool`

Before writing `paged_kv_cache.py`, we need to match your actual tensor shapes.

Run:

```bash
sed -n '1,260p' engine/block_pool.py
```

Send me that file.

I don't want to guess the dimensions because your `BlockPool` already has:

```python
num_layers
num_kv_heads
head_dim
dtype
device
```

so we need to build M3.4 around the exact storage layout you implemented in M3.2.

---

# What `paged_kv_cache.py` will eventually do

Conceptually, we'll create something like:

```python
class PagedKVCache:
    def __init__(
        self,
        block_pool: BlockPool,
        block_table: BlockTable,
    ):
        ...
```

Then:

```python
def gather_kv_for_sequence(
    self,
    seq_id: str,
    layer: int,
    length: int,
):
    ...
```

The first implementation can literally do:

```python
for position in range(length):
    block_id, offset = self.block_table.translate(
        seq_id,
        position,
    )

    # read K/V from physical block
```

and construct contiguous tensors.

That's intentionally not optimized.

---

# Example

Suppose:

```text
block_size = 16
```

and:

```text
r1 → physical blocks [7, 2, 5]
```

Then logically:

```text
positions:

0 ... 15    → physical block 7
16 ... 31   → physical block 2
32 ... 47   → physical block 5
```

If:

```text
length = 40
```

the gather operation must produce:

```text
K_gathered:

K[0]  ← block 7, offset 0
K[1]  ← block 7, offset 1
...
K[15] ← block 7, offset 15

K[16] ← block 2, offset 0
...
K[31] ← block 2, offset 15

K[32] ← block 5, offset 0
...
K[39] ← block 5, offset 7
```

So the resulting tensor has exactly the same **logical sequence ordering** as a contiguous KV cache.

---

# The most important test

We'll construct deterministic KV data.

For example, conceptually:

```text
logical position 0  → identifiable K/V values
logical position 1  → identifiable K/V values
...
logical position 39 → identifiable K/V values
```

Write those values through the paged cache.

Then:

```python
paged_k, paged_v = ...
```

must satisfy:

```python
torch.testing.assert_close(
    paged_k,
    contiguous_k,
)

torch.testing.assert_close(
    paged_v,
    contiguous_v,
)
```

This is the fundamental M3.4 invariant:

Kpaged=KcontiguousK_{\text{paged}} = K_{\text{contiguous}} Vpaged=Vcontiguous.V_{\text{paged}} = V_{\text{contiguous}}.

---

# Then decode correctness

After gather itself is correct, we'll connect it to attention.

For the same query qtq_t:

### Contiguous path

Ot=Attention⁡(qt,K0:t,V0:t)O_t = \operatorname{Attention} \left( q_t, K_{0:t}, V_{0:t} \right)

### Paged path

Ot=Attention⁡(qt,Gather⁡(Kpaged,0:t),Gather⁡(Vpaged,0:t)).O_t = \operatorname{Attention} \left( q_t, \operatorname{Gather}(K_{\text{paged}},0:t), \operatorname{Gather}(V_{\text{paged}},0:t) \right).

They should produce the same result within numerical tolerance:

```python
torch.testing.assert_close(
    paged_output,
    contiguous_output,
    rtol=...,
    atol=...,
)
```

Then we'll test **actual generation**, not just synthetic tensors.

---

## M3.4 implementation order

We'll follow exactly this sequence:

```text
1. Inspect BlockPool
       ↓
2. Implement PagedKVCache
       ↓
3. Implement gather_kv_for_sequence()
       ↓
4. Deterministic K/V gather test
       ↓
5. Multi-block test
       ↓
6. Offset/boundary tests
       ↓
7. Compare paged attention vs contiguous attention
       ↓
8. Connect paged cache to decode
       ↓
9. Compare generated tokens
       ↓
10. M3.4 validation
```

**Send me `engine/block_pool.py` first.** Then we'll write `engine/paged_kv_cache.py` against your actual implementation rather than inventing an incompatible interface.




---


next:


Exactly. Looking at your test file, **you've already completed the gather + attention correctness part of M3.4**.

Your tests cover:

|M3.4 requirement|Your test|
|---|---|
|Write K/V|`test_write_and_gather_single_position`|
|Gather K/V|same|
|Multi-block sequence|`test_multi_block_gather_preserves_logical_order`|
|Non-contiguous physical blocks|`test_non_contiguous_physical_blocks_gather_correctly`|
|Block boundary / offsets|`test_gather_across_block_boundaries`|
|Paged vs contiguous K/V|`test_paged_attention_matches_contiguous_attention`|
|Attention correctness|same|

So **don't write more synthetic gather tests just for the sake of it.**

### What remains in M3.4

The task specifically says:

> Modify decode path to read paged K,V.

That's the important remaining part.

Right now your actual model path is:

```text
ModelAdapter
    ↓
CachedQwen2Model
    ↓
CachedQwen2Attention
    ↓
KVCache
    ↓
read_prefix()
```

Your M3.4 path needs to become conceptually:

```text
ModelAdapter
    ↓
CachedQwen2Model
    ↓
CachedQwen2Attention
    ↓
PagedKVCache
    ↓
BlockTable
    ↓
BlockPool
    ↓
gather K,V
    ↓
attention
```

And your existing `CachedQwen2Attention` currently has this:

```python
cached_key, cached_value = cache.read_prefix(
    layer=self.layer_idx,
    length=total_length,
)
```

That's the **old contiguous `KVCache` interface**.

For the paged implementation, we need to replace that with something equivalent to:

```python
cached_key, cached_value = paged_cache.gather_kv_for_sequence(
    seq_id=seq_id,
    layer=self.layer_idx,
    length=total_length,
)
```

But there is an important architectural question:

### `CachedQwen2Attention` currently doesn't know `seq_id`

Your current API is:

```python
forward(
    hidden_states,
    position_embeddings,
    cache,
    position,
)
```

whereas paged KV requires:

```text
seq_id
   ↓
BlockTable
   ↓
physical blocks
```

So before changing the model code, we need to decide **how the sequence ID is passed through the decode path**.

I don't want you to randomly modify `model_adapter.py`, `qwen2_cached.py`, and the generation code.

**Send me `engine/generation.py` next.**

That's where we'll trace the current single-sequence cached generation path and make the smallest clean change to support the paged cache.