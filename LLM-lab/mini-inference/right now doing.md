
For **M1.1**, your goal is to build the **contract layer**, not KV cache, batching, or optimization yet.

## M1.1 — What you should do

### 1. Create `engine/types.py`

Define four dataclasses.

#### `SamplingConfig`

It should explicitly contain:

```text
greedy
temperature
top_k
top_p
seed
```

Conceptually:

```text
SamplingConfig
├── temperature
├── top_k
├── top_p
├── seed
└── greedy
```

Don't hide these inside the generation function.

---

### 2. Define `GenerationRequest`

This represents **what the user asks the engine to generate**.

It should contain:

```text
GenerationRequest
├── request_id
├── prompt_text OR prompt_ids
├── max_new_tokens
├── sampling_config
└── arrival_time
```

Think:

# [  
\text{GenerationRequest}

\text{input}+\text{generation constraints}+\text{metadata}  
]

The important part is `request_id`.

Later, when 32 requests are active simultaneously, the scheduler needs to know:

```text
request_17 → these tokens
request_18 → those tokens
request_19 → those KV blocks
```

---

### 3. Define `RequestState`

This represents **what is happening to a request while the engine processes it**.

Include:

```text
RequestState
├── prompt_ids
├── generated_ids
├── position
├── finished
├── arrival_time
├── prefill_start_time
├── first_token_time
├── finish_time
└── cache_handle
```

You don't need to use every timestamp yet.

You're creating the structure that later allows you to calculate:

[  
TTFT = t_{\text{first token}} - t_{\text{arrival}}  
]

and:

[  
ITL_i = t_i - t_{i-1}  
]

The `cache_handle` can initially be `None`, because **M1.1 has no KV cache yet**.

---

### 4. Define `GenerationOutput`

This represents **what the engine returns**.

Include:

```text
GenerationOutput
├── request_id
├── output_ids
├── text
├── ttft
├── itl
└── finish_reason
```

For now, `ttft` and `itl` can be `None` because you aren't measuring them yet.

Later:

```text
ttft = 0.183
itl = [0.041, 0.039, 0.040, ...]
```

This is important because your output object will eventually become the bridge between **generation and benchmarking**.

---

# 5. Create `engine/model_adapter.py`

The scheduler should **not know anything about the internal P1 model**.

Instead:

```text
Scheduler
     |
     v
ModelAdapter
     |
     v
P1 model
```

Your adapter should expose approximately:

```text
ModelAdapter
├── tokenize()
├── decode()
├── forward_no_cache()
└── sample_next_token()
```

### `tokenize()`

Input:

```text
prompt text
```

Output:

[  
\text{prompt_ids} \in \mathbb{N}^{T}  
]

where $T$ is the number of prompt tokens.

### `decode()`

Input:

[  
\text{token_ids} \in \mathbb{N}^{T}  
]

Output:

```text
string
```

### `forward_no_cache()`

This is the important one for M1.1.

Given token IDs:

[  
X \in \mathbb{N}^{B\times T}  
]

run the model **without KV caching** and return logits:

[  
L \in \mathbb{R}^{B\times T\times V}  
]

where:

- $B$ = batch size
    
- $T$ = sequence length
    
- $V$ = vocabulary size
    

You only need the logits for the final position for next-token generation:

[  
L[:,T-1,:]\in\mathbb{R}^{B\times V}  
]

---

# 6. Implement `sample_next_token()`

This function receives next-token logits:

[  
z\in\mathbb{R}^{V}  
]

and applies your explicit `SamplingConfig`.

For **greedy decoding**:

[  
x_{t+1} = \arg\max_{v\in{1,\dots,V}} z_v  
]

For now, your smoke test should use:

```text
temperature = 0
greedy = True
```

Later you will implement:

```text
temperature
top-k
top-p
```

Don't prematurely mix sampling logic with the model itself.

---

# 7. Build the no-cache generation loop

For M1.1, intentionally use the simplest possible generation algorithm:

```text
prompt
  ↓
tokenize
  ↓
input_ids
  ↓
forward_no_cache()
  ↓
sample next token
  ↓
append token
  ↓
forward_no_cache()
  ↓
sample next token
  ↓
...
```

So if the prompt is:

```text
"Hello"
```

and `max_new_tokens = 10`:

```text
Hello
  ↓
model(prompt)
  ↓
token 1
  ↓
model(prompt + token 1)
  ↓
token 2
  ↓
...
```

This is deliberately inefficient.

**That's fine.**

You're establishing correctness before introducing the KV cache in M1.3.

---

# 8. Write `tests/test_single_generation.py`

Your first test should prove:

> The adapter can take one prompt and deterministically generate tokens without a KV cache.

Use something like:

```text
prompt = "The capital of France is"
max_new_tokens = 10
greedy = True
seed = 42
```

You don't need to test performance.

You want:

```text
model loads
        ↓
tokenizer works
        ↓
prompt tokenizes
        ↓
forward works
        ↓
next token sampled
        ↓
generation terminates
        ↓
output decodes
```

---

# 9. What you should NOT implement yet

Do **not** start:

```text
❌ KV cache
❌ continuous batching
❌ scheduler
❌ paged attention
❌ block tables
❌ benchmarking
❌ Locust
❌ vLLM
```

M1.1 is intentionally small.

The dependency chain is:

```text
M1.1
Request + State + Output
        ↓
Model Adapter
        ↓
No-cache generation
        ↓
M1.2
Prefill / Decode separation
        ↓
M1.3
KV Cache
        ↓
M2
Scheduler + batching
```

---

# 10. Your M1.1 unlock gate

Don't move to M1.2 until you can honestly check:

```text
[ ] SamplingConfig exists
[ ] GenerationRequest exists
[ ] RequestState exists
[ ] GenerationOutput exists

[ ] ModelAdapter exists
[ ] ModelAdapter can tokenize
[ ] ModelAdapter can decode
[ ] ModelAdapter can run forward_no_cache()
[ ] ModelAdapter can sample the next token

[ ] One prompt generates successfully
[ ] Generation is deterministic with greedy decoding
[ ] No-cache generation test passes

[ ] Request IDs exist
[ ] Arrival timestamp exists
[ ] Output can store TTFT
[ ] Output can store ITL
```

The key idea of M1.1 is:

> **The scheduler should eventually operate on requests and states, not on model internals.**

Once this contract is stable, **M1.2 can cleanly split the generation process into prefill and decode.**