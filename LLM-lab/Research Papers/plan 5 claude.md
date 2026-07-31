
# From LLM Theory to LLM Engineer: A Post-Theory Roadmap

_Context note: you already have three near-complete, real LLM systems — a MoE/LoRA dialect model (DziriMoE), a DPO preference dataset (DziriDPO), and a reasoning benchmark (AlgerianMMLU) — plus a from-scratch GPT implementation and H100 access. This is not a "start from zero" roadmap. It's built around shipping what you have, because that produces more career value than any new toy project would._

---

## Part 1 — What an Excellent LLM Engineer Actually Knows

**Essential** (you cannot call yourself an LLM engineer without these)

- Fine-tuning: full FT, LoRA/QLoRA, DPO/ORPO/GRPO — knowing which to use when
- Data engineering: cleaning, deduplication, filtering, synthetic data generation, quality control — this is 70% of real work
- Evaluation: building benchmarks, avoiding contamination, statistical significance, human eval design
- Serving: vLLM/TGI/SGLang, batching, quantization for inference, latency/throughput tradeoffs
- Debugging training runs: loss spikes, gradient issues, OOM, silent correctness bugs
- Reading and modifying library source code (Transformers, PEFT, TRL) when the abstraction breaks
- Experiment tracking and reproducibility (W&B/MLflow, seeding, config management)
- Basic distributed training (DDP, FSDP, gradient accumulation, mixed precision)

**Very Important**

- Pretraining mechanics at small scale (even if you never pretrain a frontier model)
- RAG system design (retrieval quality >> generation quality, in practice)
- Agent/tool-use architectures and their failure modes
- CUDA-level intuition (not necessarily writing kernels, but understanding memory bandwidth, kernel fusion, why FlashAttention matters)
- Prompt engineering as a rigorous discipline (few-shot design, structured output, eval-driven iteration)
- Cost/latency/quality tradeoff modeling — this is what makes you "senior" vs "junior"
- Deployment engineering: Docker, FastAPI, basic MLOps/CI-CD for models

**Nice to Have**

- Megatron-LM/DeepSpeed internals (only if targeting frontier lab infra roles)
- Writing custom CUDA/Triton kernels
- Multi-modal (vision-language) pipelines
- Kaggle-style competitive tuning skills

**Mostly for Researchers**

- Novel architecture design (new attention variants, new MoE routing)
- Theoretical scaling law derivation
- Formal ablation methodology for publication
- Literature synthesis across 100+ papers

The dividing line: **industry pays for people who can take an imperfect model and imperfect data and ship a reliable, evaluated, fast system.** Research values people who can isolate one variable and prove something new about it. You are currently doing _both_ simultaneously through your thesis — that's rare and worth exploiting, not diluting.

---

## Part 2 — Learning Priorities (Ranked)

|Rank|Activity|Why it matters|Industry value|Research value|Skill type|Level|
|---|---|---|---|---|---|---|
|1|Fine-tuning models (real data, real eval)|Core job function|Very High|Medium|Engineering|Int|
|2|Evaluation frameworks|Nobody trusts a model without one; you already built one (AlgerianMMLU)|Very High|High|Both|Int|
|3|Building portfolio projects (deployed)|This is literally what gets you hired|Very High|Low|Engineering|All|
|4|Serving models (vLLM etc.)|Production reality|Very High|Low|Engineering|Int|
|5|Reading Transformers/PEFT/TRL source|Abstractions leak; you need to fix bugs|High|Low|Engineering|Int|
|6|RAG|Most deployed LLM products are RAG|High|Low|Engineering|Beg-Int|
|7|Reproducing papers (not from scratch, adapting)|Builds real implementation muscle|Medium|High|Both|Int-Adv|
|8|Quantization|Ubiquitous in deployment|High|Low|Engineering|Int|
|9|Distributed training (DDP/FSDP)|Needed once models don't fit one GPU|Medium-High|Medium|Engineering|Int-Adv|
|10|AI Agents / tool-use|Fast-growing niche, but shallower than RAG/finetuning fundamentals|Medium-High|Low|Engineering|Int|
|11|Reading original papers|Foundation for everything else, but slow alone|Medium|Very High|Theoretical|All|
|12|Prompt engineering (rigorous, eval-backed)|Cheap lever, often underrated|Medium|Low|Engineering|Beg|
|13|Open-source contributions|Strong signal but slow ROI early on|Medium-High|Medium|Engineering|Int-Adv|
|14|Reading vLLM/llama.cpp source|Only if you touch inference infra directly|Medium|Low|Engineering|Adv|
|15|Training GPT from scratch|You've done this (CS224N) — mostly done, revisit only for depth|Low (once done)|Medium|Both|Beg-Int|
|16|Implementing papers from scratch|High learning, low output/time — do selectively|Low-Medium|High|Theoretical|Adv|
|17|CUDA|Rare to need directly unless infra-focused|Low-Medium|Medium|Engineering|Adv|
|18|Reading Megatron-LM/DeepSpeed|Only for infra/frontier-lab roles|Low|Medium|Engineering|Adv|
|19|Hugging Face course|Good for absolute beginners only — you're past it|Low (for you)|Low|Both|Beg|
|20|Kaggle competitions|See Part 7 — not your highest-leverage move now|Low-Medium|Low|Engineering|Beg-Int|
|21|Annotated papers (e.g. "The Annotated Transformer")|Great at the theory stage — you're past it|Low|Low|Theoretical|Beg|

The pattern: **anything that produces an evaluated, deployed artifact ranks above anything that produces only understanding.** You already have the understanding.

---

## Part 3 — Practical Experience: What to Build (Given You Have 2 Months and 3 Near-Finished Projects)

Ranked by value **for you specifically**, not in the abstract:

1. **Ship AlgerianMMLU as a public, documented benchmark** — highest value. A benchmark with a leaderboard is a citable, linkable artifact that recruiters, professors (Abdul-Mageed, Darwish, Kondrak), and Gulf labs can immediately verify. Benchmarks are also disproportionately cited relative to effort.
2. **Deploy DziriMoE as a live demo (HF Spaces or FastAPI + simple frontend)** — turns "I built a MoE dialect model" into "here, try it." This is the single action item you already identified as highest-leverage.
3. **Package DziriDPO as a released dataset on Hugging Face Hub** with a proper dataset card — datasets get reused and cited independently of the paper; it's a second citable artifact from one thesis.
4. **Write and submit the arXiv preprint** tying the three together — this is your actual bottleneck for the DAAD deadline and for grad-school outreach. Everything above exists to make this preprint stronger and more credible, not to replace it.
5. **A short evaluation write-up comparing base LLMs vs your DPO-tuned model on AlgerianMMLU** — this is a small, fast, high-signal project: it's the connective tissue that makes the three projects look like one coherent research program instead of three separate things.

Projects I would _not_ spend new time on right now, and why:

- **GPT/BERT from scratch again** — already done via CS224N; repeating it adds no new signal.
- **New RLHF pipeline from scratch** — you already have DPO/GRPO in your stack; building a second alignment pipeline is redundant with what you're shipping.
- **A new RAG or agent project** — valuable in general, but lower ROI _right now_ than finishing and shipping what already exists. Do this in month 3+, after the preprint, as the next portfolio piece for job applications (RAG/agents are what many startup JDs specifically screen for).
- **Multi-agent or vision-language systems** — interesting for your longer-term generative-models pivot, but premature before the preprint ships.

---

## Part 4 — How to Read Papers Effectively

Ranked by effectiveness for someone at your stage:

1. **Read paper → implement/adapt relevant part in your own project → return to paper for details you missed.** This is the highest-retention loop, and it's literally what you're already doing (e.g., pulling DPO/MoE ideas directly into DziriMoE/DziriDPO).
2. **Reimplementing a paper from scratch** — very high learning density, but expensive in time. Reserve for 1–2 papers that are core to your specialization decision (e.g., a foundational MoE routing paper, or a DPO variant paper), not for everything you read.
3. **Reading code first, paper second** — useful when a paper is dense with notation but the reference implementation is clean (common for inference/serving papers like PagedAttention, FlashAttention).
4. **Reading annotated papers / conference talks** — fine as a fast-orientation tool for a new subfield, but you're past needing this as a primary method; use it only when jumping into an unfamiliar area (e.g., diffusion models, if you pursue CME296).
5. **Reading papers only, no code, no implementation** — lowest retention, easiest to fool yourself into thinking you understand something. Fine for staying broadly current (skim abstracts/intros of 5-10 papers/week) but should never be your primary learning mode anymore.

Rule of thumb: **one paper implemented > ten papers read.** At your stage, budget most paper-reading time toward papers that directly inform a project you're already building, not adjacent curiosity.

---

## Part 5 — Open Source Contribution

**Yes, but sequenced after shipping, not before.**

Value ranking relative to building your own projects: open-source contribution is a **strong but slow-compounding signal** — a merged PR to a well-known repo is a great line item, but it typically takes weeks of context-building before you can contribute anything non-trivial. Right now, your own projects have a nearer, larger payoff (preprint deadline, job search in 5–12 months).

Where to target, when you get there:

- **PEFT / TRL** — most tractable entry point given your DPO/LoRA work; you already understand the domain deeply enough to spot bugs or missing features (e.g., MoE-LoRA interactions, low-resource dialect tokenization edge cases).
- **Transformers library** — good for visibility (widest audience) but higher PR competition/review latency.
- **vLLM / llama.cpp** — only if you go the inference-infra direction; steeper entry cost (systems-level C++/CUDA).
- **Unsloth** — smaller, faster-moving, more approachable for LoRA/quantization contributions.

Practical suggestion: after the preprint ships, look for a bug or missing feature that came up _while building DziriMoE/DziriDPO_ (e.g., a PEFT edge case with your adapter setup) and file/fix that. It's a contribution with a story attached, which is worth more than a random first-issue PR.

---

## Part 6 — Hugging Face: What Actually Matters

Given your level, ranked:

1. **Building and publishing real examples** (your datasets, your model cards) — highest value; you're a producer now, not a consumer.
2. **Reading library source code**, specifically PEFT/TRL internals relevant to LoRA-MoE and DPO — you need this to debug your own thesis code, not as general study.
3. **Exploring model repositories** for comparable dialectal/low-resource Arabic models — directly useful for your related-work section and baseline comparisons.
4. **Documentation** (targeted lookup) — as-needed, not read cover-to-cover.
5. **Blogs** — useful for staying current on techniques (e.g., new PEFT methods), low time investment.
6. **The HF Course / intro notebooks** — skip; this is below your current level and would be time misallocated.

---

## Part 7 — Kaggle

**Not a priority for you right now, and here's why rather than a generic "no":**

Kaggle is most valuable for (a) absolute beginners building tabular/CV/NLP fundamentals, or (b) people specifically targeting roles that screen on Kaggle rankings (some applied-ML/data-science roles do). For LLM engineering roles specifically, Kaggle rank is a weak signal compared to a deployed model, a released dataset, and a preprint — which you're already producing. Time spent on a Kaggle competition right now would compete directly with your preprint deadline for the same hours, at lower expected career ROI.

If you want Kaggle later (post-preprint, for breadth or fun): NLP-specific competitions with real datasets are more transferable than generic tabular ones — but treat this as optional, not developmental.

---

## Part 8 — Becoming Research-Capable (Publishing Track)

Skills to add on top of engineering competence:

- **Literature review discipline** — systematically tracking related work (you've already validated your niche against DialectalArabicMMLU; keep doing this rigorously for the preprint's related-work section)
- **Experimental methodology** — proper train/val/test splits, avoiding data leakage between AlgerianMMLU and DziriDPO/DziriMoE training data (a common self-inflicted wound in thesis work)
- **Ablation studies** — isolate what specifically drives your results (e.g., does MoE routing help more than the LoRA adapters alone? does DPO data source quality matter more than quantity?)
- **Benchmark design rigor** — documenting how AlgerianMMLU was constructed, its limitations, potential contamination sources
- **Reproducibility** — public code, fixed seeds, environment specs, so reviewers/future users can rerun your results
- **Scientific writing** — clear framing of contribution, honest limitations section, related work positioning — this is often the actual bottleneck for master's students, more than the technical work itself

Given your DAAD deadline, this part is not abstract — it's the immediate task.

---

## Part 9 — Common Mistakes / Traps

- **Tutorial hopping** — endless HF course / YouTube tutorials past the point of diminishing returns. You're already past this stage; resist the pull back into it out of anxiety.
- **Reading without shipping** — papers accumulate, nothing gets built. Your thesis structure protects you from this if you keep prioritizing shipping over reading.
- **Starting new projects instead of finishing existing ones** — the single biggest trap given your situation. Three near-complete projects sitting unshipped is worse than one project fully shipped. Resist the urge to start a 4th project (RAG, agents, new pretraining run) before the preprint is out.
- **Collecting certificates instead of artifacts** — a certificate says you sat through content; a deployed model/dataset/paper says you can produce it. You've already correctly identified this instinct in yourself (wanting foundational depth over surface tooling) — the flip side is not letting that turn into perpetual course-collecting instead of shipping.
- **Framework-hopping** — learning 5 serving frameworks shallowly instead of vLLM deeply. Pick one inference stack and go deep.
- **Chasing hype topics disconnected from your actual work** — agents, diffusion, etc. are legitimate future directions (and you're right to be curious about CME296/generative models), but sequencing matters: they come _after_ the preprint, not instead of it.

---

## Part 10 — The Ideal 2-Month Plan (8 hrs/day)

### Month 1 — Ship the Three Projects

**Main objective:** move AlgerianMMLU, DziriMoE, and DziriDPO from "near-complete" to "publicly shipped, documented, evaluated."


- **Weeks 1–2:** Finalize AlgerianMMLU. Freeze the benchmark set, run baseline evals (GPT-4-class, open Arabic models, your DPO model) on H100, write eval methodology doc, publish to HF Hub/GitHub with a clear README and leaderboard-style results table.
- **Weeks 2–3:** Package DziriDPO as an HF dataset with a full dataset card (collection methodology: NADI/MADAR/PADIC provenance, LLM-assisted translation + correction process, Whisper transcription pipeline, dialect-classifier bootstrapping). This card _is_ a large chunk of your preprint's methods section, written early.
- **Weeks 3–4:** Deploy DziriMoE as a live demo (FastAPI backend, HF Space or simple hosted frontend). Get it usable by a stranger with zero setup.
- **Tools:** PyTorch, PEFT/TRL, HF Hub/Datasets, FastAPI, Docker, W&B for eval tracking.
- **Skills gained:** dataset packaging, benchmark construction rigor, deployment engineering, evaluation methodology.
- **Portfolio milestone (end of month):** three public, linkable artifacts (benchmark, dataset, demo) — all cross-referencing each other.

### Month 2 — Preprint, Outreach, Next Direction

**Main objective:** submit the arXiv preprint well before the Oct 30 DAAD deadline, and open the professional network that turns the work into opportunities.

- **Weeks 1–2:** Write the preprint tying the three projects into one narrative (a Darija LLM alignment system: benchmark + preference data + specialized architecture). Include the ablations from Part 8 (MoE vs. plain LoRA, data-source quality effects). Submit to arXiv.
- **Week 3:** LinkedIn/network push — post the demo + preprint, reach out to Abdul-Mageed, Darwish, Kondrak with the concrete artifacts attached (not a cold "I'm interested in your work" message — a "here's what I built" message). Also position toward Gulf labs (G42/Inception, QCRI, SDAIA, MBZUAI) given the direct market fit you identified.
- **Week 4:** Start the _next_ portfolio piece with an eye toward the 5–12 month job goal: a small RAG or tool-use agent project (your stated gap area), OR begin CME296/generative-models exploration if the preprint outreach is generating enough momentum that you can diversify. Choose based on which recruiters/labs respond to in week 3.
- **Papers:** read selectively — DPO variants (ORPO, SimPO) for the preprint's related work; one MoE routing paper in depth if you do the ablation; skim low-resource Arabic NLP literature for positioning.
- **Open-source:** if time allows, one small PEFT/TRL contribution stemming from a bug hit during packaging (optional, not blocking).
- **Skills gained:** scientific writing, professional networking, positioning research for a specific market.
- **Portfolio milestone (end of month):** arXiv preprint live, outreach sent to named targets, one new project direction started based on real market signal.

---

## Part 11 — Final Advice

If I stripped away everything non-essential, here's what would remain, classified:

**Essential**

- Ship AlgerianMMLU, DziriDPO, DziriMoE publicly (all three)
- Write and submit the arXiv preprint
- Build a deployed, working demo (FastAPI/HF Spaces)
- Learn evaluation rigor deeply (you're already forced into this)
- Basic serving (vLLM or equivalent) for the demo

**Strongly Recommended**

- LinkedIn outreach to named professors and labs with concrete artifacts attached
- One RAG or agent project after the preprint, to cover a real skill gap for job applications
- Ablation studies for the preprint (drives both research and engineering understanding simultaneously)
- Reading DPO-variant papers (ORPO, SimPO) to strengthen related work and your own tuning choices

**Optional**

- Open-source contribution (do it once you hit a natural bug, don't manufacture the opportunity)
- Kaggle (skip unless a specific role screens for it)
- HF course/intro notebooks (you're past this)
- CUDA/kernel-level work (only if inference-infra becomes your direction)

**Only if Pursuing Frontier Research**

- Megatron-LM/DeepSpeed internals
- Novel architecture design beyond what your thesis already covers
- Large-scale distributed pretraining experience

**The single highest-ROI move available to you right now is not learning anything new — it's shipping what you've already built.** You have more real LLM engineering evidence sitting half-finished than most people build in a year of tutorials. The roadmap above is really just: finish, document, publish, network — in that order, before starting anything new.
































