
# Own Inference: Mini Engine + vLLM

Two tracks. First, build a **mini inference engine from scratch** — KV cache → continuous batching → paged KV memory → a TTFT/ITL/throughput benchmark harness. Then run the **production reality**: deploy a quantized 7–8B model with vLLM behind FastAPI, load-test it, and quantify your engine's gap honestly. Inference is where the money is made — this is the resume centerpiece that gets you inference-team interviews.

Flagship · EssentialMonth 2 · Weeks 3–4 / 7–8colab T43050Resume centerpiece

**⛓ This is the capstone that consumes all four prior projects.** The **Project 1 model + KV cache** is what your mini engine wraps — you already own a correct generation loop to build batching on top of. The **Project 4 fused RMSNorm/RoPE kernels** drop into the engine's forward pass and their roofline mindset transfers directly to inference bottleneck analysis. The **vLLM block-table notes from Project 4's M5** are your on-ramp here — you already read the attention backend. And you serve your **Project 2 DPO Darija model**, evaluated on your **Project 6 AlgerianMMLU harness** to prove quantization didn't wreck quality. Every prior project pays off here.

**Strategic framing — the mini engine is the differentiator; vLLM is the credibility.** Almost no junior can build KV cache → continuous batching → paged memory from scratch. That's the artifact that makes an inference-team recruiter stop scrolling. But building your own engine without ever touching vLLM makes you look naive — so you pair it: build the mini engine to prove you _understand_ the mechanisms, then benchmark against vLLM and **explain the gap honestly** to prove you know where production wins. Two rules: (1) **build the mini engine first, vLLM second** — you can't appreciate what vLLM does until you've felt the pain it solves; (2) **define your workload before you quote any number** — "4× faster" is meaningless without prompt-length distribution, batch size, and hardware. A latency claim without a workload spec is a red flag to anyone senior.

PART 1

## Engineering Breakdown

6 milestones · KV cache → batching → paged memory → benchmark → vLLM → quantize+serve

# 1 KV Cache — The Foundation

3050W7-a

Objective

Take Project 1's model and make single-sequence generation efficient with a KV cache. Understand exactly why prefill and decode are fundamentally different phases.

```
# The two phases that govern everything in inference PREFILL : process the whole prompt at once → COMPUTE-bound (big matmuls, parallel) DECODE : generate 1 token at a time, reusing cached K,V → MEMORY-BANDWIDTH-bound (tiny matmul, but you re-read the whole KV cache every step) # This asymmetry is why TTFT and ITL are separate metrics.```
```

Deliverables

- KV cache reused from/extended out of P1's model — store K,V per layer, append each decode step
- Clean separation of prefill (full prompt) vs decode (one token) code paths
- Correctness: cached generation must be **bit-identical** to full-recompute generation (a unit test)
- Sampling reused from P1: temperature, top-p, top-k, greedy

Skills learned

KV cache mechanicsprefill vs decodecache append/indexingcausal masking with cachememory-bandwidth intuition

Common mistakes

- Position IDs / RoPE offsets wrong after the first decode step → garbage after token 1 (the classic KV-cache bug)
- Re-processing the whole sequence each step "with a cache" → no speedup, defeats the purpose
- Cache not correctly masked → attends to future/padding positions
- Not testing cached == uncached → shipping a subtly wrong cache

Stretch goals

- Measure the prefill-vs-decode FLOP/byte ratio empirically to see the compute→bandwidth shift

**OS integration:** Project `mini-inference-engine`. First entry: a **Note** — "prefill is compute-bound, decode is bandwidth-bound." This framing is your best Architecture Explanation hook.

so **Objective:** Make generation incremental — compute each new token's attention against cached keys/values instead of recomputing the whole prefix.

#### Deliverables

- Pre-allocated cache tensors; an `update()` that writes the new token's K,V at the current position
- Correctness: cached generation == cache-free generation, token-for-token, on 20 prompts
- A measured speedup at sequence length 512+ (where recomputation hurts)



# 2 Continuous Batching

3050W7-b

Objective

The single most important throughput idea in modern serving. Instead of waiting for a whole batch to finish (static batching), inject new requests and evict finished ones **at the token level**, keeping the GPU saturated.

# Static vs continuous batching — the throughput unlock STATIC: batch of 8 → all 8 must finish before the next batch starts → short requests wait for the longest one → GPU idles CONTINUOUS: at each decode step, evict finished seqs, admit waiting seqs → GPU always full → 2-4× higher throughput in practice

Deliverables

- A scheduler loop: a running batch of active sequences, a waiting queue, per-step admit/evict logic
- Ragged batch handling: sequences at different lengths decoded together (attention masking per sequence)
- A simple admission policy (FCFS is fine) + max-batch-size cap
- Throughput comparison: static batching vs continuous batching at the same request load

Skills learned

continuous/in-flight batchingrequest schedulingragged batch attentionadmit/evict logicGPU saturationqueue management

Common mistakes

- Padding all sequences to max length → wastes compute, negates the point of continuous batching
- Off-by-one when a sequence finishes mid-step and a new one is admitted → position/cache desync
- No max-batch cap → OOM when too many long requests pile up
- Confusing continuous batching with dynamic batching (batching at request arrival, not token level)

Stretch goals

- Add a simple priority / longest-prompt-first policy and measure the TTFT vs throughput trade-off

**OS integration:** `EXP-2026-014 — static vs continuous batching throughput`. The "why static batching wastes the GPU" diagram is prime content.

**Objective:** Stop idling the GPU while finished requests wait for the slowest one in a static batch.

#### Deliverables

- A request queue + a step loop that packs whatever is active each iteration
- Per-request position tracking (each sequence is at a different decode step)
- Throughput comparison: static vs continuous at a fixed arrival rate




# 3 Paged KV Memory (block tables)

3050W7-c

Objective

Implement PagedAttention's core idea in miniature: store the KV cache in fixed-size **blocks** (pages) indexed by a **block table**, instead of one contiguous buffer per sequence. This is what kills memory fragmentation and lets you pack far more concurrent sequences.

# PagedAttention: virtual memory for the KV cache Naive: each seq gets a contiguous max-length KV buffer → huge internal fragmentation (reserve for max, use little) Paged: KV stored in fixed blocks (e.g., 16 tokens each) block_table[seq] = [block_7, block_2, block_19, ...] # logical→physical → near-zero waste, blocks allocated on demand, freed on finish

Deliverables

- A block pool (fixed-size KV blocks) + a block-table mapping logical positions → physical blocks per sequence
- Block allocation on growth, block freeing on sequence completion
- Attention that gathers K,V across a sequence's (possibly non-contiguous) blocks
- Memory-utilization comparison: contiguous vs paged (how many more concurrent seqs fit?)

Skills learned

paged KV / block tableslogical→physical mappingblock allocation/freefragmentation eliminationgather-over-blocks attention

Common mistakes

- Block size too large → back to fragmentation; too small → block-table overhead. Pick a sane 16–32.
- Not freeing blocks on sequence completion → a slow "memory leak" that OOMs after many requests
- Gather over blocks with wrong indexing → attends to the wrong KV (silent wrong output)
- Trying to match vLLM's optimized CUDA gather — you're building the _concept_, correctness over speed

Stretch goals

- Prefix/block sharing: two requests with the same system prompt share prefill blocks (this is where vLLM's real wins come from)

**OS integration:** Reuse the **block-table diagram from Project 4's vLLM reading** — now you're implementing what you read. `EXP-2026-015 — paged vs contiguous KV, concurrent-seq capacity`.

**Objective:** Replace the contiguous per-sequence cache with fixed-size physical blocks addressed by a block table — eliminating internal fragmentation.

#### Deliverables

- A block allocator (free list) + a per-sequence block table
- The logical→physical address translation from P4's diagram, implemented
- A memory-waste measurement: contiguous (with max-seq padding) vs paged, at varying active lengths


# 4 Benchmark Harness — The Metrics That Matter

3050W7-d

Objective

Measure the metrics inference teams actually report: **TTFT** (time to first token, dominated by prefill), **ITL** (inter-token latency, dominated by decode), and **throughput** (total tokens/sec across all concurrent requests) — across batch 1 / 8 / 32.
```
# The inference vocabulary you must speak fluently TTFT = latency from request → first output token (prefill cost, UX-critical) ITL / TPOT = time per output token after the first (decode cost) Throughput = total output tokens/sec across all requests (serving efficiency, $$) # The core tension: batch size ↑ → throughput ↑ but per-request latency ↑
```

Deliverables

- A harness that fires N concurrent requests with a defined prompt-length + output-length distribution
- TTFT, ITL (p50/p95), and throughput measured at batch 1 / 8 / 32
- The latency-vs-throughput trade-off curve (the single most important inference chart)
- A clearly stated **workload spec** in the README (hardware, model, dtype, prompt/output lengths) — without this, numbers are worthless

Skills learned

TTFT / ITL / throughputp50/p95 latencyconcurrent load generationlatency-throughput trade-offworkload specification

Common mistakes

- Reporting mean latency only → hides tail; report p95 (tail latency is what users feel)
- Quoting throughput without batch size / prompt length → uninterpretable, uncredible
- No warmup → first-request compile/allocation skews everything
- Measuring TTFT and throughput at the same batch and calling one "the" number — they trade off, show the curve

Stretch goals

- Sweep max-batch-size and plot the throughput ceiling + the latency knee (the "operating point" analysis)

**OS integration:** `EXP-2026-016 — TTFT/ITL/throughput @ batch 1/8/32`. The trade-off curve is your **resume-centerpiece chart**.


**bjective:** Produce the latency/throughput numbers with a stated workload — the only kind that are defensible.

#### Deliverables

- TTFT (time to first token), ITL (inter-token latency), and throughput (tokens/sec) at batch 1/8/32
- Fixed prompt-length distribution and output length, stated in the table
- Warmup + repetition via a proper timer (reuse P3's `cuda_timer` discipline)



# 5 vLLM: Benchmark Against It + Read It + Load Test

colab T4W8-a

Objective

Deploy a quantized 7–8B model with vLLM, load-test it, benchmark **your mini engine vs vLLM**, and explain the gap honestly. The gap IS the learning — vLLM has fused CUDA kernels, better scheduling, CUDA graphs; naming why it's faster proves you understand production.

Deliverables

- vLLM serving a quantized 7–8B model (AWQ/GPTQ) on a single T4, OpenAI-compatible endpoint
- Locust (or equivalent) load-test script: throughput/TTFT under increasing concurrency
- Reading notes on vLLM's `scheduler.py` and `cache_engine.py` / block manager — map them to your M2/M3 code
- Honest mini-engine-vs-vLLM table + a written "why vLLM wins" analysis (kernels, CUDA graphs, scheduling, prefix caching)

Skills learned

vLLM servingLocust load testingconcurrency saturationreading production schedulershonest gap analysisOpenAI-compatible API

Common mistakes

- Comparing your pure-PyTorch engine to vLLM and being demoralized — the point is the _explained gap_, not winning
- Load-testing with a single concurrency level → miss the saturation curve entirely
- Not stating you're benchmarking a small quantized model on a T4 → over-generalizing to "70B on A100s"
- Skimming vLLM source without mapping it to your own code → reading without leverage

Stretch goals

- Find a real bug / perf gap / doc hole in vLLM while reading → an OSS PR (your OS's highest-value output)
- Skim SGLang's RadixAttention (prefix-tree KV sharing) and note how it differs from vLLM's paging

**OS integration:** `EXP-2026-017 — mini engine vs vLLM, T4, defined workload`. This closes the "I built it AND I know why the pros are faster" loop — the exact senior signal. Second **OSS PR** attempt lives here.

# 6 Quantization + Serve (production realism)

colab T4W8-b

Objective

Quantize your Project 2 DPO Darija model (bf16 baseline vs GPTQ vs AWQ), measure the quality-vs-memory trade-off **on your own AlgerianMMLU eval**, and serve it behind FastAPI with token streaming. This is the end-to-end lifecycle in one deliverable.

Deliverables

- Three variants: bf16 (baseline), GPTQ, AWQ — each measured on AlgerianMMLU (quality) + peak VRAM + throughput
- The quality/memory/latency trade-off table (the artifact your roadmap explicitly asks for)
- FastAPI server with Server-Sent-Events / streaming token output
- A stable public demo (HF Space, NOT Colab — your OS correction: Colab is not a deployment)

Skills learned

GPTQ vs AWQquantization quality evalcalibration dataFastAPI streaming (SSE)VRAM budgetingquality-memory trade-off

Common mistakes

- Claiming "quantization is free" without measuring quality → run it on AlgerianMMLU, report the real delta
- Bad calibration set for GPTQ (wrong domain) → worse quality than necessary; calibrate on Darija-relevant text
- Promising a live demo on Colab that dies → HF Space or clearly-labeled on-demand (your OS forbids the Colab-demo trap)
- Streaming that buffers the whole response → not actually streaming; test token-by-token arrival

Stretch goals

- Add an INT4 vs INT8 comparison, or a per-layer sensitivity analysis (which layers tolerate quantization worst?)
- Measure quantization's effect specifically on Darija vs English — low-resource quant degradation is under-studied

**OS integration:** `EXP-2026-018 — bf16 vs GPTQ vs AWQ on AlgerianMMLU`. This is where Projects 2, 5, and 6 converge — the serving of your own aligned model, evaluated on your own benchmark. Portfolio artifact graph completes.


**Objective:** Serve the P2 DPO checkpoint through vLLM, quantify quantization trade-offs, and put a streaming endpoint in front of it.

#### Deliverables

- vLLM serving the quantized 7–8B model on the T4 (xformers backend; AWQ works at CC 7.5, Marlin does not)
- bf16 vs GPTQ vs AWQ quality-vs-memory table on your custom eval
- FastAPI streaming endpoint + a Locust load test (throughput/TTFT under concurrency)
- The honest gap analysis: your mini engine vs vLLM, explained


PART 2

## Essential Papers

Code first, paper second · read what your engine implements

### Must Read (3)

PagedAttention (vLLM: "Efficient Memory Management for LLM Serving")

MustImplement

This IS the project. Block tables, paged KV, near-zero fragmentation, prefix sharing — you implement the core idea in M3. The virtual-memory-for-KV analogy is the mental model that makes the whole mini engine make sense.

Key ideas  
Paged KV blocks, block tables, fragmentation elimination, prefix/copy-on-write sharing, continuous batching

Focus sections  
The PagedAttention mechanism + the memory-management + scheduling sections. The block-table figures.

Implement?  
**Yes — the block-table paging in M3.**

Time / Difficulty  
~3h · ★★★★★★☆☆☆☆ (6/10)

Orca: A Distributed Serving System (iteration-level / continuous batching)

MustRead

The paper that introduced iteration-level (continuous) batching — the idea you implement in M2. vLLM builds on this. Read it to understand _why_ token-level scheduling beats static batching, before you code the scheduler.

Key ideas  
Iteration-level scheduling, selective batching, admit/evict at token granularity

Focus sections  
The iteration-level batching mechanism + the scheduling figure. Skip the distributed parts.

Implement?  
**Concept → your M2 scheduler**

Time / Difficulty  
~2.5h · ★★★★★☆☆☆☆☆ (5/10)

FlashAttention / FlashAttention-2 (revisit for inference)

MustMental model

You met it in P1/P4. Revisit with serving eyes: it's the attention kernel under both your engine's decode step and vLLM's. Understanding IO-awareness explains why decode is bandwidth-bound and why the KV cache dominates memory traffic.

Key ideas  
IO-aware attention, why decode re-reads the KV cache, memory-bandwidth ceiling

Focus sections  
The memory-hierarchy figure + (for inference) the decode/single-query discussion

Implement?  
**Concept — you already have the P4 kernel**

Time / Difficulty  
~2h re-read · ★★★★★★★☆☆☆ (7/10)

### Recommended (3)

GPTQ: Accurate Post-Training Quantization

RecApply

One of the two quantization methods you compare in M6. Understand the layer-wise error-compensation idea and why calibration data matters — it explains your quality deltas.

Focus  
The layer-wise quantization + calibration mechanism

Implement?  
Use via AutoGPTQ; understand it

Time / Diff  
~1.5h · 6/10

Why rec  
Explains M6 results

AWQ: Activation-aware Weight Quantization

RecApply

The other M6 method. Its key insight — protect the salient weights identified by activation magnitudes — is why AWQ often beats GPTQ on quality. Read to interpret your bf16-vs-GPTQ-vs-AWQ table.

Focus  
Salient-weight protection via activation scales

Implement?  
Use via AutoAWQ; understand it

Time / Diff  
~1.5h · 6/10

Why rec  
The quality-winner story

SGLang / RadixAttention

Rec

RadixAttention shares KV across requests via a prefix tree — a different, complementary idea to vLLM's paging. Skim it to understand the frontier of KV sharing and to speak to "vLLM vs SGLang" in interviews. Your roadmap says skim, not implement.

Focus  
The prefix-tree KV-sharing mechanism

Implement?  
No — skim for the concept

Time / Diff  
~1h skim · 6/10

Why rec  
Frontier context

### Optional (2)

Speculative Decoding (Leviathan et al. / Medusa)

Optional

Optional advanced inference technique — a draft model proposes tokens the target verifies in parallel, cutting decode latency. Read only if you take a stretch goal; it's a whole additional axis beyond batching/paging.

Focus  
Draft-verify loop, acceptance rate

Time / Diff  
~2h · 7/10

llama.cpp / GGUF quantization formats (docs, not a paper)

Optional

Optional and deprioritized — your roadmap says **skim**. GGUF/k-quants matter for CPU/edge deployment, a different track than GPU serving. Skim only to know the format exists and where it fits; don't invest here now.

Focus  
The k-quant format taxonomy only

Time / Diff  
~45m skim · 4/10

PART 3

## Open Source — Exactly What to Read

The specific files · production serving code reveals the scheduling tricks

|Repo|Files that actually matter|Why|
|---|---|---|
|**vllm-project/vllm**|`core/scheduler.py` · `core/block_manager*.py` · `engine/llm_engine.py` · `worker/cache_engine.py`|THE required reading. `scheduler.py` = your M2 continuous batching, done for real (admit/evict, preemption). `block_manager` = your M3 paged KV. Read these AFTER building your mini versions so you can map line-to-line.|
|**vllm-project/vllm** (attention)|`attention/ops/*` · `attention/backends/*`|You already skimmed this in Project 4. Now it clicks — the paged-attention kernel gathers KV over the block table you built in M3.|
|**karpathy/nano-vllm** (or a minimal-vLLM clone)|the whole thing — it's small|A minimal continuous-batching + paged-KV engine. The single best reference for what "vLLM but mini" should look like. Read it as your M1–M4 north star; diff your engine against it.|
|**sgl-project/sglang**|the RadixAttention / prefix-cache module|Skim only. See how prefix-tree KV sharing differs from vLLM's paging — for the "vLLM vs SGLang" interview answer.|
|**AutoGPTQ / AutoAWQ**|the quantize entry point + the calibration loop|For M6. Read enough to know what calibration data does and how the packed 4-bit weights are stored. Explains your quality deltas.|
|**locustio/locust**|a basic `locustfile.py` example|For M5 load testing. Tiny — you need the user/task pattern and how to ramp concurrency. Copy an LLM-endpoint example.|

**Reading order:** nano-vllm (see the whole minimal shape) → build your M1–M4 → THEN vLLM `scheduler.py` + `block_manager` (map to your code, understand the production extras: preemption, swapping, prefix caching). Reimplement the mini engine fully; deep-read vLLM, don't rebuild it — this is exactly your roadmap's "reserve full reimplementation for the frontier things" rule.

PART 4

## Experiments Worth Running

Each answers a question · most become content · one seeds a paper

⚗ E1 · Latency-vs-throughput curve (batch 1/8/32)→ content · centerpiece

Question How does batch size trade per-request latency (TTFT, ITL) against total throughput?

Insight The fundamental serving trade-off, visualized. This one chart is the resume centerpiece and the thing every inference interviewer wants you to explain.

Content / Paper? **Content** — the flagship benchmark chart. **Paper?** No.

⚗ E2 · Mini engine vs vLLM, decomposed→ content · senior signal

Question vLLM is Nx faster — how much is fused kernels vs scheduling vs CUDA graphs vs paging?

Insight Attributing the gap (not just measuring it) is what separates "I ran a benchmark" from "I understand production inference." The honest, decomposed answer is elite content.

Content / Paper? **Content** — the "I built a mini vLLM and here's why the real one wins" writeup. This is WRITE-UP #2.

⚗ E3 · Paged vs contiguous KV: concurrent-sequence capacity→ content

Question How many more concurrent requests fit with paged KV vs a contiguous per-sequence buffer?

Insight Makes PagedAttention's memory win concrete and measured — the paper's central claim, reproduced by you.

Content / Paper? **Content** — a reproduction post ("I reproduced PagedAttention's memory win at mini scale").

⚗ E4 · Quantization quality-vs-memory on AlgerianMMLU→ content + paper

Question How much quality does GPTQ/AWQ cost on a **low-resource dialect** vs the memory it saves?

Insight Quantization degradation on low-resource languages is genuinely under-studied. Measured on your own Darija benchmark, this is a real contribution, not a rehash.

Content / Paper? **Both.** The trade-off table + a workshop-adjacent finding on low-resource quant degradation.

⚗ E5 · The KV-cache position-offset bug forensics→ content · highest shareability

Question What exactly breaks when RoPE/position offsets are wrong after the first decode step?

Insight The single most common KV-cache bug: perfect first token, garbage after. Trigger it, show the output, fix it. The definitive inference debugging story.

Content / Paper? **Content** — Debugging Story, the most engaging piece this project produces.

PART 5

## Research Questions

Beginner → Intermediate → Advanced

### Beginner · while learning

At what batch size does throughput saturate on a T4, and where's the latency knee?

Finds the "operating point" — the practical serving sweet spot. Foundational.

How much of decode-step time is the KV-cache read vs the actual matmul?

Confirms decode is bandwidth-bound viscerally, in your own numbers.

How many more concurrent sequences fit with block size 16 vs 32 vs 64?

The paging granularity trade-off, measured.

### Intermediate · needs experiments

How much does prefix/block sharing help when many requests share a system prompt?

This is where vLLM's biggest real-world wins come from — measuring it at mini scale is instructive and under-demonstrated.

Does continuous batching's throughput advantage over static batching grow or shrink as request-length variance increases?

The mechanism only pays off with length variance — quantifying that relationship is genuinely useful.

Does 4-bit quantization degrade Darija generation more than English, at equal perplexity on the base model?

Low-resource quant fairness is under-explored and directly ties to your thesis.

### Advanced · workshop / conference potential

Do post-training quantization methods (GPTQ/AWQ) disproportionately harm low-resource language capability relative to high-resource, and does calibration-set language matter?

Publishable: novel benchmark (yours) + an under-studied fairness question + a practical deployment implication (you can't afford to serve unquantized). Ties Projects 2, 5, 6 into one paper.

Can calibrating GPTQ on low-resource text recover the quality lost when calibrating on English?

A concrete, testable intervention with real deployment value for under-served languages.

A reproducible "LLM serving on a single consumer/free GPU" benchmark: how do batching/paging/quantization interact at the small scale most practitioners actually have?

Not novel-method, but an accessible, honest systems benchmark suits a reproducibility/efficiency workshop. Strong blog first.

PART 6

## Research Ideas

Labeled by risk · novelty · difficulty

Quantization fairness for low-resource languages (GPTQ/AWQ on Darija vs English)

Medium risk

**Type:** Evaluation/analysis paper. **Novelty:** High (under-studied). **Difficulty:** Medium — you already have the benchmark (P6), the model (P2), and the quant pipeline (M6). The single strongest paper candidate in this project, and it unifies your whole roadmap.

"I built a mini vLLM" — a pedagogical minimal inference engine + gap analysis

Low risk

**Type:** Engineering/education artifact. **Novelty:** Low-Medium (value = clarity + honesty). **Difficulty:** Medium — it's this project, packaged. Extremely high portfolio/content ROI; the decomposed vLLM-gap analysis is the senior signal.

An OSS PR to vLLM or SGLang (bug fix, doc, or benchmark)

Medium risk

**Type:** Open-source contribution. **Novelty:** N/A. **Difficulty:** Medium — while deep-reading `scheduler.py`/`block_manager`, find a real gap. Per your OS, one merged PR to a systems repo like vLLM outweighs ten portfolio projects. Highest career ROI, near-zero compute.

Serving efficiency on a single consumer GPU: a reproducible benchmark suite

High risk

**Type:** Systems/reproducibility. **Novelty:** Low-Medium. **Difficulty:** High to reach paper rigor on one card. Honest verdict: strong blog, workshop only if combined with the quantization-fairness angle.

PART 7

## Content Opportunities

Per milestone · platform · effort · long-term value · evidence-gated

|Milestone|Content|Best platform|Effort|Long-term value|
|---|---|---|---|---|
|**M1 KV cache**|Architecture Explanation: "Prefill is compute-bound, decode is bandwidth-bound"|Personal site · X|2h|★★★★ teaching credibility|
|**M2 Continuous batching**|Diagram post: "Why static batching wastes your GPU"|X · LinkedIn|1.5h|★★★★ intuitive, shareable|
|**M3 Paged KV**|Paper→Code: "I reproduced PagedAttention's memory win at mini scale"|Personal site · GitHub|2.5h|★★★★★ reproduces the paper|
|**M4 Benchmark**|The latency-vs-throughput curve (resume centerpiece chart)|Personal site · X|1.5h|★★★★★ the flagship visual|
|**M5 vLLM gap**|WRITE-UP #2: "I built a mini vLLM — here's why the real one is faster"|Personal site (canonical)|4h|★★★★★ elite senior signal|
|**M6 Quant + serve**|Benchmark Report: bf16 vs GPTQ vs AWQ on AlgerianMMLU + live demo|HF Space · LinkedIn|2.5h|★★★★★ unifies the roadmap|
|**Whole project**|OSS PR to vLLM/SGLang + the `mini-inference-engine/` repo README|GitHub · OSS|—|★★★★★ PR > 10 repos|

**OS rule enforcement:** Total ≈ 14h over 2 weeks — under 15%. WRITE-UP #2 ("I built a mini vLLM") **is your 3rd canonical long-form article** — your roadmap explicitly names it, so this is the RAG-or-inference slot spent on inference. Do NOT also write a separate giant quantization article; the quant table is a _section_ of the mini-vLLM writeup + a short post. The highest-value single output remains the **OSS PR**. Everything gates on M4's defined-workload benchmark.

PART 8

## Knowledge Capture — Permanent

Feed llm-lab/projects/mini-inference-engine/ · reusable serving toolkit

#### Reusable Utilities

- **KV cache module** (prefill/decode split) — reuse in any serving work
- **Continuous-batching scheduler** (admit/evict loop) — the core reusable piece
- **Paged-KV block manager** (block pool + block table) — mini-vLLM in a file
- **Benchmark harness** (TTFT/ITL/throughput, p50/p95, concurrent load) — reuse for every serving claim
- **Locust load-test template** for LLM endpoints
- **FastAPI streaming server** (SSE) skeleton — reuse for every demo

#### Notes / Diagrams / Decisions

- **Block-table diagram** (logical→physical KV pages) — reuse from P4, extend here
- **Static-vs-continuous-batching diagram** — evergreen teaching asset
- **Prefill-vs-decode roofline note** (compute-bound vs bandwidth-bound)
- **Inference glossary**: TTFT, ITL/TPOT, throughput, p95, saturation — interview-ready
- **"Why vLLM is faster" decomposition** (kernels, CUDA graphs, scheduling, prefix cache) — a senior-signal note
- **KV-cache bug playbook**: position offset, cache masking, block-free leak, streaming buffer
- **Quant trade-off table** (bf16/GPTQ/AWQ × quality/VRAM/latency) on your own eval

Store in `llm-lab/projects/mini-inference-engine/`: `engine/` (kv_cache, scheduler, block_manager), `bench/`, `serve/fastapi_app.py`, `experiments/EXP-2026-014..018.md`, `bugs/BUG-2026-00N-kv-offset.md`, `diagrams/`. Every benchmark records the full workload spec: hardware, model, dtype, prompt/output length distribution, concurrency.

PART 9

## Resume & Portfolio Value

What appears where · what proves quality

GitHub

- `mini-inference-engine/`: KV cache + continuous batching + paged KV + benchmark harness, results-first README with the trade-off chart
- `vllm-load-testing/`: Locust script + throughput-under-concurrency graph
- The honest mini-engine-vs-vLLM comparison + decomposed gap analysis

Open Source (top prize)

- A PR to vLLM or SGLang — bug fix, doc, or benchmark, found while reading the scheduler/block manager

Hugging Face

- Quantized (AWQ/GPTQ) Darija DPO model + a live Space demo with streaming
- Quant trade-off table in the model card

Resume (two lines)

"Built a mini inference engine from scratch (KV cache, continuous batching, paged KV memory) and benchmarked TTFT/ITL/throughput vs vLLM on a defined workload, with a decomposed analysis of the performance gap."  
  
"Deployed and load-tested a quantized 7–8B model with vLLM behind a streaming FastAPI endpoint; measured GPTQ vs AWQ quality-vs-memory trade-offs on a self-built benchmark."

LinkedIn

- The latency-vs-throughput curve post
- "I built a mini vLLM" writeup
- The KV-cache debugging story + live demo

Evidence of quality

- Benchmarks with a full workload spec (hardware/model/dtype/lengths) — the detail seniors check first
- p95 latency reported, not just mean (proves you know tail latency matters)
- An _honest, decomposed_ vLLM gap analysis (integrity + depth)
- Quantization quality measured on a real eval, not assumed free
- A merged OSS PR to a serving repo (frontier-competence signal)

PART 10

## Stretch Goals — Ranked by ROI

If you finish early

1

OSS PR to vLLM or SGLang

Highest ROI. While reading the scheduler/block manager, find a real gap (bug, doc hole, missing benchmark) and submit. Per your OS, one merged systems-repo PR outweighs the entire rest of the portfolio for inference-team hiring. Near-zero compute.

Career · top prize

2

Quantization fairness on Darija vs English (E4 → paper)

Highest research ROI. You already have benchmark + model + quant pipeline. Under-studied, thesis-relevant, unifies Projects 2/5/6 into a workshop paper. Cheap given the pieces exist.

Research

3

Prefix/block sharing in the mini engine

Where vLLM's biggest real wins come from. Implementing shared-prefix KV blocks deepens the engine and makes the vLLM-gap analysis far richer. High engineering ROI.

Engineering

4

Speculative decoding (draft-verify) in the mini engine

A whole additional latency axis and a hot topic. Big stretch, big payoff for decode-latency understanding. Only after the core engine + vLLM comparison ship.

Engineering (ambitious)

5

INT4/INT8 + per-layer quantization sensitivity

Deeper quant analysis (which layers tolerate quantization worst). Good learning, moderate visible ROI. Lower priority than the fairness angle.

Depth

PART 11

## Final Project Dashboard

Everything scored · Eng / Res / Port / Content / Career (1–5)

| Task                                       | Pri    | Diff | Time | Eng | Res | Port | Content | Career |
| ------------------------------------------ | ------ | ---- | ---- | --- | --- | ---- | ------- | ------ |
| **M1 · KV cache**                          | Must   | 5/10 | ~10h | 5   | 1   | 4    | 4       | 5      |
| **M2 · Continuous batching**               | Must   | 7/10 | ~14h | 5   | 2   | 5    | 5       | 5      |
| **M3 · Paged KV memory**                   | Must   | 8/10 | ~14h | 5   | 3   | 5    | 5       | 5      |
| **M4 · Benchmark harness**                 | Must   | 5/10 | ~10h | 4   | 2   | 5    | 5       | 5      |
| **M5 · vLLM benchmark + read + load test** | Must   | 6/10 | ~14h | 5   | 2   | 5    | 5       | 5      |
| **M6 · Quantize + serve (FastAPI)**        | Strong | 6/10 | ~12h | 4   | 4   | 5    | 5       | 5      |
| E4 · Quant fairness on AlgerianMMLU        | Strong | 5/10 | ~6h  | 3   | 5   | 4    | 5       | 4      |
| OSS PR (vLLM / SGLang)                     | Strong | 6/10 | ~8h  | 4   | 1   | 5    | 4       | 5      |
| E5 · KV-offset bug forensics               | Strong | 3/10 | ~3h  | 4   | 1   | 3    | 5       | 4      |
| WRITE-UP #2 (canonical: mini vLLM)         | Strong | 3/10 | ~4h  | 1   | 2   | 5    | 5       | 5      |
| Prefix sharing (stretch)                   | Opt    | 7/10 | ~8h  | 5   | 3   | 4    | 4       | 5      |

**Read the dashboard this way:** This project has near-perfect Eng/Port/Content/Career columns (mostly 5s) — it's your **resume centerpiece** for a reason. M2 (continuous batching) and M3 (paged KV) are the differentiators almost no junior can build; protect them. M5's **decomposed vLLM gap analysis** is the single most senior-reading deliverable — "I built it AND I know why the pros are faster." Research value spikes only at M6/E4 (quantization fairness) — that's your one paper thread, and it's cheap because P2 and P6 already built the model and benchmark. If forced to cut, drop prefix-sharing first; never cut M2 or M3. Highest optional ROI: the **OSS PR**.

Project 05 Value Plan · integrates with LLM Lab OS · the capstone that consumes Projects 01–04 · mini engine first, vLLM second · define the workload before you quote a number · inference is where the money is made