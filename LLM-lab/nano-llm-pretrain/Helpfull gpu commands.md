
There is no formula that tells you exactly. The standard approach is to **measure it experimentally**, because memory usage depends on:

- model size (parameters)
    
- sequence length (`block_size`)
    
- precision (FP32, BF16, FP16)
    
- optimizer (AdamW stores extra states)
    
- activation checkpointing
    
- Flash Attention or standard attention
    
- whether you're training or doing inference
    

For LLM training, **activations** are usually what limit the batch size, not the parameters.

## The practical method

Start small and increase until you get an out-of-memory (OOM) error.

For example:

```text
batch_size = 1   ✅
batch_size = 2   ✅
batch_size = 4   ✅
batch_size = 8   ✅
batch_size = 16  ❌ OOM
```

Then your maximum usable batch size is around **8** (perhaps 10 or 12 depending on memory fragmentation).

---

## Monitor GPU memory

While training, run:

```bash
watch -n 0.5 nvidia-smi
```

or

```bash
nvidia-smi
```

You'll see something like:

```text
GPU Memory Usage

7800 MiB / 8192 MiB
```

If you're close to the limit, increasing the batch size will likely cause an OOM.

---

## You can also inspect memory in PyTorch

After a forward/backward pass:

```python
print(torch.cuda.max_memory_allocated() / 1024**3, "GB")
```

or

```python
print(torch.cuda.memory_summary())
```

---

## Why doesn't memory scale linearly?

Suppose:

- (B) = batch size
    
- (S) = sequence length
    
- (L) = number of layers
    
- (d) = model dimension
    

The activation memory roughly scales like:

[  
O(B \times S \times L \times d)  
]

So doubling the batch size approximately doubles the activation memory.

---

## Your RTX 3050 (4 GB)

For a GPT/Llama model around **100–150M parameters** with:

- `block_size = 1024`
    
- FP16/BF16 training
    

don't expect very large micro-batches. A micro-batch between **1 and 8** is common, and you would use **gradient accumulation** to reach a larger effective batch size.

---

### Rule of thumb

When building a new model:

1. Fix the sequence length.
    
2. Fix the precision (FP16/BF16/FP32).
    
3. Increase `batch_size` until you hit OOM.
    
4. Back off by one step (or leave a small memory margin).
    

That's the batch size you use. If you need a larger effective batch, increase **gradient accumulation**, not the micro-batch size.