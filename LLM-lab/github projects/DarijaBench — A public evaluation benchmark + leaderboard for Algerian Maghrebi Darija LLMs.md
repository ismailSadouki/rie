

**1. Name:** DarijaBench (or MaghrebiBench if you want Morocco/Tunisia coverage — I'd go broad).

**2. Description:** A standardized, multi-task benchmark for evaluating how well LLMs (GPT-4o, Claude, Gemini, Command-R, Jais, AceGPT, Qwen, open models) handle Algerian Darija — covering translation, sentiment, dialect identification, question answering, toxicity, instruction following, and code-switching (Arabic/French/Latin-script "Arabizi"). Ships as a HuggingFace dataset + an eval harness + a live HF Space leaderboard.

**3. Target users:** Arabic NLP researchers, multilingual model teams (Cohere, Mistral, Jais/Inception, AceGPT groups), companies deploying to North Africa, and anyone writing an Arabic-dialect paper who needs numbers.

**4. Why people care:** There is _no_ clean, trusted, code-switching-aware Darija benchmark. Every paper hand-rolls its own eval, which is unreproducible. A canonical benchmark becomes the thing people cite by default.

**5. Why it gets stars/adoption:** Leaderboards are inherently viral in ML — model builders _want_ to appear on them and will link back. "How does GPT-5 do on Algerian Darija? Here's a leaderboard" is instantly shareable. Benchmarks are the highest citation-per-effort artifact you can build.

**6. Differentiation:** Existing Arabic benchmarks (e.g., broad Arabic sets) mostly ignore _Maghrebi_ dialects and especially **Arabizi/code-switching**, which is how Algerians _actually_ write online. That's your wedge — the messy real-world register nobody benchmarks.

**7. Your unfair advantage:** You're a native/fluent Darija speaker doing a thesis on exactly this. You can judge quality, write gold references, and design tasks that non-speakers physically cannot. This is your single strongest edge.

**8. MVP scope:** 3-4 tasks, ~300-500 curated examples each (small is fine for a v1 benchmark), an eval script for open + API models, and a static leaderboard Space with 6-8 models scored. Ship a short paper/README with methodology.

**9. Long-term vision:** The default Maghrebi eval. Add human preference collection, a submission portal, expand to Moroccan/Tunisian, partner with WANLP shared tasks.

**10. Architecture:** HF Datasets for data → Python eval harness (async API calls + local HF inference) → results JSON → Gradio/Streamlit Space leaderboard reading from a HF dataset repo. Optionally `lm-evaluation-harness`-compatible task files so it plugs into existing tooling (big adoption multiplier).

**11. Tools/models:** HF Transformers, `lm-eval-harness`, API clients, small judge model or human labels. Test models: Qwen2.5, Jais, AceGPT, Command-R, plus API models.

**12. Compute:** Trivial. API calls + small local models on your 3050. This is a data + engineering project, not a training project. **Perfect fit for your constraints.**

**13. Risks:** Benchmark quality/contamination concerns; "LLM-as-judge for dialect" is noisy — you'll need human-verified gold to be credible. Scope creep on task count.

**14. Publication potential:** **Very high.** Direct fit for ArabicNLP/WANLP, LoResMT, or an EMNLP/ACL workshop. Dataset + benchmark papers cite well.

**15. Difficulty:** 4/10 technically, 7/10 on _quality/curation discipline_ (the hard part is rigor, not code).

> **This is my #1 recommendation.** It simultaneously maximizes citations, is directly your thesis-adjacent, is feasible on a laptop, and is a killer portfolio piece. Build this first.