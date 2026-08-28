Yes — **M2.4 is the next milestone**, and this one is more substantial than M2.3 because we're changing the actual decode execution from **one request at a time** to **multiple requests in one tensor operation**.

The important thing is: **don't start by rewriting the whole model.** We should first make the data flow correct.

### What M2.4 means in your current engine

Right now M2.3 effectively does:

```text
active requests:
    r1 → decode(r1)
    r2 → decode(r2)
    r3 → decode(r3)
```

Even though the scheduler is continuous, you're still calling the model separately for each request.

M2.4 wants:

```text
active requests:
    r1
    r2
    r3
      ↓
batch builder
      ↓
last_tokens = [token_r1, token_r2, token_r3]
positions   = [pos_r1, pos_r2, pos_r3]
      ↓
one batched decode
```

The critical issue is that:

```text
r1 position = 10
r2 position = 17
r3 position = 6
```

There is **no single global `position`**.

---

## First thing we should inspect

Your current `CachedQwen2Model.forward()` accepts:

```python
def forward(
    self,
    input_ids,
    cache,
    position: int
):
```

and your attention does:

```python
total_length = position + T
```

and:

```python
query_positions = torch.arange(
    position,
    position + T,
    ...
)
```

That design assumes **one position for the whole batch**.

So before writing `batch_builder.py`, we need to change the model interface to support:

```text
positions = [p1, p2, p3, ...]
```

rather than:

```text
position = p
```

But there is an even more important issue:

### Your current `KVCache` is not batch-aware

It currently allocates:

```python
[1, H_kv, T_max, D]
```

So each request owns its own `KVCache` object.

That's actually fine for M2.4 **if we keep separate cache handles**.

We do **not** need to implement a sophisticated shared paged cache yet.

We can initially have:

```text
r1 → KVCache_1
r2 → KVCache_2
r3 → KVCache_3
```

and the ragged batch operation gathers the appropriate K/V from each cache.

Conceptually:

```text
                 ┌── KVCache r1 ── prefix length 10
r1 last token ───┤
                 │
                 ├── KVCache r2 ── prefix length 17
r2 last token ───┤
                 │
                 └── KVCache r3 ── prefix length 6
r3 last token ──┘
```

No cache data should ever be mixed between requests.

---

# The implementation order I recommend

### Step 1 — `batch_builder.py`

Create something like:

```python
@dataclass
class DecodeBatch:
    input_ids: torch.Tensor
    positions: torch.Tensor
    requests: list[RequestState]
```

For active requests:

```text
r1.generated_ids[-1] = 42
r1.current_pos = 10

r2.generated_ids[-1] = 81
r2.current_pos = 17

r3.generated_ids[-1] = 12
r3.current_pos = 6
```

the builder should produce:

```python
input_ids.shape == [3, 1]

positions.shape == [3]
```

with:

```text
input_ids =
[
    [42],
    [81],
    [12],
]

positions =
[10, 17, 6]
```

This is the first concrete thing I'd implement.

---

### Step 2 — test the builder

Before touching attention, test:

```python
assert batch.input_ids.shape == (3, 1)

assert batch.positions.tolist() == [
    r1.current_pos,
    r2.current_pos,
    r3.current_pos,
]
```

Also verify that request ordering is preserved.

---

### Step 3 — modify the model to accept per-request positions

Currently:

```python
position: int
```

becomes conceptually:

```python
positions: torch.Tensor
```

with:

[  
\mathbf p \in \mathbb{N}^{B}  
]

For example:

[  
\mathbf p =  
\begin{bmatrix}  
10\  
17\  
6  
\end{bmatrix}  
]

Then the RoPE positions need to correspond to each batch element.

---

### Step 4 — solve the cache read

This is the most important part.

For request (i):

[  
K_i \in \mathbb{R}^{H_{kv}\times L_i\times D}  
]

where (L_i) is that request's valid prefix length.

So if:

[  
L_1=10,\quad L_2=17,\quad L_3=6  
]

you **cannot simply concatenate the cache as if they all had length 17** without a mask.

We need a representation where each request only attends to:

```text
r1 → K[0:10]
r2 → K[0:17]
r3 → K[0:6]
```

That is the core of M2.4.

---

# One important point

Don't try to make the entire attention implementation "fully optimized" now.

For M2.4, correctness is the goal.

A perfectly acceptable first implementation is something conceptually like:

```text
for each request:
    retrieve its valid K/V prefix
    compute its attention
stack the outputs
```

Even if internally that still contains some Python looping.

The **external decode interface** becomes ragged/batched, and the correctness test proves that the outputs are equivalent.

Later, optimization can remove those loops.

---

# The most important test

Eventually we want:

```text
Independent generation:

r1 → token sequence A
r2 → token sequence B
r3 → token sequence C
```

versus:

```text
Ragged continuous batch:

[r1, r2, r3]
       ↓
batched decode
       ↓
A, B, C
```

and verify:

```python
batched_output == independent_output
```

for deterministic greedy decoding.

That is the **real M2.4 unlock test**.

---

## So don't implement the whole thing yet

Start with exactly:

```text
engine/batch_builder.py
```

and make it produce:

[  
\text{input_ids}\in\mathbb{N}^{B\times1}  
]

and

[  
\text{positions}\in\mathbb{N}^{B}  
]

from `list[RequestState]`.

Then write the builder test.

**That's the first step of M2.4.**