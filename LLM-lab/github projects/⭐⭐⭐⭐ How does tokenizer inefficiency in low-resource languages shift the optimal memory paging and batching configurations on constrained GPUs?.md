
Assistant A

Understood. If you did not like the Alignment & Evaluation System, we drop it completely.

Your background is in Statistics and Data Science, and you are currently building a low-level inference engine (KV cache, prefill/decode, batching). It is likely that DPO/Alignment felt too much like "training fluff" and not enough like hard systems engineering. You want to push deeper into the infrastructure, performance, and systems side of LLM Engineering.

Since you mentioned `dziri-eval` and Algerian Darija, we can integrate that, but not as a benchmark dataset. Instead, we will use it as the **workload** that exposes a hard systems problem.

Here is the project that bridges your inference engine work, your stats background, and the Made With ML MLOps curriculum, without touching model training.

---

### The New Project: LLM Inference Auto-Tuner & Profiling Platform

**Problem:**  
LLM inference performance is highly sensitive to engine configurations: batch size, KV cache block size, swap space, and continuous batching thresholds. Finding the optimal config for specific hardware (like an RTX 3050 4GB) and a specific workload is pure guesswork. Furthermore, low-resource languages like Algerian Darija suffer from tokenizer bloat (a single Darija word takes more tokens than an English word), which means KV caches fill up faster, fundamentally shifting the memory/compute bottleneck. Tuning for English workloads on a 4GB GPU will OOM or throttle on Darija workloads.

**Users:**  
ML/Serving engineers deploying LLMs to constrained or edge hardware.

**Technical core:**  
Workload characterization, latency/throughput modeling, Bayesian optimization (Optuna) applied to _system parameters_ rather than model weights.

**Engineering core:**  
Automating the server lifecycle (start vLLM -> wait for ready -> load test -> kill), parsing `torch profiler` / `nvidia-smi` metrics, handling OOMs gracefully during the search loop.

**Systems core:**  
GPU memory profiling, identifying compute-bound vs memory-bound phases, tuning KV-cache paging parameters to survive tokenizer bloat.

**MVP:**  
A Python script that takes a batch size and KV cache fraction, starts a vLLM server, fires Dziri-Eval prompts at it using a load tester, measures TTFT/ITL, and logs the result to MLflow.

**Evolution:**  
MVP -> Optuna integration for multi-parameter search -> Multi-objective optimization (finding the Pareto frontier of latency vs throughput) -> CI/CD performance regression pipeline -> Tuning your custom mini-engine and comparing its optimal configs against vLLM.

**Learning:**  
Bayesian optimization, async server lifecycle management, deep GPU memory profiling, performance engineering, LLMOps automation.

**Made With ML connection:**  
Optuna for system tuning (instead of model tuning); MLflow for system metrics (instead of loss); CI/CD for perf regression (instead of code linting).

**Beyond Made With ML:**  
OS-level process management, Pareto frontier analysis, low-level vLLM config internals, hardware roofline modeling.

**Portfolio:**  
Elite systems engineering signal. Proves you don't just run a model, you understand how to squeeze maximum performance out of constrained hardware for a specific domain.

**Research:**  
How does tokenizer inefficiency in low-resource languages shift the optimal memory paging and batching configurations on constrained GPUs?

**Open source:**  
A workload-aware auto-tuning framework for local LLM serving.

**Compute:**  
Yes. Entirely designed around the 4GB RTX 3050 limit. In fact, the constraint is the point of the project.

**Difficulty:** 8/10

**Time:** 6 weeks

---

### Why this is the right project for you

1. **It uses your Stats background properly:** Instead of using statistics for subjective LLM-as-a-judge prompts, you are using Bayesian Optimization (Gaussian Processes, Tree-structured Parzen Estimators) to explore a high-dimensional system configuration space and find a Pareto frontier. This is hard, rigorous statistics applied to ML infrastructure.
2. **It extends your inference engine:** You are already building an inference engine. This platform becomes the _harness_ that tests and tunes both your engine and vLLM. You aren't abandoning your current work; you are building the MLOps layer on top of it.
3. **It uses Dziri-Eval meaningfully:** Darija tokenization bloat is a real systems problem. If an English prompt takes 50 tokens and a Darija prompt takes 120 tokens, the KV cache pressure is completely different. Your auto-tuner will prove that a 4GB GPU needs different paging/batching configs for Darija than it does for English. That is a publishable, highly interesting engineering insight.
4. **It perfectly matches Made With ML without copying it:** Made With ML teaches MLflow and Optuna for _model hyperparameter tuning_. You will use the exact same tools, but for _inference hyperparameter tuning_. This shows you understand the MLOps principles deeply enough to apply them to novel problems.

---

### Architecture

text

```
Workload Generator (Dziri-Eval Darija prompts + English MMLU prompts)
↓
Tuner Agent (Optuna) → suggests params (max_batch_size, block_size, swap_space, gpu_mem_util)
↓
Server Lifecycle Manager (Subprocess/vLLM API) → starts engine with suggested params
↓
Load Tester (Locust/async Python) → fires concurrent requests, measures TTFT/ITL/Throughput
↓
Metrics Collector (nvidia-smi, torch profiler) → peak VRAM, KV cache hit rate, OOM events
↓
Objective Evaluator → calculates Pareto penalty (e.g., minimize ITL, maximize throughput, penalize OOM)
↓
Experiment Tracking (MLflow) → logs params, metrics, and system traces
↓
Best Config Artifact → JSON file deployed with the model
↓
CI/CD (GitHub Actions) → triggers auto-tuner on hardware when engine code changes to detect regressions
```

### Repository structure

text

```
inference-auto-tuner/
├── .github/workflows/
│   └── perf_regression.yml          # Run auto-tuner on PRs to check for perf drops
├── configs/
│   ├── search_space.yaml            # Define min/max for batch size, block size, etc.
│   └── workload_configs.yaml        # Darija vs English prompt distributions
├── data/
│   └── dziri_eval_workload.jsonl    # Sampled Darija prompts for load testing
├── src/
│   ├── workloads/
│   │   └── loader.py                # Loads and tokenizes Dziri-Eval prompts
│   ├── lifecycle/
│   │   ├── server_manager.py        # Start/stop vLLM or mini-engine subprocess
│   │   └── health_check.py          # Wait for server ready before load testing
│   ├── profiler/
│   │   ├── metrics.py               # Parse nvidia-smi / torch profiler logs
│   │   └── oom_detector.py          # Graceful OOM handling during search
│   ├── tuner/
│   │   ├── objective.py             # The function Optuna optimizes
│   │   ├── sampler.py               # TPE / Gaussian Process config
│   │   └── pareto.py                # Multi-objective logic
│   ├── tracking/
│   │   └── mlflow_logger.py         # Log system params and perf metrics
│   └── deploy/
│       └── apply_config.py          # Inject best config into production server
├── tests/
│   ├── test_lifecycle.py            # Mock server start/stop
│   └── test_objective.py            # Test Pareto penalty calculation
├── notebooks/
│   └── analyze_search.ipynb            Visualize Optuna trials and Pareto frontiers
├── Dockerfile                       # Container with vLLM, CUDA, Optuna
├── requirements.txt
└── README.md
```

### Development stages

**Stage 1 — MVP**

- Write a Python script that starts a vLLM server with hardcoded arguments (e.g., `--max-num-seqs 8`, `--gpu-memory-utilization 0.5`).
- Write an async load tester that fires 50 Dziri-Eval prompts at it.
- Measure TTFT (Time To First Token) and ITL (Inter-Token Latency), kill the server, and print the results.

**Stage 2 — Engineering foundation**

- Introduce Optuna. Define a search space for 3 variables: `max_num_seqs` (4 to 32), `block_size` (8 to 32), and `gpu_memory_utilization` (0.3 to 0.8).
- Write the `objective` function: Optuna suggests params -> script starts vLLM -> load tests -> returns average ITL to Optuna.
- Integrate MLflow to log every Optuna trial.

**Stage 3 — Productionization & Multi-Objective**

- Upgrade the objective function to be multi-objective: Optuna must _minimize_ ITL and _maximize_ Throughput simultaneously. This generates a Pareto frontier.
- Add robust OOM handling: if vLLM crashes mid-trial due to 4GB VRAM limits, the script catches the subprocess exit code, tells Optuna the trial failed (prune), and moves to the next config.
- Dockerize the auto-tuner so it can run on any cloud GPU with the same constraints.

**Stage 4 — Advanced engineering (The Mini-Engine Integration)**

- Point the auto-tuner at _your_ custom mini-inference engine instead of vLLM.
- Tune the batching logic and KV cache limits of your engine.
- Compare the optimal Pareto frontier of your engine vs vLLM on the 4GB GPU for Darija workloads.

**Stage 5 — Research / Open Source**

- Publish the findings: "Optimal Inference Configurations for Low-Resource Languages Tokenizer Bloat on 4GB GPUs."
- Release the `inference-auto-tuner` as a CLI tool: `tune-inference --engine vllm --workload dziri.jsonl --vram 4gb`.

---

### Synchronize with Made With ML

Here is how this project maps to the Made With ML curriculum, distinguishing what you learn from the course vs. what you learn independently.

text

```
Made With ML topic: Design
        ↓
Learned from MWMl: Translating product requirements into ML objectives; defining online vs offline metrics.
        ↓
Applied to the project: Define the objective: "Find the Pareto-optimal vLLM system configs that minimize ITL and maximize throughput for Darija workloads on a 4GB GPU."
        ↓
Learned independently: Understanding that LLM system metrics (TTFT, ITL, KV cache hit rate) are fundamentally different from model metrics (F1, accuracy). Understanding how tokenizer bloat shifts memory pressure.
        ↓
Project milestone: Project charter and system metric rubric defined.

Made With ML topic: Data
        ↓
Learned from MWMl: Data preprocessing, splitting, and versioning.
        ↓
Applied to the project: Process the Dziri-Eval dataset into a realistic load-testing workload (JSONL of prompts with varying lengths). Version this workload data with DVC.
        ↓
Learned independently: Workload characterization. Real users don't send uniform prompts. You must sample Dziri-Eval to create a distribution of short, medium, and long Darija prompts to stress the KV cache realistically.
        ↓
Project milestone: Reproducible workload generator that simulates concurrent Darija traffic.

Made With ML topic: Modeling (Experiment Tracking)
        ↓
Learned from MWMl: MLflow integration, logging params/metrics/artifacts.
        ↓
Applied to the project: Log every Optuna trial to MLflow. The "params" are system configs (block size, batch size), the "metrics" are TTFT, ITL, peak VRAM.
        ↓
Learned independently: Subprocess lifecycle management. Programmatically starting a CUDA subprocess, waiting for the HTTP health check to pass, running the load test, and violently killing the subprocess to free VRAM for the next trial without leaking memory.
        ↓
Project milestone: A working Optuna loop that logs system performance to MLflow without crashing the OS.

Made With ML topic: Optimization (Hyperparameter Tuning)
        ↓
Learned from MWMl: Using Optuna for hyperparameter tuning; defining search spaces; pruning unpromising trials.
        ↓
Applied to the project: Define the system search space. Use Optuna's TPE (Tree-structured Parzen Estimator) sampler to navigate the high-dimensional space of vLLM configs. Use Optuna's MedianPruner to kill server trials that are OOMing or producing terrible latency early.
        ↓
Learned independently: Multi-objective optimization (Pareto fronts). You aren't looking for one best number; you are looking for the set of configs where you cannot improve throughput without hurting latency. Visualizing Pareto frontiers in MLflow.
        ↓
Project milestone: An automated search that outputs a Pareto frontier of optimal system configurations.

Made With ML topic: Evaluation
        ↓
Learned from MWMl: Offline evaluation workflows, behavioral testing.
        ↓
Applied to the project: Build an evaluation harness that takes the "best" config found by Optuna, runs an extended 10-minute load test, and statistically validates that it actually outperforms a default config (e.g., vLLM defaults).
        ↓
Learned independently: Statistical significance in latency testing. Variance in TTFT can be high. Using statistical tests (e.g., Mann-Whitney U) to prove that the tuned config's p95 latency is genuinely better, not just a fluke of the workload distribution.
        ↓
Project milestone: A rigorous benchmark report proving the auto-tuner found a better operating point.

Made With ML topic: Serving
        ↓
Learned from MWMl: FastAPI, Ray Serve, microservices.
        ↓
Applied to the project: The auto-tuner outputs a JSON config file. Write a FastAPI deployment script that reads this JSON and boots vLLM with those exact parameters for "production" serving.
        ↓
Learned independently: Deep vLLM engine internals. Understanding what `gpu_memory_utilization` actually reserves vs what the OS needs. Understanding how `block_size` affects attention kernel IO on a 4GB card. 
        ↓
Project milestone: A deployment script that serves the Darija model using the auto-tuned optimal parameters.

Made With ML topic: CI/CD
        ↓
Learned from MWMl: GitHub Actions, testing workflows, continuous training pipelines.
        ↓
Applied to the project: Write a GitHub Actions workflow. On any PR that modifies the inference engine code, spin up a cloud GPU runner, run a shortened 10-trial Optuna search, and assert that the best Pareto point hasn't degraded by more than 5% from the main branch.
        ↓
Learned independently: Performance regression testing. This is a massive part of ML Infra engineering. If someone changes the KV cache logic in your engine, does it silently make latency worse? The CI/CD pipeline catches this before merge.
        ↓
Project milestone: Fully automated performance regression pipeline.
```

### Final Objective Realized

With this project, you do not train a single weight. You focus entirely on the hardest part of LLM Engineering: making inference fast, cheap, and reliable under strict hardware limits.

You take a real problem (Darija tokenizer bloat on 4GB GPUs), use MWMl concepts (MLflow, Optuna, CI/CD) to structure the engineering process, and push far beyond the course by implementing multi-objective Bayesian optimization on system parameters, subprocess lifecycle management, and Pareto frontier analysis.

When you walk into an interview, you can say: _"I built an inference auto-tuner that uses Bayesian optimization to find the Pareto-optimal batching and KV-cache configurations for low-resource language workloads on 4GB GPUs. It automatically starts and kills the inference engine, profiles VRAM and latency, and gates merges via a performance regression CI/CD pipeline."_

That is an LLM Infrastructure Engineer.

Assistant B

That was **not** my recommendation. I recommended **InferenceCore** — an inference engine, serving pipeline, and benchmark system. You seem to be reacting to the evaluation layer inside it.

Let me correct this immediately and remove the ambiguity.

## What I actually said: Build InferenceCore

Not an "alignment system." Not an "evaluation system." This is what it actually is:

text

```
Your inference engine (KV cache, batching, paged memory concept)
    ↓
Benchmark harness (TTFT / ITL / throughput — the metrics inference teams use)
    ↓
vLLM comparison + honest gap analysis
    ↓
Quantized serving (FastAPI + streaming)
    ↓
Basic telemetry (latency logs, not "model evaluation")
```

The **only** reason evaluation appears is in M6: you measure whether quantization destroyed your Darija model's quality **before serving it**. That is not the project. That is a **quality gate** inside a serving pipeline.

If that evaluation gate is what made it sound like an "alignment & evaluation system," I will strip it out of the framing entirely.

## The corrected, pure version: Call it **ServeCore**

Same architecture. Same stages. But framed exactly for your career goal (ML/LLM Engineer, inference, serving):

|Component|Purpose|Not this|
|---|---|---|
|Engine (your code)|Learn mechanisms|Not "research"|
|Benchmark harness|Measure serving performance|Not "benchmark science"|
|vLLM reference|Understand production gap|Not "comparison study"|
|Quantized serving|Deploy a real endpoint|Not "deployment demo"|
|Telemetry|See latency/spikes|Not "monitoring platform"|
|Darija quality check|**One table**: does quantized 7B still work?|Not an "evaluation framework"|

The Darija check is literally one script (`quant_eval/quality_check.py`) that outputs a table: `bf16` vs `GPTQ` vs `AWQ` on a small prompt set. If it passes, you serve it. If not, you serve `bf16`. That is **engineering validation**, not an evaluation system.

## If you reject even that embedded evaluation layer

Then remove M6's quality evaluation and replace it with pure serving optimization:

- M6 becomes: **Quantized serving + load testing + streaming optimization** (no quality comparison, just serve the quantized model and measure throughput).
- The project becomes 100% inference infrastructure: build, benchmark, serve, observe.

But I advise keeping the single quality table — because serving a broken quantized model is worse engineering, not better.

## Confirm your choice

> **Build ServeCore** (InferenceCore with zero evaluation framing).

- Pure inference mechanisms (KV cache, batching, paged concept).
- Benchmark against vLLM (systems comparison).
- Quantized model served via FastAPI (deployment).
- Load tested with Locust (performance engineering).
- Latency telemetry (observability).

No alignment. No evaluation platform. No `dziri-eval` framework.

Tell me: **Keep the embedded quant-quality gate (one table), or remove it entirely and go pure serving?** Once you confirm, I will redesign the repository structure and stages with zero ambiguity.

i didnt liked End-to-End Alignment & Evaluation System

Assistant A

We absolutely should incorporate Algerian Darija and `dziri-eval` into this project—but we should not make the project _only_ `dziri-eval`.

Here is the engineering reasoning behind this distinction, and why we should frame it as **"An End-to-End LLM Alignment System, applied to Darija and evaluated via Dziri-Eval."**

### 1. Artifact vs. Engineering System

If the project is just "dziri-eval," you are primarily building a **dataset and a benchmark artifact**. That is highly valuable for the open-source community and for NLP/Data science, but it does not test your ML/AI infrastructure skills.

- Building a benchmark means: translating questions, formatting JSONL, writing a script to loop through a model and check exact match/LLM-judge scores.
- Building an alignment system means: handling QLoRA memory crashes, orchestrating async LLM-judge API calls, automating model merging/quantization, wiring up CI/CD, and serving behind vLLM.

If you only build `dziri-eval`, you bypass the hardest LLMOps problems. By making the alignment system the core and `dziri-eval` the evaluation environment, you get both.

### 2. It Unifies Your Existing Roadmap

Look closely at the example roadmap you pasted in your initial prompt. You already have:

- **Project 2:** DPO Darija model
- **Project 6:** AlgerianMMLU harness

If we make this new project the system that _productionizes_ and _evaluates_ those exact artifacts, you create a unified portfolio.

- The base model gets aligned using Darija preference data in this new system.
- The system then runs `dziri-eval` (your AlgerianMMLU harness) as the gating metric.
- If the DPO model passes the eval threshold, the system automatically merges the LoRA adapters, quantizes it (M6 of your roadmap), and serves it via your mini-inference engine or vLLM.

### 3. The Low-Resource Advantage

Evaluating DPO on English is boring because everyone does it. The LLM-as-a-judge might just say "both are good."  
Evaluating DPO on Darija creates genuine, unsolved engineering and ML problems:

- **Judge Bias:** Does an English-centric LLM judge (like GPT-4o) fairly evaluate Darija responses? You will have to engineer the judge prompts to mitigate linguistic bias.
- **Tokenizer inefficiency:** Darija uses Latin script mixed with Arabic and French. Tokenizers bloat token counts, which means your KV cache fills up faster, making the 4GB VRAM constraint even harder. You have to engineer around this.
- **Quantization Degradation:** As noted in your roadmap's E4 experiment: does 4-bit quantization disproportionately harm Darija capability? The system can automatically measure this delta during the eval phase.

### How we adjust the Architecture

We keep the exact system I proposed, but we inject Darija as the domain and `dziri-eval` as the evaluation core:

text

```
Darija Preference Data (JSONL - chosen/rejected pairs in Darija)
↓
Data Validation & Versioning (DVC / Git LFS)
↓
DPO/QLoRA Training (Qwen2.5-0.5B/1.5B, 4-bit quantized, paged_adamw_8bit)
↓
Experiment Tracking (MLflow: DPO loss, KL divergence, LoRA params)
↓
Automated Evaluation Engine
  ├─ Module A: LLM-as-a-Judge (Async API calls, Darija-specific prompt rubric)
  └─ Module B: Dziri-Eval (AlgerianMMLU harness - exact match/MCQ scoring)
↓
Gating Logic (Does Dziri-Eval accuracy drop < 2%? Does Judge win-rate > 55%?)
  ├─ YES -> Model Registry (MLflow: transition to "Staging")
  └─ NO  -> Alert / Abort Pipeline
↓
Serving (Merge LoRA -> AWQ 4-bit -> vLLM backend behind FastAPI)
↓
Observability (Log Darija token lengths, KV cache eviction rates, generation latency)
↓
CI/CD (GitHub Actions: lint -> test -> train -> eval -> conditional deploy)
```

### What this means for your final pitch

If you just build `dziri-eval`, your pitch is: _"I built a benchmark for Algerian Darija."_ (Good, but limits you to NLP/Data roles).

With this system, your pitch becomes: _"I engineered an automated LLMOps pipeline for low-resource language alignment. It runs DPO under 4GB VRAM constraints, evaluates the model using a custom Darija LLM-judge and my AlgerianMMLU harness, and if it passes statistical gating, automatically quantizes and deploys it via vLLM. I solved tokenization bloat and quantization degradation for Arabic dialects along the way."_

That pitch makes you an LLM Engineer who happens to have deep domain expertise in low-resource languages, rather than just a linguist with a script.

**Verdict:** Keep the End-to-End Alignment & Evaluation System. Use Darija as the data, and `dziri-eval` as the evaluation module. This binds your past work, your domain interest, and the Made With ML curriculum into one elite engineering project.

Assistant B

We **could** — and if your goal were "LLM evaluation researcher for low-resource Arabic," I would have recommended it. But I didn't, because for **your** career target (ML/LLM Engineer, inference, serving, LLMOps, deployment), replacing InferenceCore with a standalone `dziri-eval` would be a strategic mistake.

Here is the direct comparison.

## What `dziri-eval` gives you

- A version-controlled benchmark harness for Algerian Darija.
- Metrics tracking across fine-tunes (DPO, alignment).
- Statistical comparison infrastructure.
- A valuable open-source artifact for the Arabic/NLP community.

That is serious work. But notice what it **does not** develop:

- No inference engine mechanics (your current KV-cache work dies).
- No serving layer (FastAPI, streaming, deployment).
- No vLLM / production serving comparison.
- No quantization serving evaluation (only model evaluation).
- No load testing, latency metrics, or systems performance.
- No CI/CD for deployed endpoints.

You already have evaluation experience (`AlgerianMMLU` is in your background). Building another evaluation framework — even domain-specific — does **not** close your biggest career gap: **you need to prove you can serve, benchmark, and operate LLM systems in production, not just score them.**

## Your current trajectory

You are literally in the middle of building an inference engine (KV cache, prefill/decode, batching, RoPE, position IDs). If you pivot to `dziri-eval` now, you abandon that momentum. The prompt explicitly said: _"Do not assume my project must use all existing skills... I want it to challenge me,"_ but it also said you want to end with **"a serious ML/AI system."** An evaluation harness is a tool; an inference pipeline with evaluation embedded is a system.

## The correct integration (not replacement)

Inside **InferenceCore**, `dziri-eval` already lives — not as the main dish, but as the **evaluation backbone** of the serving pipeline:

text

```
In M6 (Quant + Serve):
  → Quantize your Darija DPO model
  → Evaluate on AlgerianMMLU / your Darija benchmark (this IS dziri-eval logic)
  → Measure quality-vs-memory trade-off
  → Serve the quantized winner
```

So you **do** build `dziri-eval`: the benchmark dataset versioning, the evaluation script, the regression tests for Darija quality. But it serves the **engineering goal** (proving your quantized serving pipeline works) rather than being an isolated evaluation repo.

## When `dziri-eval` should be standalone

Only if you decide to specialize in:

- LLM evaluation science / benchmark design.
- Low-resource language fairness research (quantization degradation studies).

Even then, the best version of that project is **"dziri-eval as the evaluation layer of a serving system"** — which is exactly what InferenceCore does.

## Final decision

> **Do not pivot to `dziri-eval`.**

Keep **InferenceCore** as the primary project. Build the Darija evaluation infrastructure (`dziri-eval` logic) inside the quantization/evaluation stage (M6). This gives you **both** the evaluation rigor for Algerian Darija **and** the inference/serving/deployment system that makes you hireable as an ML/LLM Engineer.

If you finish InferenceCore and still want a pure evaluation artifact, extract the benchmark harness from `bench/` and publish it separately. But don't make it the capstone. The capstone must be the system that **uses** evaluation to serve models reliably.

why we dont make it dziri-eval , dziri for algerian darija

Assistant A

### Part 1: Inspection of Made With ML

Made With ML (by Goku Mohandas) is a highly structured course that teaches the operational lifecycle of ML models. Rather than focusing on model architecture design, it focuses on the _system around the model_.

**What it actually teaches:**

1. **Design & Data:** Translating product requirements into ML objectives, creating datasets, data preprocessing, and versioning (using basic scripts/JSON manifests or Ray Data for scale).
2. **Modeling:** Baselines, PyTorch training loops, experiment tracking (MLflow), hyperparameter tuning (Optuna), and metric logging.
3. **Evaluation:** Offline evaluation workflows, slicing metrics, and behavioral testing.
4. **Serving:** Microservices, FastAPI, Ray Serve, batching, and scaling.
5. **LLMOps:** Prompt engineering, RAG, retrieval evaluation, and fine-tuning LLMs (LoRA).
6. **MLOps/CI/CD:** Git workflows, GitHub Actions, Docker, testing (pytest), and continuous training pipelines.

**What it lacks (and where my project must push beyond):**

- **Deep LLM Evaluation:** The course touches on RAG evaluation, but not the rigorous, programmatic LLM-as-a-judge pipelines required for alignment and generation tasks.
- **Preference Optimization:** It covers basic fine-tuning, but skips DPO/RLHF infrastructure and evaluation.
- **Inference Optimization:** It teaches Ray Serve and basic batching, but not vLLM integration, quantization trade-offs, or KV-cache management at the deployment level.
- **LLM-Specific Observability:** It covers generic model monitoring, but not token-level cost tracking, hallucination detection, or prompt drift.

The project I choose will use Made With ML as the MLOps backbone (testing, tracking, CI/CD, serving) but force me to build the LLM-specific engineering components the course doesn't cover.

---

### Part 2: 8–10 Serious Candidates

#### 1. End-to-End LLM Alignment & Evaluation System

**Problem:** Fine-tuning LLMs with DPO/RLHF is operationally fragmented. Tracking preference data, running alignment runs under tight compute limits, and rigorously evaluating if the model actually improved (without human标注) is difficult.  
**Users:** LLM Engineers running alignment experiments on small teams.  
**Technical core:** Preference data processing, DPO loss computation, LLM-as-a-judge automated evaluation.  
**Engineering core:** Building a reproducible pipeline from data prep -> DPO training -> automated eval -> model registry, handling OOMs gracefully on 4GB VRAM.  
**Systems core:** QLoRA memory management, concurrent LLM-as-a-judge inference for evaluation, vLLM deployment of the final adapter-merged model.  
**MVP:** A DPO training script for a 0.5B model using QLoRA, plus an LLM-as-a-judge eval script.  
**Evolution:** MVP -> MLflow tracking & config management -> CI/CD pipeline triggering on data updates -> vLLM deployment with quantization -> automated rollback based on eval metrics.  
**Learning:** Deep LLM alignment mechanics, QLoRA memory profiling, LLM-judge prompt engineering, LLMOps CI/CD.  
**Made With ML connection:** Experiment tracking (MLflow) -> DPO runs; CI/CD -> Auto-trigger eval; Serving -> vLLM behind FastAPI.  
**Beyond Made With ML:** DPO implementation, QLoRA optimization for 4GB, LLM-as-a-judge statistical significance, vLLM deployment.  
**Portfolio:** Proves ability to own the LLM post-training lifecycle end-to-end.  
**Research:** Does LLM-as-a-judge correlate with human preference on low-resource languages after DPO?  
**Open source:** A reusable CLI/package for running DPO + automated eval on single GPUs.  
**Compute:** Yes. 0.5B/1.5B models with QLoRA fit on 4GB.  
**Difficulty:** 8/10  
**Time:** 6-8 weeks

#### 2. LLM Evaluation & Benchmarking Platform

**Problem:** Evaluating LLMs is an unsolved engineering problem. Ad-hoc eval scripts break, lack statistical rigor, and don't scale across multiple models or prompts.  
**Users:** ML teams needing to compare open-source models before deployment.  
**Technical core:** Defining metrics (exact match, semantic similarity, LLM-judge), handling non-determinism.  
**Engineering core:** Asynchronous evaluation pipelines, API rate limiting, caching intermediate results, reproducibility.  
**Systems core:** Concurrent batched inference against local models vs APIs.  
**MVP:** A Python framework that takes a model endpoint and a dataset, runs 3 metric types, and outputs a report.  
**Evolution:** MVP -> MLflow integration -> Distributed execution (Ray) -> Custom metric plugins -> UI dashboard.  
**Learning:** Eval system design, async Python, statistical testing for LLMs, MLOps reporting.  
**Made With ML connection:** Evaluation -> Building robust eval suites; Experiment tracking -> Logging metrics.  
**Beyond Made With ML:** Building a framework, not a script. LLM-judge bias mitigation. Async concurrent eval architecture.  
**Portfolio:** Shows rigorous ML engineering—understanding that eval is harder than training.  
**Research:** Variance reduction techniques for LLM-as-a-judge.  
**Open source:** Highly usable as an open-source eval harness.  
**Compute:** Yes. Can evaluate small local models or APIs.  
**Difficulty:** 7/10  
**Time:** 5-7 weeks

#### 3. Semantic Caching & Dynamic Batching Inference Proxy

**Problem:** Serving LLMs is expensive. Redundant queries waste compute, and naive serving underutilizes the GPU.  
**Users:** AI infrastructure teams.  
**Technical core:** Vector similarity for cache hits, token-level dynamic batching.  
**Engineering core:** Building a reverse proxy that intercepts LLM calls, checks a vector DB, and queues requests for batched generation.  
**Systems core:** Concurrency, queue management, vector indexing (FAISS), latency profiling.  
**MVP:** A FastAPI proxy that caches exact/semantic matches using SQLite + FAISS.  
**Evolution:** MVP -> Dynamic batching scheduler -> Redis vector cache -> TTL/eviction policies -> Observability dashboard.  
**Learning:** Inference infra, vector DB ops, async systems engineering, latency optimization.  
**Made With ML connection:** Serving -> Batching and scaling; CI/CD -> Testing the proxy.  
**Beyond Made With ML:** Reverse proxy architecture, semantic similarity caching, continuous dynamic batching logic.  
**Portfolio:** Infrastructure engineering signal. Proves understanding of inference bottlenecks.  
**Research:** Cache hit-rate vs. LLM hallucination risk (does returning a cached "similar" response break factual accuracy?).  
**Open source:** Useful drop-in proxy for OpenAI/vLLM endpoints.  
**Compute:** Yes. Runs locally, uses minimal GPU.  
**Difficulty:** 7/10  
**Time:** 5 weeks

#### 4. Automated Quantization & Deployment Pipeline

**Problem:** Deploying LLMs requires navigating quantization (GPTQ, AWQ, BNB). The quality/memory/latency trade-offs are highly model-dependent and manual to test.  
**Users:** ML Engineers deploying models to edge or constrained environments.  
**Technical core:** Quantization calibration, perplexity evaluation, KV-cache memory calculation.  
**Engineering core:** Automating the loop: download model -> quantize -> eval -> benchmark latency -> register best version.  
**Systems core:** Memory profiling, vLLM integration, benchmarking harness creation.  
**MVP:** Script that takes a bf16 model, applies BNB 4-bit, measures VRAM and perplexity.  
**Evolution:** MVP -> Add GPTQ/AWQ -> Automated latency benchmarking -> MLflow registry -> Auto-deploy best variant to vLLM.  
**Learning:** Quantization internals, deployment automation, performance engineering.  
**Made With ML connection:** Optimization -> Quantization; CI/CD -> Automating the pipeline; Registry -> MLflow.  
**Beyond Made With ML:** Deep quantization trade-off analysis, vLLM serving constraints, automated benchmarking.  
**Portfolio:** Strong systems/deployment engineering signal.  
**Research:** Layer-wise quantization sensitivity on small models.  
**Open source:** A CLI tool: `auto-quantize <model>`  
**Compute:** Yes, if using 0.5B-1.5B models.  
**Difficulty:** 6/10  
**Time:** 4-5 weeks

#### 5. Agent Observability & Tracing Platform

**Problem:** LLM agents are black boxes. When an agent fails a tool call, it's incredibly hard to debug without per-step tracing.  
**Users:** AI Engineers building tool-using agents.  
**Technical core:** Execution graph tracing, token counting, tool I/O validation.  
**Engineering core:** Patching/wrapping LLM clients to intercept calls non-invasively, streaming traces to a backend.  
**Systems core:** Low-overhead logging, async trace aggregation, UI visualization.  
**MVP:** A Python decorator/context manager that logs LLM inputs/outputs and tool calls to a local SQLite DB + simple web UI.  
**Evolution:** MVP -> Stream traces -> Add cost/latency metrics -> Anomaly detection on agent steps -> LangSmith clone.  
**Learning:** Observability, metaprogramming (decorators), distributed tracing concepts.  
**Made With ML connection:** Observability -> Monitoring models; Serving -> Instrumenting APIs.  
**Beyond Made With ML:** Agent-specific tracing architecture, OpenTelemetry concepts, UI development.  
**Portfolio:** Good for AI Engineer roles, slightly less deep on ML math.  
**Research:** Automated detection of agent loops/failures via trace analysis.  
**Open source:** Usable tracing library for LangChain/raw Python agents.  
**Compute:** Yes. Mostly CPU/web dev.  
**Difficulty:** 6/10  
**Time:** 5 weeks

#### 6. RAG Retrieval Optimization & Experimentation System

**Problem:** Most RAG projects are toys. Engineering a RAG system means systematically tuning chunk size, overlap, embedding models, and rerankers, and knowing if changes actually improved retrieval.  
**Users:** AI Engineers building knowledge bases.  
**Technical core:** Dense/sparse retrieval, cross-encoder reranking, recall@k metrics.  
**Engineering core:** Building an experimentation harness for retrieval, handling large document corpora efficiently.  
**Systems core:** Vector DB indexing pipelines, memory-efficient document processing.  
**MVP:** Pipeline: Chunk -> Embed -> Retrieve -> Evaluate with a curated dataset.  
**Evolution:** MVP -> Add Reranker -> Sweep chunking params via Optuna -> MLflow tracking -> Deploy as microservice.  
**Learning:** IR fundamentals, eval metrics (NDCG, Recall@k), MLOps for retrieval.  
**Made With ML connection:** Hyperparameter tuning -> Optuna for chunk size; Tracking -> MLflow.  
**Beyond Made With ML:** Deep retrieval evaluation, vector DB indexing internals, reranker deployment.  
**Portfolio:** Solid, but RAG is becoming commoditized.  
**Research:** Semantic chunking vs static chunking trade-offs.  
**Open source:** RAG evaluation harness.  
**Compute:** Yes. Embeddings fit on 4GB easily.  
**Difficulty:** 5/10  
**Time:** 4 weeks

#### 7. Continuous Evaluation & Auto-Rollback System

**Problem:** LLMs drift in production, and bad prompts/weights get deployed. There is no "CI for LLMs" that actually blocks bad deployments based on behavioral tests.  
**Users:** MLOps/LLMOps teams.  
**Technical core:** Behavioral testing (e.g., "model must refuse X", "must format as JSON"), statistical guardrails.  
**Engineering core:** GitHub Actions integration, model registry querying, automated deployment gating.  
**Systems core:** Running evals efficiently in CI, managing model artifacts.  
**MVP:** A pytest suite that hits a model endpoint and asserts behavioral constraints.  
**Evolution:** MVP -> Integrate with MLflow -> GitHub Actions gate -> Auto-rollback via Ray Serve API -> Synthetic data generation for new tests.  
**Learning:** LLMOps, CI/CD at depth, behavioral testing.  
**Made With ML connection:** CI/CD -> GitHub Actions; Evaluation -> Offline eval.  
**Beyond Made With ML:** Automated rollback logic, behavioral test generation, production gating.  
**Portfolio:** Strong MLOps signal, but less "ML engineering".  
**Research:** How many behavioral tests are needed to guarantee a regression threshold?  
**Open source:** CI/CD templates for LLMs.  
**Compute:** Yes.  
**Difficulty:** 6/10  
**Time:** 4 weeks

#### 8. Speculative Decoding Engine

**Problem:** LLM inference decode phase is memory-bandwidth bound. Speculative decoding uses a tiny draft model to propose tokens verified by the target model, cutting latency.  
**Users:** Inference teams.  
**Technical core:** Draft-target token verification, acceptance sampling, KV-cache alignment.  
**Engineering core:** Orchestrating two models of different sizes in a single forward pass loop.  
**Systems core:** Memory management for two models, parallel verification, latency profiling.  
**MVP:** Implement speculative decoding in pure PyTorch for a 0.5B target and 0.1B draft model.  
**Evolution:** MVP -> Add benchmark harness -> Measure acceptance rates -> Compare against vLLM vanilla decoding.  
**Learning:** Deep inference internals, low-level PyTorch, probability (acceptance distributions).  
**Made With ML connection:** Tangential. Maybe CI/CD for testing correctness.  
**Beyond Made With ML:** Entirely beyond. This is low-level systems/ML.  
**Portfolio:** Elite inference engineering signal.  
**Research:** Optimal draft/target size ratios for low-resource languages.  
**Open source:** Pedagogical speculative decoding implementation.  
**Compute:** Yes, with tiny models (e.g., TinyLlama).  
**Difficulty:** 9/10  
**Time:** 6 weeks  
_(Note: Since you are already building a mini-inference engine, this overlaps too much with your current work)._

#### 9. Synthetic Data Generation & Validation Factory

**Problem:** Fine-tuning requires data, but human annotation is slow. LLM-generated data is noisy and can lead to model collapse if not rigorously filtered.  
**Users:** LLM Engineers doing SFT/Alignment.  
**Technical core:** Self-instruct pipelines, diversity metrics, LLM-as-a-judge filtering, deduplication via embeddings.  
**Engineering core:** Asynchronous pipeline for high-throughput API calls, cost tracking, data versioning (DVC).  
**Systems core:** Concurrent API requests, embedding deduplication at scale.  
**MVP:** Script that generates instructions, filters them via a judge, saves to JSONL.  
**Evolution:** MVP -> Add DVC -> Distributed generation (Ray) -> Automated quality dashboards -> SFT loop integration.  
**Learning:** Data-centric AI, distributed systems, DVC, prompt engineering.  
**Made With ML connection:** Data -> Versioning and processing; CI/CD -> Validating data schema.  
**Beyond Made With ML:** Data generation taxonomies, embedding-based dedup, data quality metrics.  
**Portfolio:** Good data engineering signal.  
**Research:** Impact of judge-model bias on synthetic data quality.  
**Open source:** Synthetic data pipeline template.  
**Compute:** Yes, mostly API calls.  
**Difficulty:** 6/10  
**Time:** 4-5 weeks

#### 10. LLM Guardrails & Input/Output Security System

**Problem:** Deploying LLMs safely requires preventing PII leakage, prompt injections, and toxic outputs in real-time.  
**Users:** AI Engineers deploying customer-facing apps.  
**Technical core:** Regex + NER for PII, embedding classifiers for injections, rule-based output validation.  
**Engineering core:** Non-blocking I/O in the serving layer, chaining validators, fallback strategies.  
**Systems core:** Latency overhead of sequential validators, async streaming validation.  
**MVP:** FastAPI middleware that intercepts prompts, runs regex/embedding checks, blocks/redsacts.  
**Evolution:** MVP -> Add NeMo Guardrails concepts -> Vector DB for injection detection -> Streaming redaction -> Observability logging.  
**Learning:** NLP security, middleware design, streaming architecture.  
**Made With ML connection:** Serving -> API design; Observability -> Logging failures.  
**Beyond Made With ML:** Security NLP, real-time streaming interception.  
**Portfolio:** Niche but highly practical for AI Engineers.  
**Research:** Embedding space separation of prompt injections vs benign prompts.  
**Open source:** Guardrails middleware library.  
**Compute:** Yes.  
**Difficulty:** 6/10  
**Time:** 4 weeks

---

### Part 3: Comparison of Candidates

|Criterion|1. Alignment & Eval System|2. Eval Platform|3. Semantic Cache Proxy|4. Auto-Quant Pipeline|5. Agent Tracing|6. RAG Optimization|7. Auto-Rollback CI|8. Spec Decoding|9. Synthetic Data|10. Guardrails|
|---|---|---|---|---|---|---|---|---|---|---|
|ML engineering depth|9|8|5|7|4|6|5|10|6|5|
|LLM engineering relevance|10|9|7|8|8|8|7|9|9|7|
|Systems engineering depth|8|7|9|7|6|5|5|10|6|6|
|MLOps/LLMOps value|10|8|7|9|8|6|10|4|8|6|
|Technical difficulty|8|7|7|6|6|5|6|9|6|6|
|Learning value|9|8|7|7|6|6|7|10|6|6|
|Real-world usefulness|9|9|8|8|9|8|8|6|8|8|
|Portfolio value|10|8|7|7|8|7|7|10|6|6|
|Research potential|8|6|4|5|4|4|5|8|5|4|
|Open-source potential|8|9|8|7|9|7|7|7|7|8|
|Feasibility for one person|8|9|8|9|8|9|9|6|8|9|
|Compute feasibility (4GB)|8|9|10|8|10|10|10|7|10|10|
|Long-term extensibility|9|9|8|7|8|7|7|6|7|7|
|Evolves w/ Made With ML|10|9|7|9|6|8|10|3|8|6|

**Trade-offs Explanation:**

- Candidate 8 (Spec Decoding) and your current inference engine project represent the absolute peak of systems difficulty, but they connect poorly to Made With ML's MLOps curriculum. You are already doing this; doing it twice wastes the MWMl curriculum.
- Candidates 2, 6, 9 are highly useful but slightly lack the "deep ML" rigor—they lean more towards software engineering or data processing.
- Candidate 1 (Alignment & Eval System) is the ultimate crucible for an LLM Engineer. It forces you to grapple with the hardest ML problem (alignment/DPO), the hardest ops problem (LLM evaluation), and maps perfectly onto MWMl's MLOps backbone while pushing you past its limits. It directly complements your low-level inference engine work: your inference engine handles _how_ the model generates tokens; this project handles _what_ tokens it generates, _why_, and _how we prove it's good_.

---

### Part 4: The Decision

> **Build the End-to-End LLM Alignment & Evaluation System.**

1. **Why it is the best project for my career goal:** To be an LLM Engineer, you must do more than call APIs or serve models. You must be able to improve models (alignment) and rigorously prove they improved (evaluation). This project hits the exact intersection of deep learning (DPO), systems (vLLM, QLoRA memory limits), and LLMOps (CI/CD, MLflow).
2. **Why it is better than the other candidates:** It leverages everything you already know (DPO concepts, vLLM, FastAPI, Docker) but forces you to operationalize them. Unlike the semantic cache or guardrails (which are mostly middleware software engineering), this requires deep ML understanding of loss functions, gradient memory, and statistical evaluation. Unlike the Eval Platform alone, this gives you an actual model artifact to serve and track.
3. **What engineering skills it will force me to develop:** QLoRA memory profiling on a 4GB GPU, asynchronous LLM-as-a-judge orchestration, statistical significance testing for LLM metrics, CI/CD pipeline design for model training, and vLLM adapter merging/serving.
4. **What I will know how to build after completing it that I cannot confidently build today:** A fully automated, reproducible pipeline that ingests preference data, fine-tunes a model under extreme compute constraints, runs an automated LLM-judge eval suite, registers the model if it passes a statistical threshold, and deploys it via vLLM—all triggered by a Git commit.
5. **Why it is worth having on my GitHub:** It proves you understand the entire modern LLM post-training lifecycle. A recruiter seeing a repo that handles DPO + LLM-as-a-Judge + vLLM + CI/CD knows you are ready for an LLM Engineer role, not just an entry-level ML role.

---

### Part 5: Project Design

#### Architecture

text

```
Preference Data (JSONL)
↓
Data Validation & Versioning (DVC / Git LFS)
↓
DPO/QLoRA Training (0.5B/1.5B base model, 4-bit quantized, paged_adamw_8bit optimizer)
↓
Experiment Tracking (MLflow: loss curves, KL divergence, hyperparams)
↓
Automated Evaluation (LLM-as-a-Judge via API, statistical significance vs baseline)
↓
Model Registry (MLflow: transition to "Staging" if eval > threshold)
↓
Serving (Merge LoRA adapter -> Quantize to AWQ -> vLLM backend behind FastAPI)
↓
Observability (Token usage logging, latency tracking, prompt-drift monitoring via Prometheus/Grafana or simple SQLite logger)
↓
CI/CD (GitHub Actions: lint -> test -> train -> eval -> conditional deploy)
```

#### Repository structure

text

```
llm-alignment-system/
├── .github/workflows/
│   └── ci_cd_pipeline.yml          # Lint, test, trigger training on data update
├── configs/
│   ├── train_config.yaml           # DPO hyperparams, LoRA config
│   └── eval_config.yaml            # Judge model, metrics, thresholds
├── data/
│   ├── raw/                        # Versioned preference data
│   └── processed/                  # Formatted for DPO
├── src/
│   ├── data/
│   │   └── prepare.py              # Load, validate, format preference pairs
│   ├── train/
│   │   ├── dpo_trainer.py          # QLoRA + DPO logic (TRL)
│   │   └── memory_utils.py         # 4GB VRAM optimizations
│   ├── eval/
│   │   ├── llm_judge.py            # Async LLM-as-a-judge logic
│   │   ├── metrics.py              # Win rate, KL div, statistical tests
│   │   └── runner.py               # Orchestrates eval suite
│   ├── serve/
│   │   ├── merge_quant.py          # Merge LoRA -> AWQ -> vLLM
│   │   ├── api.py                  # FastAPI streaming endpoint
│   │   └── vllm_engine.py          # vLLM initialization
│   └── observability/
│       └── logger.py               # Middleware for logging requests/responses
├── tests/
│   ├── test_data.py
│   ├── test_train.py               # Asserts loss decreases on tiny dataset
│   └── test_eval.py
├── notebooks/
│   └── exploration.ipynb
├── .gitignore
├── Dockerfile
├── requirements.txt
└── README.md
```

#### Development stages

**Stage 1 — MVP**

- Take a small open-source preference dataset (e.g., 1,000 rows).
- Write a PyTorch script using TRL's `DPOTrainer` with a 0.5B model (Qwen2.5-0.5B) using bitsandbytes 4-bit.
- Run a basic LLM-as-a-judge script using an API (Groq/OpenAI) to compare base vs. tuned model on 50 samples.
- _Constraint:_ Must run without OOM on RTX 3050 4GB.

**Stage 2 — Engineering foundation**

- Add `pytest` to validate data schemas and assert that the training loop actually reduces loss.
- Introduce `hydra` for configuration management (train_config.yaml).
- Integrate MLflow to log DPO hyperparameters, loss curves, and KL divergence metrics.
- Add DVC to version the `data/raw` JSONL files.

**Stage 3 — Productionization**

- Build the async evaluation engine: concurrent API calls to the judge, calculation of win-rate confidence intervals.
- Build the deployment module: Merge the LoRA adapter, convert to AWQ, and serve via vLLM locally.
- Wrap vLLM in a FastAPI app with Server-Sent Events (SSE) for token streaming.
- Dockerize the serving application.

**Stage 4 — Advanced engineering**

- Build a GitHub Actions CI/CD pipeline: On PR to `main`, run tests. On merge to `main` (if `data/` changed), trigger the full training run on a self-hosted/cloud runner.
- Implement automated model registration: if win-rate > 55% with p < 0.05, transition to "Staging" in MLflow.
- Add observability middleware to the FastAPI app: log prompt length, generation time, and token counts to a local SQLite DB or Prometheus endpoint.

**Stage 5 — Research/open-source version**

- Investigate: "Does 4-bit QLoRA DPO degrade performance differently across languages?" (Using your Darija/AlgerianMMLU context if desired).
- Abstract the eval engine into a standalone CLI: `llm-eval --model <endpoint> --dataset <file>`.
- Publish the CI/CD pipeline template and memory-optimization tricks as a public good.

---

### Part 6: Synchronization with Made With ML

Here is how the project evolves alongside the Made With ML curriculum, explicitly distinguishing what is learned from the course, applied to the project, and learned independently.

text

```
Made With ML topic: Design
        ↓
Learned from MWMl: Translating product requirements into ML objectives; defining online vs offline metrics.
        ↓
Applied to the project: Define the objective: "Reduce toxicity/maintain helpfulness using DPO under 4GB VRAM." Define offline metric (LLM-judge win rate) and online metric (latency under vLLM).
        ↓
Learned independently: DPO-specific objective math (preference loss, KL divergence penalty to reference model).
        ↓
Project milestone: Project charter and eval rubric defined.

Made With ML topic: Data
        ↓
Learned from MWMl: Data preprocessing, splitting, and versioning using DVC/manifests.
        ↓
Applied to the project: Write `src/data/prepare.py` to load JSONL, tokenize chosen/rejected pairs, and version the dataset with DVC.
        ↓
Learned independently: Handling preference data edge cases (e.g., chosen == rejected, token length truncation for KV cache limits, attention masking for paired sequences).
        ↓
Project milestone: Reproducible data pipeline. `dvc repro` generates the training-ready dataset.

Made With ML topic: Modeling (Experiment Tracking)
        ↓
Learned from MWMl: MLflow integration, logging params/metrics/artifacts, PyTorch training loop structure.
        ↓
Applied to the project: Integrate MLflow into the DPO trainer. Log LoRA rank, learning rate, DPO beta, and loss curves.
        ↓
Learned independently: QLoRA memory engineering on 4GB. Using `paged_adamw_8bit`, gradient accumulation, targeting specific modules (q_proj, v_proj) to fit 0.5B-1.5B models in 4GB. Logging KL divergence as a health metric.
        ↓
Project milestone: A working DPO training run that completes on the 3050 4GB and logs to MLflow. (Tested via pytest: assert loss decreases).

Made With ML topic: Evaluation
        ↓
Learned from MWMl: Offline evaluation workflows, slicing, behavioral testing.
        ↓
Applied to the project: Build an eval suite of 100 prompts. Slice by prompt type (coding, creative, toxic). Run base model and DPO model.
        ↓
Learned independently: LLM-as-a-Judge engineering. Async concurrent API calls to the judge model. Position bias mitigation (swapping chosen/rejected order in the judge prompt). Calculating statistical significance (bootstrapping confidence intervals for win rates).
        ↓
Project milestone: Automated eval script that outputs a statistical report comparing model versions.

Made With ML topic: Optimization
        ↓
Learned from MWMl: Quantization, pruning, hardware awareness.
        ↓
Applied to the project: Post-training, merge the LoRA adapter into the base model weights. Apply AWQ 4-bit quantization to the merged model for serving.
        ↓
Learned independently: The difference between QLoRA (training) and AWQ/GPTQ (serving). Measuring the perplexity/quality drop from the AWQ conversion. vLLM memory budgeting (how much VRAM for weights vs KV cache on a 4GB card).
        ↓
Project milestone: A quantized, merged model artifact ready for production serving.

Made With ML topic: Serving
        ↓
Learned from MWMl: FastAPI, Ray Serve, batching, microservices.
        ↓
Applied to the project: Serve the AWQ model using vLLM as the backend engine. Wrap it in a FastAPI application with an OpenAI-compatible `/v1/chat/completions` endpoint.
        ↓
Learned independently: vLLM engine parameters (`gpu_memory_utilization`, `enforce_eager` for small GPUs). Implementing Server-Sent Events (SSE) for real-time token streaming. Handling vLLM's continuous batching under the hood.
        ↓
Project milestone: Live local endpoint serving the custom-aligned model with streaming responses.

Made With ML topic: LLMOps / CI/CD
        ↓
Learned from MWMl: GitHub Actions, testing workflows, continuous training pipelines.
        ↓
Applied to the project: Write `ci_cd_pipeline.yml`. On push: lint (ruff), test (pytest). On data update: trigger cloud GPU runner to execute training -> eval.
        ↓
Learned independently: Conditional deployment logic. Writing a script that queries MLflow model registry: if the new model is in "Staging", trigger a Docker rebuild and restart the vLLM serving container. Implementing automated rollback if the container fails health checks.
        ↓
Project milestone: Fully automated loop. Update data -> train -> eval -> register -> deploy.

Made With ML topic: Observability
        ↓
Learned from MWMl: Drift detection, data quality monitoring, logging.
        ↓
Applied to the project: Add middleware to the FastAPI app to log every request's prompt length, generation length, and latency.
        ↓
Learned independently: LLM-specific observability. Logging token/sec efficiency. Setting up a basic dashboard (Grafana or Streamlit) to monitor vLLM KV-cache utilization and OOM risks in real-time.
        ↓
Project milestone: Production system is fully instrumented and observable.
```

### Final Objective Realized

If you complete this, you will not be someone who "did a DPO tutorial." You will be an engineer who took a mathematical alignment concept (DPO), forced it to run on severely constrained hardware (QLoRA on 4GB), proved it worked via rigorous statistics (LLM-as-a-Judge), packaged it for production (AWQ + vLLM + FastAPI), and automated the entire lifecycle (MLflow + GitHub Actions).

You will have built the exact infrastructure that modern AI companies are desperately trying to build right now, and you will deeply understand every layer of the stack.

Assistant B

After inspecting Made With ML, its real curriculum is: **ML system design → feature stores / data versioning → training pipelines (Experiment tracking, hyperparam tuning) → deployment patterns (batch/real-time) → CI/CD for ML → model monitoring & drift detection → basic MLOps tooling**. It teaches _workflow discipline_, not deep inference mechanics. It does NOT cover: KV-cache design, continuous batching, paged attention, quantization evaluation, LLM-specific evaluation harnesses, streaming inference, or load-testing inference endpoints. That gap is exactly where your project must live.

Given your existing inference-engine work (KV cache, prefill/decode, RoPE, batching) and your goal (ML/LLM Engineer), here are 10 serious candidates.

---

## 8 Serious Candidates

### 1. InferenceCore — Benchmark-Driven Inference Pipeline

**Problem:** You can’t optimize what you don’t measure. Most inference projects skip workload-defined benchmarking.  
**Users:** ML Engineers deploying LLMs; yourself for interview narratives.  
**Technical core:** Inference correctness + throughput/latency trade-offs.  
**Engineering core:** Your existing KV-cache engine + continuous batching logic + vLLM comparison + quantization evaluation + FastAPI streaming server.  
**Systems core:** Memory fragmentation (paged KV concept), GPU saturation, load testing.  
**MVP:** Your current engine generating correctly with KV cache + a benchmark harness (TTFT/ITL) vs vLLM on a quantized 7B on T4/cloud.  
**Evolution:** Add paged-block concept → quantized serving (GPTQ/AWQ) with quality evaluation on your own benchmark → observability (token latency logging) → public benchmark suite.  
**Learning:** Inference optimization, quantization trade-offs, production serving patterns, honest gap analysis.  
**MwML connection:** Data versioning (dataset/benchmark versioning), experiment tracking (MLflow for config/results), CI (automated benchmark tests), monitoring (latency metrics).  
**Beyond MwML:** KV-cache correctness bugs, RoPE offset debugging, workload-spec rigor, quantization quality evaluation on low-resource language, vLLM internal architecture.  
**Portfolio:** "Built and benchmarked a custom inference component against vLLM with decomposed gap analysis."  
**Research:** Yes — quantization fairness / degradation measurement on dialect.  
**Open source:** Yes — benchmark harness reusable.  
**Compute:** RTX 3050 4GB for small quantized models (4-bit 7B); T4 for vLLM.  
**Difficulty:** 7/10. **Time:** 6–8 weeks.

### 2. AlgerianMMLU + LLM Evaluation Platform

**Problem:** No standardized evaluation infra for your specific domain (Darija/low-resource).  
**Users:** Researchers; your own fine-tuning pipeline.  
**MVP:** Automated eval harness with versioned prompts, metric tracking, regression tests for fine-tuned models.  
**Evolution:** Multi-task evaluation, statistical significance testing, human-in-the-loop comparison UI.  
**Beyond MwML:** LLM-as-a-judge design, evaluation bias, statistical rigor.  
**Difficulty:** 5/10. **Time:** 4–5 weeks. (Too narrow; misses systems/inference skills you need.)

### 3. Synthetic Data & Preference Pipeline for Alignment

**Problem:** Building DPO/alignment data requires quality control and versioning.  
**Users:** Your own training pipeline.  
**Engineering core:** Data generation, filtering, versioning, DPO training tracking.  
**Beyond MwML:** Synthetic data quality metrics, preference dataset curation.  
**Difficulty:** 6/10. **Time:** 5 weeks. (Good but misses serving/inference which is your career center.)

### 4. Production RAG with Retrieval Evaluation & Observability

**Problem:** RAG fails silently; retrieval quality degrades.  
**Engineering core:** Chunking pipeline, embedding versioning, retrieval metrics (MRR, recall@k), generation evaluation.  
**Beyond MwML:** Embedding model updates, retrieval monitoring.  
**Difficulty:** 6/10. **Time:** 5 weeks. (Useful, but you already know RAG; this is less differentiated.)

### 5. Agent Execution Infrastructure & Tool-Use Monitoring

**Problem:** Agents are unreliable; need execution tracing and failure recovery.  
**Engineering core:** Tool registry, execution graph, state versioning, error classification.  
**Beyond MwML:** Agent state machines, tool-use evaluation.  
**Difficulty:** 7/10. **Time:** 6 weeks. (Interesting, but further from your current engine work.)

### 6. Multimodal Inference & Serving Pipeline

**Problem:** Serving vision-language models requires different batching (image tokens vary wildly).  
**Engineering core:** Variable-length image token handling, multimodal KV-cache considerations.  
**Difficulty:** 8/10. **Time:** 8 weeks. (Too compute-heavy for 4GB; high risk.)

### 7. Model Experimentation Platform (MLOps Deep)

**Problem:** Reproducible LLM experiments with hyperparameter tracking, model registry, A/B deploy logic.  
**Users:** Team ML engineers.  
**Engineering core:** Experiment tracking, artifact storage, model registry, deployment gating.  
**Beyond MwML:** LLM-specific registry (adapter weights, LoRA versions), prompt versioning.  
**Difficulty:** 6/10. **Time:** 6 weeks. (Too generic; sounds like "FastAPI + MLflow + Docker.")

### 8. Training Optimization & Mixed-Precision Pipeline

**Problem:** Efficient fine-tuning on limited GPU.  
**Engineering core:** LoRA/QLoRA, gradient checkpointing, mixed precision tracking.  
**Beyond MwML:** Memory profiling, optimizer state management.  
**Difficulty:** 5/10. **Time:** 4 weeks. (Too training-focused; you want serving/inference.)

### 9. LLM Observability & Reliability System

**Problem:** Serving failures, latency spikes, and output degradation happen without visibility.  
**Engineering core:** Log aggregation, token-level latency traces, drift detection for outputs.  
**Beyond MwML:** LLM-specific telemetry, prompt injection detection.  
**Difficulty:** 6/10. **Time:** 5 weeks. (Good complement, but needs an engine to observe first.)

### 10. Inference Micro-Optimization: Speculative Decoding + CUDA Graphs

**Problem:** Decode latency dominates.  
**Engineering core:** Draft model, verification logic, CUDA graph capture for static shapes.  
**Beyond MwML:** Custom CUDA kernels, graph capture debugging.  
**Difficulty:** 9/10. **Time:** 10+ weeks. (Too ambitious for solo + 4GB; should be Phase 2.)

---

## Comparison Table

|Criterion|1 InferenceCore|2 Eval Platform|3 SynData|4 RAG|5 Agents|6 Multimodal|7 Exp Platform|8 Training Opt|9 Observability|10 SpecDec|
|---|---|---|---|---|---|---|---|---|---|---|
|ML Eng Depth|5|3|3|3|4|4|3|3|3|5|
|LLM Eng Rel|5|4|4|4|3|4|2|3|3|5|
|Systems Depth|5|2|2|2|3|4|2|3|3|5|
|MLOps/LLMOps|4|4|4|4|3|3|5|3|5|2|
|Difficulty|7|5|6|6|7|8|6|5|6|9|
|Learning Value|5|3|3|3|4|4|2|3|3|4|
|Real-World Use|5|3|3|4|3|3|3|2|4|3|
|Portfolio Value|5|3|2|3|3|2|2|2|3|4|
|Research Potential|4|4|2|2|3|3|1|2|2|3|
|Open Source|4|4|3|3|3|2|3|2|3|3|
|Feasibility (Solo)|4|5|5|5|4|2|4|5|4|2|
|Compute Feas|4|5|4|4|4|2|5|5|4|2|
|Long-Term Ext|5|3|3|3|4|3|3|2|3|3|
|MwML Sync|4|4|4|4|3|3|5|3|5|2|

---

## The Decision

> **Build Project 1: InferenceCore — A Benchmark-Driven LLM Inference & Serving System.**

This is the only project on your list that:

- Directly extends your _current_ engine work (KV cache, prefill/decode, batching) rather than starting over.
- Forces you to confront **real engineering problems**: correctness vs speed, workload specification rigor, honest gap analysis, quantization quality measurement.
- Produces a **portfolio centerpiece** that demonstrates both "I can build inference mechanisms" and "I understand production serving."
- Naturally integrates Made With ML’s workflow concepts (experiment tracking, CI, monitoring, deployment) without being a generic "MLflow + Docker" demo.
- Is feasible on RTX 3050 4GB + occasional T4 (using 4-bit quantized 7B models).

The others either miss the systems/inference core (2, 3, 7, 8), are too far from your current trajectory (5, 6), or are too narrow to justify months of solo work (2, 9).

---

## Why InferenceCore Wins

1. **Career alignment:** ML/LLM Engineers are hired for inference, serving, and evaluation, not for "another chatbot." This builds the resume line every inference team wants.
2. **Engineering depth:** You don’t just "use vLLM" — you build enough of the mechanism yourself (KV-cache logic, batching scheduler concept, benchmark harness) that you can explain _why_ vLLM wins.
3. **Not from scratch where unnecessary:** You use your existing model and engine as the base; you integrate vLLM for production comparison rather than rewriting CUDA kernels.
4. **Forces new skills:** Workload specification, TTFT/ITL measurement, quantization evaluation on your own benchmark, streaming serving, load testing with Locust, observability of token-level metrics.

---

## What You Will Know How to Build After

Today you can build: a model with KV-cache and basic generation.

After InferenceCore you can confidently build:

- A **correct inference benchmark** with defined workload specs (the skill every senior engineer demands).
- A **comparison study** between custom and production serving systems with decomposed explanations.
- A **quantized serving endpoint** with quality evaluation (not just "it runs").
- A **streaming inference API** with basic latency observability.
- You will have touched **every layer** of modern LLM serving: data (benchmark datasets), model (quantized weights), inference (engine + vLLM), evaluation (quality metrics), serving (FastAPI + SSE), infrastructure (Docker/load testing), and monitoring (latency logs).

---

## Project Design: InferenceCore

### Architecture

text

```
Data / Benchmark Specs
  ↓ (versioned prompt sets, workload definitions)
Custom Inference Engine (Your Work)
  ↓ (KV cache, prefill/decode split, batching scheduler concept)
Benchmark Harness
  ↓ (TTFT, ITL p50/p95, throughput @ batch 1/8/16, workload spec logged)
vLLM Production Reference
  ↓ (quantized 7B, openai-compatible endpoint, Locust load test)
Quantization & Quality Evaluation
  ↓ (GPTQ/AWQ vs bf16, evaluated on your benchmark + perplexity/quality)
Serving Layer
  ↓ (FastAPI + SSE streaming, token-level logging)
Observability / Logging
  ↓ (latency per phase, error tracking, benchmark regression alerts)
Deployment / CI
  ↓ (Docker, automated benchmark tests, artifact storage)
```

Only justified components included. No Kubernetes. No distributed training cluster.

### Repository Structure

text

```
inference-core/
├── engine/
│   ├── kv_cache.py          # Your existing logic, hardened
│   ├── scheduler.py          # Continuous/admit-evict logic (conceptual implementation)
│   ├── forward.py            # Prefill/decode split, RoPE, sampling
│   └── correctness_tests.py  # Bit-identical cached vs uncached
├── bench/
│   ├── harness.py            # Concurrent request generator
│   ├── metrics.py            # TTFT, ITL p50/p95, throughput
│   └── workload_specs/       # JSON specs: model, dtype, lengths, hardware
├── vllm_ref/
│   ├── serve_vllm.py         # Launch quantized model
│   ├── load_test_locust.py   # Locust file for saturation curve
│   └── gap_analysis.md       # "Why vLLM wins" decomposed writeup
├── quant_eval/
│   ├── quantize.py           # GPTQ/AWQ pipeline
│   ├── quality_eval.py       # Benchmark quality + perplexity
│   └── tradeoff_table.md     # bf16 vs quant results
├── serve/
│   ├── api_server.py         # FastAPI + SSE streaming
│   └── telemetry.py          # Token latency, error logging
├── experiments/              # MLflow runs for each benchmark/config
├── data/
│   └── benchmark_prompts/    # Versioned evaluation prompts
├── docker/
│   ├── Dockerfile.engine
│   └── docker-compose.yml
├── .github/workflows/        # CI: correctness tests + benchmark regression
└── README.md                 # Results-first: chart, workload spec, key numbers
```

### Development Stages

**Stage 1 — MVP (Weeks 1–2):**

- Harden your KV-cache engine: correctness tests, prefill/decode split, sampling.
- Implement benchmark harness: single/multi-request, measure TTFT and ITL.
- Define your workload spec (hardware, model, prompt/output length distribution).

**Stage 2 — Engineering Foundation (Weeks 3–4):**

- Add a scheduling concept (admit/evict loop for continuous batching, even if simple).
- Implement paged-block memory concept (block pool, logical→physical mapping).
- Add CI (pytest for correctness, automated benchmark run).

**Stage 3 — Productionization (Weeks 5–6):**

- Deploy quantized 7B with vLLM on T4/cloud.
- Run Locust load-test; capture saturation curve.
- Write honest gap analysis mapping vLLM scheduler/block manager to your code.

**Stage 4 — Advanced Engineering (Weeks 7–8):**

- Quantize your fine-tuned model (GPTQ + AWQ); evaluate quality on your benchmark + perplexity.
- Build FastAPI streaming server (SSE) for the quantized model.
- Add telemetry (per-token latency, error tracking).

**Stage 5 — Research/Open-Source (Ongoing):**

- Publish benchmark harness as reusable tool.
- Write the canonical analysis: "Mini Engine vs vLLM: A Decomposed Gap Analysis."
- Optional: investigate quantization degradation specifically on Darija vs English.

---

## Synchronization with Made With ML

text

```
Made With ML Topic
        ↓
What I Learn
        ↓
What I Implement
        ↓
What I Test
        ↓
Engineering Problem I Hit
        ↓
What I Research
        ↓
Project Milestone
```

**Module: ML Design & Data Strategy**

- Learn: Feature importance, data leakage, dataset versioning.
- Implement: Version benchmark prompt datasets in `data/benchmark_prompts/`; log dataset hashes with MLflow.
- Test: Benchmark regression when dataset version changes.
- Problem: Benchmark results change with prompt distribution shifts.
- Research: Workload specification standards.
- Milestone: Reproducible benchmark spec.

**Module: Development — Experiment Tracking**

- Learn: MLflow tracking (params, metrics, artifacts).
- Implement: Log every benchmark run: model config, quant method, batch size, TTFT/ITL/throughput.
- Test: Can I reproduce last week’s numbers?
- Problem: Config drift in manual runs.
- Research: Reproducible ML practices for inference.
- Milestone: Every result is reproducible from MLflow run.

**Module: CI/CD for ML**

- Learn: Automated testing, build pipelines.
- Implement: GitHub Actions running correctness tests + quick benchmark sanity check on PR.
- Test: Does new commit break inference correctness?
- Problem: Slow benchmark tests in CI.
- Research: CI strategies for GPU tests.
- Milestone: Automated regression testing.

**Module: Deployment — Real-Time Serving**

- Learn: API patterns, containerization, scaling basics.
- Implement: FastAPI container with streaming; Docker Compose for local serving.
- Test: Load-test with Locust; measure saturation.
- Problem: Streaming buffers incorrectly or latency spikes under load.
- Research: Streaming protocols, load testing patterns.
- Milestone: Deployed streaming endpoint.

**Module: Monitoring & Drift**

- Learn: Data drift, model performance monitoring.
- Implement: Token-level latency logging; error rate tracking; benchmark quality tracking over time.
- Test: Alert when ITL p95 exceeds threshold or quality drops.
- Problem: Inference quality degrades with quantization or data shift.
- Research: LLM output monitoring, quantization drift.
- Milestone: Basic observability dashboard/logs.

**Module: Feature Stores / Data Pipelines (Conceptual)**

- Learn: Data versioning, feature storage.
- Implement: Not a feature store, but benchmark prompt versioning and model artifact versioning.
- Test: Can I retrieve exact quantized model + benchmark version?
- Milestone: Artifact traceability.

### Learned Independently (Beyond Made With ML)

- **KV-cache correctness debugging:** Position ID / RoPE offset errors after decode step 1.
- **Roofline analysis:** Understanding compute-bound prefill vs bandwidth-bound decode.
- **Workload specification rigor:** Why "4× faster" is meaningless without specs.
- **Quantization evaluation:** Measuring quality loss, not assuming it’s free.
- **vLLM architecture mapping:** Reading scheduler/block manager and comparing to your implementation.
- **Inference latency vocabulary:** TTFT vs ITL vs throughput — and why they trade off.
- **Streaming serving:** Server-Sent Events for token-level delivery.

---

## Final Objective Statement

After completing InferenceCore alongside Made With ML, you will not say "I did the course and built a project." You will say:

> "I designed an inference benchmark with defined workload specifications, implemented and verified KV-cache mechanisms, built a scheduling concept, benchmarked my system honestly against vLLM with a decomposed gap analysis, evaluated quantization quality on my own evaluation pipeline, served a quantized model with streaming and telemetry, and automated correctness and regression testing in CI. I understand the full system from data specification through serving to monitoring — and I know exactly where production systems win and why."

That is the ML/LLM Engineer profile. Build this.

I want you to help me choose ONE serious project to build while studying Made With ML:

[https://madewithml.com/](https://madewithml.com/)

But first, understand my actual goal.

## My career goal

I am specifically aiming to become an:

- ML Engineer
- AI Engineer
- LLM Engineer

I am NOT trying to become a generalist across every ML domain.

Therefore, do NOT recommend projects in unrelated areas such as:

- recommendation systems
- time-series forecasting
- generic anomaly detection
- generic tabular ML
- classical ML projects
- unrelated computer vision projects

unless there is a very strong reason why the project directly develops skills needed by an ML/AI/LLM Engineer.

The project should primarily live in the intersection of:

- ML engineering
- deep learning systems
- LLM engineering
- LLMOps / MLOps
- model training and fine-tuning
- model evaluation
- inference
- model serving
- inference optimization
- data pipelines for ML/LLMs
- experimentation
- model/data versioning
- AI infrastructure
- reliability and observability
- deployment
- performance engineering
- AI agents and agent infrastructure
- RAG systems, when there is a meaningful engineering problem
- multimodal systems, when appropriate
- other areas directly relevant to modern ML/LLM engineering

Do not assume that the project must be an LLM application. A low-level ML systems project, inference project, evaluation system, training infrastructure project, or model-serving system may be better.

The goal is to make me a **much stronger ML/LLM engineer**, not simply to produce an impressive-looking project.

---

# My current background

I am an M2 Statistics & Data Science student.

I already have experience with:

- Python
- NumPy
- Pandas
- scikit-learn
- PyTorch
- Hugging Face / Transformers
- NLP
- LLMs
- RAG
- DPO / alignment
- FastAPI
- Docker
- Git/GitHub
- pytest
- basic MLflow
- basic CI/CD
- vLLM / LLM inference concepts

I am currently also working on an inference-engine project involving concepts such as:

- KV cache
- prefill/decode
- position IDs
- RoPE
- inference correctness
- batching
- eventually performance optimization

I have limited compute:

- RTX 3050 4GB locally
- occasional free/low-cost cloud GPU access

Do not assume that my project must use all of these existing skills. I want the project to challenge me and teach me things I don't already know.

---

# Why I am studying Made With ML

I want to use Made With ML as a **learning curriculum**, not as a project to copy.

The intended process is:

Made With ML  
↓  
learn an engineering concept  
↓  
apply it to my own project  
↓  
encounter real engineering problems  
↓  
research and solve them  
↓  
test the solution  
↓  
continue through the curriculum  
↓  
end with a serious ML/AI system

Therefore, the project must be something that is genuinely worth building **even if Made With ML did not exist**.

I do NOT want:

- a clone of the Made With ML project
- a toy MLOps project
- "FastAPI + Docker + MLflow" with no meaningful problem
- a generic chatbot
- a simple RAG chatbot
- a basic fine-tuning demo
- a project whose only purpose is to demonstrate tools
- unnecessary Kubernetes/distributed systems added just to make it look advanced

The engineering complexity must arise naturally from the problem.

---

# First: inspect Made With ML

Before recommending projects, actually inspect:

[https://madewithml.com/](https://madewithml.com/)

Understand the real curriculum rather than relying on generic knowledge of MLOps.

Identify:

- what the course actually teaches
- the engineering principles behind each topic
- the workflow it promotes
- the tools it uses
- what skills I should have after completing it
- what parts are generalizable to ML/LLM engineering
- what important ML/LLM engineering topics it does NOT cover

Do not simply summarize the website.

I want you to understand what the course is trying to teach so that you can determine how a separate project can serve as my practical laboratory.

---

# Then: find projects

Search specifically within the **ML/AI/LLM engineering space**.

Consider serious projects involving things such as:

- LLM evaluation
- benchmark infrastructure
- LLM data pipelines
- fine-tuning infrastructure
- preference optimization
- synthetic data generation
- model serving
- inference engines
- KV-cache systems
- batching
- speculative decoding
- quantization
- GPU optimization
- model compression
- RAG infrastructure
- retrieval evaluation
- agent infrastructure
- agent evaluation
- multimodal systems
- model experimentation platforms
- ML/LLM observability
- model deployment
- training/inference pipelines
- reproducible ML systems
- ML platforms
- other genuinely relevant ML/AI engineering problems

You may also propose something outside these categories **only if you can demonstrate that it develops highly transferable skills for an ML/LLM Engineer**.

---

# Give me 8–10 serious candidates

For every candidate, explain:

### Problem

What real technical problem does it solve?

### Users

Who would actually use it?

### Technical core

What is the actual ML/AI problem?

### Engineering core

What makes the engineering difficult?

### Systems core

Are there meaningful systems/performance/infrastructure challenges?

### MVP

What is the smallest version that is still a legitimate project?

### Evolution

How could it grow over 3–6 months?

For example:

MVP  
→ robust system  
→ production-oriented system  
→ advanced system

### Learning

What new skills would I gain?

Be specific about:

- ML
- deep learning
- LLMs
- software engineering
- systems
- infrastructure
- MLOps/LLMOps

### Made With ML connection

For each major Made With ML topic:

text

```
Made With ML concept
        ↓
How I would apply it to this project
```

Only claim a connection when it is natural.

### Beyond Made With ML

What would this project force me to learn that Made With ML does not teach?

This is extremely important.

I don't want the course to define the ceiling of the project.

### Portfolio

What would this demonstrate to an ML/LLM engineering recruiter?

### Research

Could it produce a meaningful technical investigation or paper?

### Open source

Could other engineers actually use it?

### Compute

Can I realistically build it with an RTX 3050 4GB and limited cloud GPU access?

### Difficulty

Rate it from 1–10.

### Time

Give a realistic solo-development estimate.

---

# Then compare the candidates

Compare them on:

|Criterion|
|---|
|ML engineering depth|
|LLM engineering relevance|
|Systems engineering depth|
|MLOps/LLMOps value|
|Technical difficulty|
|Learning value|
|Real-world usefulness|
|Portfolio value|
|Research potential|
|Open-source potential|
|Feasibility for one person|
|Compute feasibility|
|Long-term extensibility|
|How naturally it can evolve alongside Made With ML|

Do not simply select the project with the highest number.

Explain the trade-offs.

---

# Then choose ONE

Be decisive.

Tell me:

> **Build X.**

Then explain:

1. Why it is the best project for my career goal.
2. Why it is better than the other candidates.
3. What engineering skills it will force me to develop.
4. What I will know how to build after completing it that I cannot confidently build today.
5. Why it is worth having on my GitHub.

Do not give me "it depends" or "here are several equally good choices."

I want your strongest recommendation.

---

# Then design the project

For the selected project, give me:

## Architecture

Show the complete architecture from:

text

```
data
↓
ML/LLM component
↓
evaluation
↓
training/inference
↓
serving
↓
infrastructure
↓
observability
```

Only include components that are actually justified.

## Repository structure

Give me a realistic production-oriented repository structure.

## Development stages

Define:

### Stage 1 — MVP

The smallest meaningful system.

### Stage 2 — Engineering foundation

Testing, configuration, reproducibility, etc.

### Stage 3 — Productionization

Serving, packaging, deployment, etc.

### Stage 4 — Advanced engineering

Performance, scalability, optimization, etc.

### Stage 5 — Research/open-source version

What could make the project genuinely valuable to the community?

---

# Most importantly: synchronize it with Made With ML

After understanding the actual Made With ML curriculum, create a roadmap like:

text

```
Made With ML topic
        ↓
What I learn
        ↓
What I implement in my project
        ↓
What I test
        ↓
What engineering problem I encounter
        ↓
What I need to research
        ↓
Project milestone
```

Do this for the entire course.

Clearly distinguish:

### Learned from Made With ML

What the course teaches me.

### Applied to the project

What I implement based on that knowledge.

### Learned independently

What the project forces me to learn beyond the course.

This distinction is important because I want the project to **push me beyond the course**, not simply reproduce it.

---

# Final objective

I do NOT want to finish and say:

> "I completed Made With ML and built a project."

I want to finish and be able to say:

> "I can design, implement, test, evaluate, deploy, operate, debug, and optimize a real ML/AI system."

And ideally:

> "I understand not only how to train an LLM/model, but how the complete system around it works."

Optimize the recommendation for **depth of ML/LLM engineering ability**, not for the number of technologies used and not for how impressive the project sounds on paper.

If an apparently simpler project produces substantially deeper engineering learning, choose the simpler project.

the project should be like the one bellow, but for my mlops goal and also my focus is not building from scratch unless its needed. so just to take and idea on the level of the projects i build :

Own Inference: Mini Engine + vLLM

Two tracks. First, build a mini inference engine from scratch — KV cache → continuous batching → paged KV memory → a TTFT/ITL/throughput benchmark harness. Then run the production reality: deploy a quantized 7–8B model with vLLM behind FastAPI, load-test it, and quantify your engine's gap honestly. Inference is where the money is made — this is the resume centerpiece that gets you inference-team interviews.  
Flagship · Essential Month 2 · Weeks 3–4 / 7–8 colab T4 3050 Resume centerpiece  
⛓ This is the capstone that consumes all four prior projects. The Project 1 model + KV cache is what your mini engine wraps — you already own a correct generation loop to build batching on top of. The Project 4 fused RMSNorm/RoPE kernels drop into the engine's forward pass and their roofline mindset transfers directly to inference bottleneck analysis. The vLLM block-table notes from Project 4's M5 are your on-ramp here — you already read the attention backend. And you serve your Project 2 DPO Darija model, evaluated on your Project 6 AlgerianMMLU harness to prove quantization didn't wreck quality. Every prior project pays off here.  
Strategic framing — the mini engine is the differentiator; vLLM is the credibility. Almost no junior can build KV cache → continuous batching → paged memory from scratch. That's the artifact that makes an inference-team recruiter stop scrolling. But building your own engine without ever touching vLLM makes you look naive — so you pair it: build the mini engine to prove you understand the mechanisms, then benchmark against vLLM and explain the gap honestly to prove you know where production wins. Two rules: (1) build the mini engine first, vLLM second — you can't appreciate what vLLM does until you've felt the pain it solves; (2) define your workload before you quote any number — "4× faster" is meaningless without prompt-length distribution, batch size, and hardware. A latency claim without a workload spec is a red flag to anyone senior.  
PART 1  
Engineering Breakdown  
6 milestones · KV cache → batching → paged memory → benchmark → vLLM → quantize+serve  
1KV Cache — The Foundation  
3050W7-a  
Objective

Take Project 1's model and make single-sequence generation efficient with a KV cache. Understand exactly why prefill and decode are fundamentally different phases.

# The two phases that govern everything in inference PREFILL : process the whole prompt at once → COMPUTE-bound (big matmuls, parallel) DECODE : generate 1 token at a time, reusing cached K,V → MEMORY-BANDWIDTH-bound (tiny matmul, but you re-read the whole KV cache every step) # This asymmetry is why TTFT and ITL are separate metrics.

Deliverables

text

```
KV cache reused from/extended out of P1's model — store K,V per layer, append each decode step
Clean separation of prefill (full prompt) vs decode (one token) code paths
Correctness: cached generation must be bit-identical to full-recompute generation (a unit test)
Sampling reused from P1: temperature, top-p, top-k, greedy
```

Skills learned  
KV cache mechanicsprefill vs decodecache append/indexingcausal masking with cachememory-bandwidth intuition  
Common mistakes

text

```
Position IDs / RoPE offsets wrong after the first decode step → garbage after token 1 (the classic KV-cache bug)
Re-processing the whole sequence each step "with a cache" → no speedup, defeats the purpose
Cache not correctly masked → attends to future/padding positions
Not testing cached == uncached → shipping a subtly wrong cache
```

Stretch goals

text

```
Measure the prefill-vs-decode FLOP/byte ratio empirically to see the compute→bandwidth shift
```

OS integration: Project mini-inference-engine. First entry: a Note — "prefill is compute-bound, decode is bandwidth-bound." This framing is your best Architecture Explanation hook.  
2Continuous Batching  
3050W7-b  
Objective

The single most important throughput idea in modern serving. Instead of waiting for a whole batch to finish (static batching), inject new requests and evict finished ones at the token level, keeping the GPU saturated.

# Static vs continuous batching — the throughput unlock STATIC: batch of 8 → all 8 must finish before the next batch starts → short requests wait for the longest one → GPU idles CONTINUOUS: at each decode step, evict finished seqs, admit waiting seqs → GPU always full → 2-4× higher throughput in practice

Deliverables

text

```
A scheduler loop: a running batch of active sequences, a waiting queue, per-step admit/evict logic
Ragged batch handling: sequences at different lengths decoded together (attention masking per sequence)
A simple admission policy (FCFS is fine) + max-batch-size cap
Throughput comparison: static batching vs continuous batching at the same request load
```

Skills learned  
continuous/in-flight batchingrequest schedulingragged batch attentionadmit/evict logicGPU saturationqueue management  
Common mistakes

text

```
Padding all sequences to max length → wastes compute, negates the point of continuous batching
Off-by-one when a sequence finishes mid-step and a new one is admitted → position/cache desync
No max-batch cap → OOM when too many long requests pile up
Confusing continuous batching with dynamic batching (batching at request arrival, not token level)
```

Stretch goals

text

```
Add a simple priority / longest-prompt-first policy and measure the TTFT vs throughput trade-off
```

OS integration: EXP-2026-014 — static vs continuous batching throughput. The "why static batching wastes the GPU" diagram is prime content.  
3Paged KV Memory (block tables)  
3050W7-c  
Objective

Implement PagedAttention's core idea in miniature: store the KV cache in fixed-size blocks (pages) indexed by a block table, instead of one contiguous buffer per sequence. This is what kills memory fragmentation and lets you pack far more concurrent sequences.

# PagedAttention: virtual memory for the KV cache Naive: each seq gets a contiguous max-length KV buffer → huge internal fragmentation (reserve for max, use little) Paged: KV stored in fixed blocks (e.g., 16 tokens each) block_table[seq] = [block_7, block_2, block_19, ...] # logical→physical → near-zero waste, blocks allocated on demand, freed on finish

Deliverables

text

```
A block pool (fixed-size KV blocks) + a block-table mapping logical positions → physical blocks per sequence
Block allocation on growth, block freeing on sequence completion
Attention that gathers K,V across a sequence's (possibly non-contiguous) blocks
Memory-utilization comparison: contiguous vs paged (how many more concurrent seqs fit?)
```

Skills learned  
paged KV / block tableslogical→physical mappingblock allocation/freefragmentation eliminationgather-over-blocks attention  
Common mistakes

text

```
Block size too large → back to fragmentation; too small → block-table overhead. Pick a sane 16–32.
Not freeing blocks on sequence completion → a slow "memory leak" that OOMs after many requests
Gather over blocks with wrong indexing → attends to the wrong KV (silent wrong output)
Trying to match vLLM's optimized CUDA gather — you're building the concept, correctness over speed
```

Stretch goals

text

```
Prefix/block sharing: two requests with the same system prompt share prefill blocks (this is where vLLM's real wins come from)
```

OS integration: Reuse the block-table diagram from Project 4's vLLM reading — now you're implementing what you read. EXP-2026-015 — paged vs contiguous KV, concurrent-seq capacity.  
4Benchmark Harness — The Metrics That Matter  
3050W7-d  
Objective

Measure the metrics inference teams actually report: TTFT (time to first token, dominated by prefill), ITL (inter-token latency, dominated by decode), and throughput (total tokens/sec across all concurrent requests) — across batch 1 / 8 / 32.

# The inference vocabulary you must speak fluently TTFT = latency from request → first output token (prefill cost, UX-critical) ITL / TPOT = time per output token after the first (decode cost) Throughput = total output tokens/sec across all requests (serving efficiency, $$) # The core tension: batch size ↑ → throughput ↑ but per-request latency ↑

Deliverables

text

```
A harness that fires N concurrent requests with a defined prompt-length + output-length distribution
TTFT, ITL (p50/p95), and throughput measured at batch 1 / 8 / 32
The latency-vs-throughput trade-off curve (the single most important inference chart)
A clearly stated workload spec in the README (hardware, model, dtype, prompt/output lengths) — without this, numbers are worthless
```

Skills learned  
TTFT / ITL / throughputp50/p95 latencyconcurrent load generationlatency-throughput trade-offworkload specification  
Common mistakes

text

```
Reporting mean latency only → hides tail; report p95 (tail latency is what users feel)
Quoting throughput without batch size / prompt length → uninterpretable, uncredible
No warmup → first-request compile/allocation skews everything
Measuring TTFT and throughput at the same batch and calling one "the" number — they trade off, show the curve
```

Stretch goals

text

```
Sweep max-batch-size and plot the throughput ceiling + the latency knee (the "operating point" analysis)
```

OS integration: EXP-2026-016 — TTFT/ITL/throughput @ batch 1/8/32. The trade-off curve is your resume-centerpiece chart.  
5vLLM: Benchmark Against It + Read It + Load Test  
colab T4W8-a  
Objective

Deploy a quantized 7–8B model with vLLM, load-test it, benchmark your mini engine vs vLLM, and explain the gap honestly. The gap IS the learning — vLLM has fused CUDA kernels, better scheduling, CUDA graphs; naming why it's faster proves you understand production.  
Deliverables

text

```
vLLM serving a quantized 7–8B model (AWQ/GPTQ) on a single T4, OpenAI-compatible endpoint
Locust (or equivalent) load-test script: throughput/TTFT under increasing concurrency
Reading notes on vLLM's scheduler.py and cache_engine.py / block manager — map them to your M2/M3 code
Honest mini-engine-vs-vLLM table + a written "why vLLM wins" analysis (kernels, CUDA graphs, scheduling, prefix caching)
```

Skills learned  
vLLM servingLocust load testingconcurrency saturationreading production schedulershonest gap analysisOpenAI-compatible API  
Common mistakes

text

```
Comparing your pure-PyTorch engine to vLLM and being demoralized — the point is the explained gap, not winning
Load-testing with a single concurrency level → miss the saturation curve entirely
Not stating you're benchmarking a small quantized model on a T4 → over-generalizing to "70B on A100s"
Skimming vLLM source without mapping it to your own code → reading without leverage
```

Stretch goals

text

```
Find a real bug / perf gap / doc hole in vLLM while reading → an OSS PR (your OS's highest-value output)
Skim SGLang's RadixAttention (prefix-tree KV sharing) and note how it differs from vLLM's paging
```

OS integration: EXP-2026-017 — mini engine vs vLLM, T4, defined workload. This closes the "I built it AND I know why the pros are faster" loop — the exact senior signal. Second OSS PR attempt lives here.  
6Quantization + Serve (production realism)  
colab T4W8-b  
Objective

Quantize your Project 2 DPO Darija model (bf16 baseline vs GPTQ vs AWQ), measure the quality-vs-memory trade-off on your own AlgerianMMLU eval, and serve it behind FastAPI with token streaming. This is the end-to-end lifecycle in one deliverable.  
Deliverables

text

```
Three variants: bf16 (baseline), GPTQ, AWQ — each measured on AlgerianMMLU (quality) + peak VRAM + throughput
The quality/memory/latency trade-off table (the artifact your roadmap explicitly asks for)
FastAPI server with Server-Sent-Events / streaming token output
A stable public demo (HF Space, NOT Colab — your OS correction: Colab is not a deployment)
```

Skills learned  
GPTQ vs AWQquantization quality evalcalibration dataFastAPI streaming (SSE)VRAM budgetingquality-memory trade-off  
Common mistakes

text

```
Claiming "quantization is free" without measuring quality → run it on AlgerianMMLU, report the real delta
Bad calibration set for GPTQ (wrong domain) → worse quality than necessary; calibrate on Darija-relevant text
Promising a live demo on Colab that dies → HF Space or clearly-labeled on-demand (your OS forbids the Colab-demo trap)
Streaming that buffers the whole response → not actually streaming; test token-by-token arrival
```

Stretch goals

text

```
Add an INT4 vs INT8 comparison, or a per-layer sensitivity analysis (which layers tolerate quantization worst?)
Measure quantization's effect specifically on Darija vs English — low-resource quant degradation is under-studied
```

OS integration: EXP-2026-018 — bf16 vs GPTQ vs AWQ on AlgerianMMLU. This is where Projects 2, 5, and 6 converge — the serving of your own aligned model, evaluated on your own benchmark. Portfolio artifact graph completes.  
PART 2  
Essential Papers  
Code first, paper second · read what your engine implements  
Must Read (3)  
PagedAttention (vLLM: "Efficient Memory Management for LLM Serving")  
MustImplement  
This IS the project. Block tables, paged KV, near-zero fragmentation, prefix sharing — you implement the core idea in M3. The virtual-memory-for-KV analogy is the mental model that makes the whole mini engine make sense.  
Key ideas  
Paged KV blocks, block tables, fragmentation elimination, prefix/copy-on-write sharing, continuous batching  
Focus sections  
The PagedAttention mechanism + the memory-management + scheduling sections. The block-table figures.  
Implement?  
Yes — the block-table paging in M3.  
Time / Difficulty  
~3h · ★★★★★★☆☆☆☆ (6/10)  
Orca: A Distributed Serving System (iteration-level / continuous batching)  
MustRead  
The paper that introduced iteration-level (continuous) batching — the idea you implement in M2. vLLM builds on this. Read it to understand why token-level scheduling beats static batching, before you code the scheduler.  
Key ideas  
Iteration-level scheduling, selective batching, admit/evict at token granularity  
Focus sections  
The iteration-level batching mechanism + the scheduling figure. Skip the distributed parts.  
Implement?  
Concept → your M2 scheduler  
Time / Difficulty  
~2.5h · ★★★★★☆☆☆☆☆ (5/10)  
FlashAttention / FlashAttention-2 (revisit for inference)  
MustMental model  
You met it in P1/P4. Revisit with serving eyes: it's the attention kernel under both your engine's decode step and vLLM's. Understanding IO-awareness explains why decode is bandwidth-bound and why the KV cache dominates memory traffic.  
Key ideas  
IO-aware attention, why decode re-reads the KV cache, memory-bandwidth ceiling  
Focus sections  
The memory-hierarchy figure + (for inference) the decode/single-query discussion  
Implement?  
Concept — you already have the P4 kernel  
Time / Difficulty  
~2h re-read · ★★★★★★★☆☆☆ (7/10)  
Recommended (3)  
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
Optional (2)  
Speculative Decoding (Leviathan et al. / Medusa)  
Optional  
Optional advanced inference technique — a draft model proposes tokens the target verifies in parallel, cutting decode latency. Read only if you take a stretch goal; it's a whole additional axis beyond batching/paging.  
Focus  
Draft-verify loop, acceptance rate  
Time / Diff  
~2h · 7/10  
llama.cpp / GGUF quantization formats (docs, not a paper)  
Optional  
Optional and deprioritized — your roadmap says skim. GGUF/k-quants matter for CPU/edge deployment, a different track than GPU serving. Skim only to know the format exists and where it fits; don't invest here now.  
Focus  
The k-quant format taxonomy only  
Time / Diff  
~45m skim · 4/10  
PART 3  
Open Source — Exactly What to Read  
The specific files · production serving code reveals the scheduling tricks  
Repo Files that actually matter Why  
vllm-project/vllm core/scheduler.py · core/block_manager*.py · engine/llm_engine.py · worker/cache_engine.py THE required reading. scheduler.py = your M2 continuous batching, done for real (admit/evict, preemption). block_manager = your M3 paged KV. Read these AFTER building your mini versions so you can map line-to-line.  
vllm-project/vllm (attention) attention/ops/* · attention/backends/* You already skimmed this in Project 4. Now it clicks — the paged-attention kernel gathers KV over the block table you built in M3.  
karpathy/nano-vllm (or a minimal-vLLM clone) the whole thing — it's small A minimal continuous-batching + paged-KV engine. The single best reference for what "vLLM but mini" should look like. Read it as your M1–M4 north star; diff your engine against it.  
sgl-project/sglang the RadixAttention / prefix-cache module Skim only. See how prefix-tree KV sharing differs from vLLM's paging — for the "vLLM vs SGLang" interview answer.  
AutoGPTQ / AutoAWQ the quantize entry point + the calibration loop For M6. Read enough to know what calibration data does and how the packed 4-bit weights are stored. Explains your quality deltas.  
locustio/locust a basic locustfile.py example For M5 load testing. Tiny — you need the user/task pattern and how to ramp concurrency. Copy an LLM-endpoint example.  
Reading order: nano-vllm (see the whole minimal shape) → build your M1–M4 → THEN vLLM scheduler.py + block_manager (map to your code, understand the production extras: preemption, swapping, prefix caching). Reimplement the mini engine fully; deep-read vLLM, don't rebuild it — this is exactly your roadmap's "reserve full reimplementation for the frontier things" rule.  
PART 4  
Experiments Worth Running  
Each answers a question · most become content · one seeds a paper  
⚗ E1 · Latency-vs-throughput curve (batch 1/8/32) → content · centerpiece  
Question How does batch size trade per-request latency (TTFT, ITL) against total throughput?  
Insight The fundamental serving trade-off, visualized. This one chart is the resume centerpiece and the thing every inference interviewer wants you to explain.  
Content / Paper? Content — the flagship benchmark chart. Paper? No.  
⚗ E2 · Mini engine vs vLLM, decomposed → content · senior signal  
Question vLLM is Nx faster — how much is fused kernels vs scheduling vs CUDA graphs vs paging?  
Insight Attributing the gap (not just measuring it) is what separates "I ran a benchmark" from "I understand production inference." The honest, decomposed answer is elite content.  
Content / Paper? Content — the "I built a mini vLLM and here's why the real one wins" writeup. This is WRITE-UP #2.  
⚗ E3 · Paged vs contiguous KV: concurrent-sequence capacity → content  
Question How many more concurrent requests fit with paged KV vs a contiguous per-sequence buffer?  
Insight Makes PagedAttention's memory win concrete and measured — the paper's central claim, reproduced by you.  
Content / Paper? Content — a reproduction post ("I reproduced PagedAttention's memory win at mini scale").  
⚗ E4 · Quantization quality-vs-memory on AlgerianMMLU → content + paper  
Question How much quality does GPTQ/AWQ cost on a low-resource dialect vs the memory it saves?  
Insight Quantization degradation on low-resource languages is genuinely under-studied. Measured on your own Darija benchmark, this is a real contribution, not a rehash.  
Content / Paper? Both. The trade-off table + a workshop-adjacent finding on low-resource quant degradation.  
⚗ E5 · The KV-cache position-offset bug forensics → content · highest shareability  
Question What exactly breaks when RoPE/position offsets are wrong after the first decode step?  
Insight The single most common KV-cache bug: perfect first token, garbage after. Trigger it, show the output, fix it. The definitive inference debugging story.  
Content / Paper? Content — Debugging Story, the most engaging piece this project produces.  
PART 5  
Research Questions  
Beginner → Intermediate → Advanced  
Beginner · while learning  
At what batch size does throughput saturate on a T4, and where's the latency knee?  
Finds the "operating point" — the practical serving sweet spot. Foundational.  
How much of decode-step time is the KV-cache read vs the actual matmul?  
Confirms decode is bandwidth-bound viscerally, in your own numbers.  
How many more concurrent sequences fit with block size 16 vs 32 vs 64?  
The paging granularity trade-off, measured.  
Intermediate · needs experiments  
How much does prefix/block sharing help when many requests share a system prompt?  
This is where vLLM's biggest real-world wins come from — measuring it at mini scale is instructive and under-demonstrated.  
Does continuous batching's throughput advantage over static batching grow or shrink as request-length variance increases?  
The mechanism only pays off with length variance — quantifying that relationship is genuinely useful.  
Does 4-bit quantization degrade Darija generation more than English, at equal perplexity on the base model?  
Low-resource quant fairness is under-explored and directly ties to your thesis.  
Advanced · workshop / conference potential  
Do post-training quantization methods (GPTQ/AWQ) disproportionately harm low-resource language capability relative to high-resource, and does calibration-set language matter?  
Publishable: novel benchmark (yours) + an under-studied fairness question + a practical deployment implication (you can't afford to serve unquantized). Ties Projects 2, 5, 6 into one paper.  
Can calibrating GPTQ on low-resource text recover the quality lost when calibrating on English?  
A concrete, testable intervention with real deployment value for under-served languages.  
A reproducible "LLM serving on a single consumer/free GPU" benchmark: how do batching/paging/quantization interact at the small scale most practitioners actually have?  
Not novel-method, but an accessible, honest systems benchmark suits a reproducibility/efficiency workshop. Strong blog first.  
PART 6  
Research Ideas  
Labeled by risk · novelty · difficulty  
Quantization fairness for low-resource languages (GPTQ/AWQ on Darija vs English)  
Medium risk

Type: Evaluation/analysis paper. Novelty: High (under-studied). Difficulty: Medium — you already have the benchmark (P6), the model (P2), and the quant pipeline (M6). The single strongest paper candidate in this project, and it unifies your whole roadmap.  
"I built a mini vLLM" — a pedagogical minimal inference engine + gap analysis  
Low risk

Type: Engineering/education artifact. Novelty: Low-Medium (value = clarity + honesty). Difficulty: Medium — it's this project, packaged. Extremely high portfolio/content ROI; the decomposed vLLM-gap analysis is the senior signal.  
An OSS PR to vLLM or SGLang (bug fix, doc, or benchmark)  
Medium risk

Type: Open-source contribution. Novelty: N/A. Difficulty: Medium — while deep-reading scheduler.py/block_manager, find a real gap. Per your OS, one merged PR to a systems repo like vLLM outweighs ten portfolio projects. Highest career ROI, near-zero compute.  
Serving efficiency on a single consumer GPU: a reproducible benchmark suite  
High risk

Type: Systems/reproducibility. Novelty: Low-Medium. Difficulty: High to reach paper rigor on one card. Honest verdict: strong blog, workshop only if combined with the quantization-fairness angle.  
PART 7  
Content Opportunities  
Per milestone · platform · effort · long-term value · evidence-gated  
Milestone Content Best platform Effort Long-term value  
M1 KV cache Architecture Explanation: "Prefill is compute-bound, decode is bandwidth-bound" Personal site · X 2h ★★★★ teaching credibility  
M2 Continuous batching Diagram post: "Why static batching wastes your GPU" X · LinkedIn 1.5h ★★★★ intuitive, shareable  
M3 Paged KV Paper→Code: "I reproduced PagedAttention's memory win at mini scale" Personal site · GitHub 2.5h ★★★★★ reproduces the paper  
M4 Benchmark The latency-vs-throughput curve (resume centerpiece chart) Personal site · X 1.5h ★★★★★ the flagship visual  
M5 vLLM gap WRITE-UP #2: "I built a mini vLLM — here's why the real one is faster" Personal site (canonical) 4h ★★★★★ elite senior signal  
M6 Quant + serve Benchmark Report: bf16 vs GPTQ vs AWQ on AlgerianMMLU + live demo HF Space · LinkedIn 2.5h ★★★★★ unifies the roadmap  
Whole project OSS PR to vLLM/SGLang + the mini-inference-engine/ repo README GitHub · OSS — ★★★★★ PR > 10 repos  
OS rule enforcement: Total ≈ 14h over 2 weeks — under 15%. WRITE-UP #2 ("I built a mini vLLM") is your 3rd canonical long-form article — your roadmap explicitly names it, so this is the RAG-or-inference slot spent on inference. Do NOT also write a separate giant quantization article; the quant table is a section of the mini-vLLM writeup + a short post. The highest-value single output remains the OSS PR. Everything gates on M4's defined-workload benchmark.  
PART 8  
Knowledge Capture — Permanent  
Feed llm-lab/projects/mini-inference-engine/ · reusable serving toolkit  
Reusable Utilities

text

```
KV cache module (prefill/decode split) — reuse in any serving work
Continuous-batching scheduler (admit/evict loop) — the core reusable piece
Paged-KV block manager (block pool + block table) — mini-vLLM in a file
Benchmark harness (TTFT/ITL/throughput, p50/p95, concurrent load) — reuse for every serving claim
Locust load-test template for LLM endpoints
FastAPI streaming server (SSE) skeleton — reuse for every demo
```

Notes / Diagrams / Decisions

text

```
Block-table diagram (logical→physical KV pages) — reuse from P4, extend here
Static-vs-continuous-batching diagram — evergreen teaching asset
Prefill-vs-decode roofline note (compute-bound vs bandwidth-bound)
Inference glossary: TTFT, ITL/TPOT, throughput, p95, saturation — interview-ready
"Why vLLM is faster" decomposition (kernels, CUDA graphs, scheduling, prefix cache) — a senior-signal note
KV-cache bug playbook: position offset, cache masking, block-free leak, streaming buffer
Quant trade-off table (bf16/GPTQ/AWQ × quality/VRAM/latency) on your own eval
```

Store in llm-lab/projects/mini-inference-engine/: engine/ (kv_cache, scheduler, block_manager), bench/, serve/fastapi_app.py, experiments/EXP-2026-014..018.md, bugs/BUG-2026-00N-kv-offset.md, diagrams/. Every benchmark records the full workload spec: hardware, model, dtype, prompt/output length distribution, concurrency.  
PART 9  
Resume & Portfolio Value  
What appears where · what proves quality  
GitHub

text

```
mini-inference-engine/: KV cache + continuous batching + paged KV + benchmark harness, results-first README with the trade-off chart
vllm-load-testing/: Locust script + throughput-under-concurrency graph
The honest mini-engine-vs-vLLM comparison + decomposed gap analysis
```

Open Source (top prize)

text

```
A PR to vLLM or SGLang — bug fix, doc, or benchmark, found while reading the scheduler/block manager
```

Hugging Face

text

```
Quantized (AWQ/GPTQ) Darija DPO model + a live Space demo with streaming
Quant trade-off table in the model card
```

Resume (two lines)

"Built a mini inference engine from scratch (KV cache, continuous batching, paged KV memory) and benchmarked TTFT/ITL/throughput vs vLLM on a defined workload, with a decomposed analysis of the performance gap."

"Deployed and load-tested a quantized 7–8B model with vLLM behind a streaming FastAPI endpoint; measured GPTQ vs AWQ quality-vs-memory trade-offs on a self-built benchmark."  
LinkedIn

text

```
The latency-vs-throughput curve post
"I built a mini vLLM" writeup
The KV-cache debugging story + live demo
```

Evidence of quality

text

```
Benchmarks with a full workload spec (hardware/model/dtype/lengths) — the detail seniors check first
p95 latency reported, not just mean (proves you know tail latency matters)
An honest, decomposed vLLM gap analysis (integrity + depth)
Quantization quality measured on a real eval, not assumed free
A merged OSS PR to a serving repo (frontier-competence signal)
```

PART 10  
Stretch Goals — Ranked by ROI  
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
Final Project Dashboard  
Everything scored · Eng / Res / Port / Content / Career (1–5)  
Task Pri Diff Time Eng Res Port Content Career  
M1 · KV cache Must 5/10 ~10h 5 1 4 4 5  
M2 · Continuous batching Must 7/10 ~14h 5 2 5 5 5  
M3 · Paged KV memory Must 8/10 ~14h 5 3 5 5 5  
M4 · Benchmark harness Must 5/10 ~10h 4 2 5 5 5  
M5 · vLLM benchmark + read + load test Must 6/10 ~14h 5 2 5 5 5  
M6 · Quantize + serve (FastAPI) Strong 6/10 ~12h 4 4 5 5 5  
E4 · Quant fairness on AlgerianMMLU Strong 5/10 ~6h 3 5 4 5 4  
OSS PR (vLLM / SGLang) Strong 6/10 ~8h 4 1 5 4 5  
E5 · KV-offset bug forensics Strong 3/10 ~3h 4 1 3 5 4  
WRITE-UP #2 (canonical: mini vLLM) Strong 3/10 ~4h 1 2 5 5 5  
Prefix sharing (stretch) Opt 7/10 ~8h 5 3 4 4 5  
Read the dashboard this way: This project has near-perfect Eng/Port/Content/Career columns (mostly 5s) — it's your resume centerpiece for a reason. M2 (continuous batching) and M3 (paged KV) are the differentiators almost no junior can build; protect them. M5's decomposed vLLM gap analysis is the single most senior-reading deliverable — "I built it AND I know why the pros are faster." Research value spikes only at M6/E4 (quantization fairness) — that's your one paper thread, and it's cheap because P2 and P6 already built the model and benchmark. If forced to cut, drop prefix-sharing first; never cut M2 or M3. Highest optional ROI: the OSS PR.  
Project 05 Value Plan · integrates with LLM Lab OS · the capstone that consumes Projects 01–04 · mini engine first, vLLM second · define the workload before you quote a number · inference is where the money is made

Inputs are processed by third-party AI and responses may be inaccurate.

Understood. If you did not like the Alignment & Evaluation System, we drop it completely.

Your background is in Statistics and Data Science, and you are currently building a low-level inference engine (KV cache, prefill/decode, batching). It is likely that DPO/Alignment felt too much like "training fluff" and not enough like hard systems engineering. You want to push deeper into the infrastructure, performance, and systems side of LLM Engineering.

Since you mentioned `dziri-eval` and Algerian Darija, we can integrate that, but not as a benchmark dataset. Instead, we will use it as the **workload** that exposes a hard systems problem.

Here is the project that bridges your inference engine work, your stats background, and the Made With ML MLOps curriculum, without touching model training.

---

### The New Project: LLM Inference Auto-Tuner & Profiling Platform

**Problem:**  
LLM inference performance is highly sensitive to engine configurations: batch size, KV cache block size, swap space, and continuous batching thresholds. Finding the optimal config for specific hardware (like an RTX 3050 4GB) and a specific workload is pure guesswork. Furthermore, low-resource languages like Algerian Darija suffer from tokenizer bloat (a single Darija word takes more tokens than an English word), which means KV caches fill up faster, fundamentally shifting the memory/compute bottleneck. Tuning for English workloads on a 4GB GPU will OOM or throttle on Darija workloads.

**Users:**  
ML/Serving engineers deploying LLMs to constrained or edge hardware.

**Technical core:**  
Workload characterization, latency/throughput modeling, Bayesian optimization (Optuna) applied to _system parameters_ rather than model weights.

**Engineering core:**  
Automating the server lifecycle (start vLLM -> wait for ready -> load test -> kill), parsing `torch profiler` / `nvidia-smi` metrics, handling OOMs gracefully during the search loop.

**Systems core:**  
GPU memory profiling, identifying compute-bound vs memory-bound phases, tuning KV-cache paging parameters to survive tokenizer bloat.

**MVP:**  
A Python script that takes a batch size and KV cache fraction, starts a vLLM server, fires Dziri-Eval prompts at it using a load tester, measures TTFT/ITL, and logs the result to MLflow.

**Evolution:**  
MVP -> Optuna integration for multi-parameter search -> Multi-objective optimization (finding the Pareto frontier of latency vs throughput) -> CI/CD performance regression pipeline -> Tuning your custom mini-engine and comparing its optimal configs against vLLM.

**Learning:**  
Bayesian optimization, async server lifecycle management, deep GPU memory profiling, performance engineering, LLMOps automation.

**Made With ML connection:**  
Optuna for system tuning (instead of model tuning); MLflow for system metrics (instead of loss); CI/CD for perf regression (instead of code linting).

**Beyond Made With ML:**  
OS-level process management, Pareto frontier analysis, low-level vLLM config internals, hardware roofline modeling.

**Portfolio:**  
Elite systems engineering signal. Proves you don't just run a model, you understand how to squeeze maximum performance out of constrained hardware for a specific domain.

**Research:**  
How does tokenizer inefficiency in low-resource languages shift the optimal memory paging and batching configurations on constrained GPUs?

**Open source:**  
A workload-aware auto-tuning framework for local LLM serving.

**Compute:**  
Yes. Entirely designed around the 4GB RTX 3050 limit. In fact, the constraint is the point of the project.

**Difficulty:** 8/10

**Time:** 6 weeks

---

### Why this is the right project for you

1. **It uses your Stats background properly:** Instead of using statistics for subjective LLM-as-a-judge prompts, you are using Bayesian Optimization (Gaussian Processes, Tree-structured Parzen Estimators) to explore a high-dimensional system configuration space and find a Pareto frontier. This is hard, rigorous statistics applied to ML infrastructure.
2. **It extends your inference engine:** You are already building an inference engine. This platform becomes the _harness_ that tests and tunes both your engine and vLLM. You aren't abandoning your current work; you are building the MLOps layer on top of it.
3. **It uses Dziri-Eval meaningfully:** Darija tokenization bloat is a real systems problem. If an English prompt takes 50 tokens and a Darija prompt takes 120 tokens, the KV cache pressure is completely different. Your auto-tuner will prove that a 4GB GPU needs different paging/batching configs for Darija than it does for English. That is a publishable, highly interesting engineering insight.
4. **It perfectly matches Made With ML without copying it:** Made With ML teaches MLflow and Optuna for _model hyperparameter tuning_. You will use the exact same tools, but for _inference hyperparameter tuning_. This shows you understand the MLOps principles deeply enough to apply them to novel problems.

---

### Architecture

text

```
Workload Generator (Dziri-Eval Darija prompts + English MMLU prompts)
↓
Tuner Agent (Optuna) → suggests params (max_batch_size, block_size, swap_space, gpu_mem_util)
↓
Server Lifecycle Manager (Subprocess/vLLM API) → starts engine with suggested params
↓
Load Tester (Locust/async Python) → fires concurrent requests, measures TTFT/ITL/Throughput
↓
Metrics Collector (nvidia-smi, torch profiler) → peak VRAM, KV cache hit rate, OOM events
↓
Objective Evaluator → calculates Pareto penalty (e.g., minimize ITL, maximize throughput, penalize OOM)
↓
Experiment Tracking (MLflow) → logs params, metrics, and system traces
↓
Best Config Artifact → JSON file deployed with the model
↓
CI/CD (GitHub Actions) → triggers auto-tuner on hardware when engine code changes to detect regressions
```

### Repository structure

text

```
inference-auto-tuner/
├── .github/workflows/
│   └── perf_regression.yml          # Run auto-tuner on PRs to check for perf drops
├── configs/
│   ├── search_space.yaml            # Define min/max for batch size, block size, etc.
│   └── workload_configs.yaml        # Darija vs English prompt distributions
├── data/
│   └── dziri_eval_workload.jsonl    # Sampled Darija prompts for load testing
├── src/
│   ├── workloads/
│   │   └── loader.py                # Loads and tokenizes Dziri-Eval prompts
│   ├── lifecycle/
│   │   ├── server_manager.py        # Start/stop vLLM or mini-engine subprocess
│   │   └── health_check.py          # Wait for server ready before load testing
│   ├── profiler/
│   │   ├── metrics.py               # Parse nvidia-smi / torch profiler logs
│   │   └── oom_detector.py          # Graceful OOM handling during search
│   ├── tuner/
│   │   ├── objective.py             # The function Optuna optimizes
│   │   ├── sampler.py               # TPE / Gaussian Process config
│   │   └── pareto.py                # Multi-objective logic
│   ├── tracking/
│   │   └── mlflow_logger.py         # Log system params and perf metrics
│   └── deploy/
│       └── apply_config.py          # Inject best config into production server
├── tests/
│   ├── test_lifecycle.py            # Mock server start/stop
│   └── test_objective.py            # Test Pareto penalty calculation
├── notebooks/
│   └── analyze_search.ipynb            Visualize Optuna trials and Pareto frontiers
├── Dockerfile                       # Container with vLLM, CUDA, Optuna
├── requirements.txt
└── README.md
```

### Development stages

**Stage 1 — MVP**

- Write a Python script that starts a vLLM server with hardcoded arguments (e.g., `--max-num-seqs 8`, `--gpu-memory-utilization 0.5`).
- Write an async load tester that fires 50 Dziri-Eval prompts at it.
- Measure TTFT (Time To First Token) and ITL (Inter-Token Latency), kill the server, and print the results.

**Stage 2 — Engineering foundation**

- Introduce Optuna. Define a search space for 3 variables: `max_num_seqs` (4 to 32), `block_size` (8 to 32), and `gpu_memory_utilization` (0.3 to 0.8).
- Write the `objective` function: Optuna suggests params -> script starts vLLM -> load tests -> returns average ITL to Optuna.
- Integrate MLflow to log every Optuna trial.

**Stage 3 — Productionization & Multi-Objective**

- Upgrade the objective function to be multi-objective: Optuna must _minimize_ ITL and _maximize_ Throughput simultaneously. This generates a Pareto frontier.
- Add robust OOM handling: if vLLM crashes mid-trial due to 4GB VRAM limits, the script catches the subprocess exit code, tells Optuna the trial failed (prune), and moves to the next config.
- Dockerize the auto-tuner so it can run on any cloud GPU with the same constraints.

**Stage 4 — Advanced engineering (The Mini-Engine Integration)**

- Point the auto-tuner at _your_ custom mini-inference engine instead of vLLM.
- Tune the batching logic and KV cache limits of your engine.
- Compare the optimal Pareto frontier of your engine vs vLLM on the 4GB GPU for Darija workloads.

**Stage 5 — Research / Open Source**

- Publish the findings: "Optimal Inference Configurations for Low-Resource Languages Tokenizer Bloat on 4GB GPUs."
- Release the `inference-auto-tuner` as a CLI tool: `tune-inference --engine vllm --workload dziri.jsonl --vram 4gb`.

---

### Synchronize with Made With ML

Here is how this project maps to the Made With ML curriculum, distinguishing what you learn from the course vs. what you learn independently.

text

```
Made With ML topic: Design
        ↓
Learned from MWMl: Translating product requirements into ML objectives; defining online vs offline metrics.
        ↓
Applied to the project: Define the objective: "Find the Pareto-optimal vLLM system configs that minimize ITL and maximize throughput for Darija workloads on a 4GB GPU."
        ↓
Learned independently: Understanding that LLM system metrics (TTFT, ITL, KV cache hit rate) are fundamentally different from model metrics (F1, accuracy). Understanding how tokenizer bloat shifts memory pressure.
        ↓
Project milestone: Project charter and system metric rubric defined.

Made With ML topic: Data
        ↓
Learned from MWMl: Data preprocessing, splitting, and versioning.
        ↓
Applied to the project: Process the Dziri-Eval dataset into a realistic load-testing workload (JSONL of prompts with varying lengths). Version this workload data with DVC.
        ↓
Learned independently: Workload characterization. Real users don't send uniform prompts. You must sample Dziri-Eval to create a distribution of short, medium, and long Darija prompts to stress the KV cache realistically.
        ↓
Project milestone: Reproducible workload generator that simulates concurrent Darija traffic.

Made With ML topic: Modeling (Experiment Tracking)
        ↓
Learned from MWMl: MLflow integration, logging params/metrics/artifacts.
        ↓
Applied to the project: Log every Optuna trial to MLflow. The "params" are system configs (block size, batch size), the "metrics" are TTFT, ITL, peak VRAM.
        ↓
Learned independently: Subprocess lifecycle management. Programmatically starting a CUDA subprocess, waiting for the HTTP health check to pass, running the load test, and violently killing the subprocess to free VRAM for the next trial without leaking memory.
        ↓
Project milestone: A working Optuna loop that logs system performance to MLflow without crashing the OS.

Made With ML topic: Optimization (Hyperparameter Tuning)
        ↓
Learned from MWMl: Using Optuna for hyperparameter tuning; defining search spaces; pruning unpromising trials.
        ↓
Applied to the project: Define the system search space. Use Optuna's TPE (Tree-structured Parzen Estimator) sampler to navigate the high-dimensional space of vLLM configs. Use Optuna's MedianPruner to kill server trials that are OOMing or producing terrible latency early.
        ↓
Learned independently: Multi-objective optimization (Pareto fronts). You aren't looking for one best number; you are looking for the set of configs where you cannot improve throughput without hurting latency. Visualizing Pareto frontiers in MLflow.
        ↓
Project milestone: An automated search that outputs a Pareto frontier of optimal system configurations.

Made With ML topic: Evaluation
        ↓
Learned from MWMl: Offline evaluation workflows, behavioral testing.
        ↓
Applied to the project: Build an evaluation harness that takes the "best" config found by Optuna, runs an extended 10-minute load test, and statistically validates that it actually outperforms a default config (e.g., vLLM defaults).
        ↓
Learned independently: Statistical significance in latency testing. Variance in TTFT can be high. Using statistical tests (e.g., Mann-Whitney U) to prove that the tuned config's p95 latency is genuinely better, not just a fluke of the workload distribution.
        ↓
Project milestone: A rigorous benchmark report proving the auto-tuner found a better operating point.

Made With ML topic: Serving
        ↓
Learned from MWMl: FastAPI, Ray Serve, microservices.
        ↓
Applied to the project: The auto-tuner outputs a JSON config file. Write a FastAPI deployment script that reads this JSON and boots vLLM with those exact parameters for "production" serving.
        ↓
Learned independently: Deep vLLM engine internals. Understanding what `gpu_memory_utilization` actually reserves vs what the OS needs. Understanding how `block_size` affects attention kernel IO on a 4GB card. 
        ↓
Project milestone: A deployment script that serves the Darija model using the auto-tuned optimal parameters.

Made With ML topic: CI/CD
        ↓
Learned from MWMl: GitHub Actions, testing workflows, continuous training pipelines.
        ↓
Applied to the project: Write a GitHub Actions workflow. On any PR that modifies the inference engine code, spin up a cloud GPU runner, run a shortened 10-trial Optuna search, and assert that the best Pareto point hasn't degraded by more than 5% from the main branch.
        ↓
Learned independently: Performance regression testing. This is a massive part of ML Infra engineering. If someone changes the KV cache logic in your engine, does it silently make latency worse? The CI/CD pipeline catches this before merge.
        ↓
Project milestone: Fully automated performance regression pipeline.
```

### Final Objective Realized

With this project, you do not train a single weight. You focus entirely on the hardest part of LLM Engineering: making inference fast, cheap, and reliable under strict hardware limits.

You take a real problem (Darija tokenizer bloat on 4GB GPUs), use MWMl concepts (MLflow, Optuna, CI/CD) to structure the engineering process, and push far beyond the course by implementing multi-objective Bayesian optimization on system parameters, subprocess lifecycle management, and Pareto frontier analysis.

When you walk into an interview, you can say: _"I built an inference auto-tuner that uses Bayesian optimization to find the Pareto-optimal batching and KV-cache configurations for low-resource language workloads on 4GB GPUs. It automatically starts and kills the inference engine, profiles VRAM and latency, and gates merges via a performance regression CI/CD pipeline."_

That is an LLM Infrastructure Engineer.