


**Description:** A standardized, open-source benchmark suite for evaluating LLM capabilities in Algerian Darija (Darja/Dziri). Inspired by what Atlas-Chat did for Moroccan Darija — [39](https://arxiv.org/html/2409.17912v1)which developed benchmarks such as DarijaMMLU, DarijaHellaSwag, and DarijaBench to evaluate LLM capabilities in real-world knowledge, following Darija instructions, and performing traditional NLP tasks — this would be the Algerian equivalent covering knowledge, instruction following, sentiment analysis, translation, and cultural reasoning. No one has built this for Algeria yet.

**Target users:** Multilingual NLP researchers, Arabic AI labs (MBZUAI, CAMeL Lab, ARBML community), LLM developers evaluating models for North African markets, and Algerian tech companies.

**Why people would care:** [42](https://www.middleeastainews.com/p/atlasia-releases-smarter-moroccan-llms)The Arabic language is considered by developers to be a low-resource language and training data resources for local dialects are even scarcer. There is literally no way to systematically measure how well GPT-4, Llama, Qwen, or any model handles Algerian Darija. Anyone building for the 45M+ Algerian market is flying blind.

**Why it could gain GitHub stars/adoption:** Benchmarks become infrastructure. Every new model release triggers people running your benchmark. Every paper comparing models in Arabic needs dialect-specific benchmarks. [25](https://www.kdnuggets.com/top-5-open-source-llm-evaluation-platforms)The Language Model Evaluation Harness underlies the Hugging Face Open LLM Leaderboard and is used in the research community and cited by hundreds of papers. Your benchmark would fill a vacuum.

**What makes it different:** [39](https://arxiv.org/html/2409.17912v1)ArabicMMLU covers MSA across 40 tasks from eight different Arabic-speaking countries, but lumps all Arabic together. Atlas-Chat benchmarks are Moroccan-specific. No one has done Algerian-specific tasks covering code-switching (Arabic-French-Arabizi), cultural knowledge (history, geography, daily life), and dialect-specific reasoning.

**My unfair advantages:** You are Algerian, doing your thesis on Algerian Darija NLP, understand the linguistics, can create culturally valid questions, and have a statistics background to ensure methodological rigor.

**MVP scope:** Create 500–1,000 multiple-choice questions across 5 categories (Algerian culture/history, everyday reasoning in Darija, MSA→Darija translation evaluation, sentiment analysis in Darija, code-switched text understanding). Build a Python CLI to run any HuggingFace model against the benchmark. Publish on GitHub + HuggingFace Datasets.

**Long-term vision:** Expand to a live HuggingFace Space leaderboard. Add Tunisian and Libyan dialects. Become the standard citation for Maghrebi LLM evaluation. Partner with ARBML community.

**Technical architecture:** Python package with `lm-eval` or `lighteval` integration → HuggingFace Dataset (JSONL with metadata) → Streamlit/Gradio leaderboard Space → FastAPI evaluation API.

**Required tools/models:** `transformers`, `lighteval` or `lm-evaluation-harness`, `datasets`, Gradio/Streamlit, Python.

**Compute requirements:** Minimal — inference only. Can run small models on RTX 3050, use free Colab for larger ones, or call APIs for proprietary models.

**Main technical risks:** (1) Ensuring question quality and avoiding ambiguity. (2) Getting enough native speaker validators. (3) Marketing it to the right researchers.

**Research/publication potential:** Very high. Direct ACL/EMNLP workshop paper. Could submit to ArabicNLP workshop, AfricaNLP, or the main LoResLM workshop. [16](https://aclanthology.org/2025.loreslm-1.2/)Atlas-Chat was published at ACL's LoResLM workshop — your Algerian benchmark could follow the same path.

**Difficulty:** 6/10