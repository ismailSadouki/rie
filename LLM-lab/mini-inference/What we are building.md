# Mini Inference Engine — Project Direction

Yes. **This is a strong project to start now**, and the structure you have is much better than simply “build a mini vLLM.” The important part is that you're building an **inference engineering evidence chain**, not just implementing components.

The one thing I would change before you touch code is the **execution contract**.

## 1. The central principle

Keep this rule absolutely rigid:

> **No benchmark number exists without a workload specification.**

For every result, you should be able to answer:

$$  
\mathcal W =  
(\text{hardware},\text{model},\text{dtype},L_p,L_o,C,\text{sampling},\text{batch policy},\text{warmup},N)  
$$

where:

- $L_p$ = prompt-length distribution
    
- $L_o$ = output-length distribution
    
- $C$ = concurrency
    
- $N$ = number of requests
    
- hardware = GPU + VRAM + driver/CUDA
    
- model = exact checkpoint + parameter count
    
- dtype = FP32/FP16/BF16/etc.
    
- sampling = temperature, top-p, max tokens, etc.
    

Then your numbers have meaning.

For example:

# $$  
\mathrm{TTFT}

## t_{\text{first output token}}

t_{\text{request arrival}}  
$$

# $$  
\mathrm{ITL}_i

t_i-t_{i-1}  
$$

and

# $$  
\mathrm{throughput}

\frac{\text{generated tokens}}  
{\text{wall-clock time}}  
$$

But:

> “My engine generates 42 tok/s”

is basically meaningless without the workload.

That's exactly the mindset that makes this project valuable.

---

# 2. Don't start with M1.1 blindly

I'd make **M0 slightly more concrete before M1.1**.

Your repository should roughly become:

```text
mini-inference-engine/
├── engine/
│   ├── __init__.py
│   ├── model_adapter.py
│   ├── request.py
│   ├── generation.py
│   ├── kv_cache.py
│   ├── scheduler.py
│   ├── block_pool.py
│   ├── block_table.py
│   └── attention.py
│
├── bench/
│   ├── workloads.py
│   ├── generator.py
│   ├── metrics.py
│   ├── runner.py
│   └── plots.py
│
├── tests/
│   ├── test_generation.py
│   ├── test_kv_cache.py
│   ├── test_scheduler.py
│   ├── test_block_pool.py
│   ├── test_block_table.py
│   └── test_paged_attention.py
│
├── serve/
│   ├── fastapi_app.py
│   └── locustfile.py
│
├── configs/
│   ├── workload_single.yaml
│   ├── workload_batch.yaml
│   └── workload_saturation.yaml
│
├── experiments/
│   └── results/
│
├── docs/
│   ├── architecture.md
│   ├── inference_glossary.md
│   └── kv_cache_bug_playbook.md
│
├── scripts/
│   ├── benchmark.py
│   └── plot_results.py
│
├── pyproject.toml
└── README.md
```

Don't overengineer this. The directory structure exists to make the evidence easy to collect.

---

# 3. Your first workload spec

Before M1.1, define **one canonical workload**.

I'd recommend starting with a deliberately simple workload:

### Workload S1 — single-request generation

```text
Hardware:
    RTX 3050 Laptop
    4 GB VRAM

Model:
    [your exact P1 checkpoint]

Dtype:
    float16

Concurrency:
    1

Prompt lengths:
    fixed / explicitly recorded

Output length:
    fixed max_new_tokens

Sampling:
    deterministic
    temperature = 0
    fixed seed

Warmup:
    e.g. 5 requests

Measured requests:
    e.g. 20

Metrics:
    TTFT
    prefill latency
    decode latency
    ITL
    total generation latency
    generated tokens/sec
```

The exact values can change depending on your P1 model and available VRAM.

The important thing is that **the workload is a first-class artifact**.

For example:

```text
configs/workload_single.yaml
```

Then every experiment references it.

That prevents this future disaster:

> “I changed max sequence length, batch size, model dtype and sampling while benchmarking and forgot.”

---

# 4. The conceptual progression is excellent

Your M1 → M4 progression has a very nice engineering logic:

$$  
\boxed{\text{single request}}  
$$

↓

$$  
\boxed{\text{KV cache}}  
$$

↓

$$  
\boxed{\text{multiple requests}}  
$$

↓

$$  
\boxed{\text{continuous batching}}  
$$

↓

$$  
\boxed{\text{virtualized KV memory}}  
$$

↓

$$  
\boxed{\text{paged KV}}  
$$

↓

$$  
\boxed{\text{benchmark}}  
$$

Then:

$$  
\boxed{\text{mini engine}}  
\rightarrow  
\boxed{\text{vLLM}}  
$$

That's much better than starting by reading thousands of lines of vLLM code.

You're effectively constructing the concepts yourself first.

---

# 5. Pay particular attention to M1.2–M1.4

This is probably the most important technical section of the whole project.

For a decoder-only Transformer, suppose:

$$  
X\in\mathbb R^{B\times T\times d_{\text{model}}}  
$$

For layer $l$:

$$  
K_l,V_l  
\in  
\mathbb R^{B\times H\times T\times d_h}  
$$

where:

$$  
d_{\text{model}}=H d_h  
$$

During **prefill**, you process:

$$  
x_{1:T}  
$$

and generate the KV states for all $T$ positions.

During **decode**, you only process:

$$  
x_{T+1}  
$$

and append:

$$  
K_{T+1},V_{T+1}  
$$

to the cache.

So instead of recomputing:

$$  
K(x_{1:T}),V(x_{1:T})  
$$

at every generation step, you reuse:

$$  
K_{1:T},V_{1:T}  
$$

and compute only the new token's projections.

That is the mechanism you should make completely explicit in your implementation.

---

# 6. Your most valuable correctness test

M1.4 shouldn't merely be:

> cached output == uncached output

Make the invariant precise.

For a deterministic generation configuration and prompt $x$:

# $$  
y^{\text{cached}}_{1:T}

y^{\text{uncached}}_{1:T}  
$$

token-by-token.

Even better, test the logits:

## $$  
\left|  
z_t^{\text{cached}}

z_t^{\text{uncached}}  
\right|_\infty  
<\epsilon  
$$

for each decode step $t$, where

$$  
z_t\in\mathbb R^{|V|}  
$$

and $V$ is the vocabulary.

Then separately test:

```text
same tokens
same logits within tolerance
same EOS behavior
same generated length
```

This will make your KV-cache implementation much more defensible.

---

# 7. Continuous batching is where the project becomes interesting

The conceptual shift is:

### Static batching

Suppose:

```text
request A → 100 tokens
request B → 20 tokens
request C → 50 tokens
```

A static batch essentially keeps the batch together until the batch completes.

So after B finishes, its GPU capacity is potentially sitting idle.

### Continuous batching

At decode step $t$:

```text
active requests
       ↓
decode one token
       ↓
finished?
  ↙       ↘
yes       no
 ↓         ↓
evict    continue
 ↓
admit waiting request
```

This is the mechanism you want to demonstrate experimentally.

Don't just implement it.

**Show why it matters.**

---

# 8. Paged KV is probably your strongest portfolio component

Your M3 section is excellent.

The conceptual transformation is:

### Contiguous

For request $i$:

$$  
K_i,V_i  
\rightarrow  
\text{one contiguous allocation}  
$$

with capacity potentially reserved for:

$$  
T_{\max}  
$$

even if the request only uses $T_i$.

### Paged

Split KV memory into blocks of $B$ tokens.

For request $i$:

$$  
\text{logical block }j  
\rightarrow  
\text{physical block }p  
$$

through:

$$  
\text{block_table}_i[j]=p  
$$

Then a logical sequence:

$$  
[0,1,2,\ldots]  
$$

might map to:

$$  
[7,2,13,4,\ldots]  
$$

in physical memory.

That is the important abstraction.

The sequence doesn't care where its KV lives physically.

---

# 9. Be careful with one thing in M3.4

Don't accidentally turn M3.4 into:

> “I implemented PagedAttention exactly like the paper/vLLM.”

You probably won't.

Instead claim:

> **A simplified paged-KV attention implementation demonstrating logical-to-physical KV block translation and non-contiguous KV gathering.**

That's completely legitimate.

Then later:

$$  
\text{mini implementation}  
\leftrightarrow  
\text{vLLM implementation}  
$$

and explain the differences.

That honesty actually makes the project stronger.

---

# 10. M4 should become your experiment layer

I'd make the benchmark architecture extremely disciplined.

Every result should effectively be:

```text
Experiment
    ↓
Workload config
    ↓
Engine config
    ↓
Run
    ↓
Raw measurements
    ↓
Aggregated metrics
    ↓
Plot/table
```

For example:

```text
experiments/
└── 2026-08-XX-static-vs-continuous/
    ├── workload.yaml
    ├── engine.yaml
    ├── raw.jsonl
    ├── summary.json
    └── plots/
```

Then you can reproduce the result months later.

---

# 11. Don't obsess over absolute performance

This is important given your hardware.

Your RTX 3050 is not going to compete with a production vLLM deployment on an A100/H100.

**That's irrelevant.**

Your question is:

$$  
\text{What mechanism caused what behavior?}  
$$

For example:

$$  
\text{continuous batching}  
\rightarrow  
\uparrow \text{GPU utilization}  
\rightarrow  
\uparrow \text{throughput}  
$$

while potentially:

$$  
\uparrow \text{concurrency}  
\rightarrow  
\uparrow \text{queueing}  
\rightarrow  
\uparrow \text{TTFT}_{p95}  
$$

That's an engineering result independent of having expensive hardware.

---

# 12. Your vLLM section should be investigative, not just deployment

This part:

> Read vLLM scheduler and cache engine after building mini versions

is **exactly right**.

Don't read vLLM deeply before the mini engine.

Afterward you'll be able to ask:

> “I solved this problem this way. How does vLLM solve it?”

Then build a mapping:

|Mini engine|vLLM concept|
|---|---|
|request state|request/sequence state|
|FCFS scheduler|scheduler|
|KV block pool|KV cache manager|
|block table|block mapping|
|token admission|scheduling|
|decode loop|model execution|
|benchmark runner|production metrics/load testing|

And then:

> Why isn't my engine anywhere near vLLM's performance?

That's where your **gap decomposition** becomes interesting.

---

# 13. Decompose the vLLM gap

Don't write:

> “vLLM is 5× faster.”

Instead:

$$  
\frac{T_{\text{mini}}}{T_{\text{vLLM}}}  
$$

should be investigated through things such as:

# $$  
\Delta T

\Delta T_{\text{kernels}}  
+  
\Delta T_{\text{CUDA graphs}}  
+  
\Delta T_{\text{scheduler}}  
+  
\Delta T_{\text{memory}}  
+  
\Delta T_{\text{framework}}  
+  
\Delta T_{\text{other}}  
$$

You won't necessarily be able to measure each term independently.

That's okay.

You can categorize the observed gap and clearly distinguish:

- measured
    
- inferred
    
- unknown
    

That is much more professional than inventing a precise attribution.

---

# 14. Quantization is a separate scientific experiment

M6 should not contaminate the inference-engine benchmark.

Have:

### Serving benchmark

$$  
\text{same model}  
+  
\text{same workload}  
+  
\text{different serving systems}  
$$

and:

### Quantization experiment

$$  
\text{same model}  
+  
\text{same evaluation}  
+  
\text{different quantization}  
$$

Then compare:

$$  
\text{quality}  
\leftrightarrow  
\text{memory}  
\leftrightarrow  
\text{latency}  
$$

For example:

|Model|Precision|Quality|VRAM|TTFT|ITL|
|---|---|--:|--:|--:|--:|
|DPO|BF16|...|...|...|...|
|DPO|GPTQ|...|...|...|...|
|DPO|AWQ|...|...|...|...|

Don't assume:

> quantization = faster

because depending on hardware/kernel support, quantization can reduce memory while not necessarily producing proportional latency improvements.

That itself could become a good result.

---

# 15. The resume story becomes very strong

The real story isn't:

> “I built a mini inference engine.”

It's:

> **I implemented the core mechanisms behind modern LLM serving, validated their correctness, measured their effects under controlled workloads, then compared my implementation against vLLM and deployed a quantized model in production-style serving.**

That's a substantially stronger engineering narrative.

And it connects your previous work:

$$  
\text{P1 model}  
\rightarrow  
\text{inference}  
\rightarrow  
\text{P2 DPO model}  
\rightarrow  
\text{AlgerianMMLU/P6}  
\rightarrow  
\text{quantized serving}  
$$

So your projects aren't isolated anymore.

---

## What I would do **today**

Don't start implementing KV cache yet.

Your first session should be:

### M0

1. Create the repo.
    
2. Create the directory structure.
    
3. Write the workload contract.
    
4. Record your hardware/software environment.
    
5. Choose the exact P1 model.
    
6. Define the first workload.
    
7. Define the metric formulas.
    
8. Define the experiment-result format.
    
9. Add a minimal test framework.
    
10. Commit.
    

Then start:

**M1.1 → M1.2 → M1.3.**

And one discipline I'd strongly recommend:

> **Every milestone produces an artifact.**

Not just code.

For example:

```text
M1.3
code
+
unit test
+
cache shape documentation
```

```text
M1.4
code
+
20-prompt correctness test
+
result
```

```text
M1.5
benchmark
+
raw data
+
plot
+
workload spec
```

By the time you reach M4, you won't merely have a GitHub repository full of code.

You'll have an **evidence trail** showing that you understand why each inference mechanism exists and what it actually changes.