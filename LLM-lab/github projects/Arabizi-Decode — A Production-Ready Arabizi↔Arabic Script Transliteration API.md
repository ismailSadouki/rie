

**Description:** A lightweight, fast, accurate neural transliteration tool that converts Arabizi (Latin-script Arabic, e.g., "3lech ma jitch?" → "علاش ما جيتش؟") to proper Arabic script and vice versa, with dialect-awareness for Algerian/Moroccan/Tunisian variants. Packaged as a pip-installable library, a FastAPI microservice, and a HuggingFace Space demo.

**Target users:** NLP researchers needing to preprocess social media data, Algerian/Moroccan developers building apps, data engineers cleaning Arabic corpora, sociolinguistics researchers.

**Why people would care:** [41](https://github.com/darija-open-dataset/dataset)DODa takes into account the diversity of Darija spellings used in various contexts. The dataset includes entries written in both Latin and Arabic alphabets, reflecting the linguistic variations. A huge portion of North African online text is written in Arabizi, making it invisible to Arabic NLP tools. [18](https://huggingface.co/datasets/UBC-NLP/nilechat-arabizi-mor)The Arabizi-Morocco dataset (1.45 million rows) was transliterated using CohereLabs' model, and is primarily designed for pre-training large language models. Everyone doing Maghrebi NLP hits this wall.

**Why GitHub stars/adoption:** It's a utility tool. Utilities that save 10 hours of preprocessing get starred. Easy to try (paste text, get result). Easy to integrate (pip install + 3 lines of code).

**What makes it different:** Existing tools are either rule-based (break on ambiguous input) or trained only on Egyptian/Levantine Arabic. Maghrebi Arabizi has unique conventions (3 for ع, 9 for ق, French-influenced spellings) that require dialect-specific training.

**My unfair advantages:** Algerian native speaker, understands Arabizi conventions, NLP thesis work, ability to build production APIs (FastAPI + Docker), web dev skills for a polished demo.

**MVP scope:** (1) Collect/curate 5K–10K Arabizi↔Arabic parallel pairs from existing datasets + manual creation. (2) Fine-tune a small seq2seq model (mT5-small or ByT5-small — character-level model ideal for transliteration). (3) Package as `pip install arabizi-decode`. (4) Deploy FastAPI endpoint + HuggingFace Space.

**Long-term vision:** Support all Maghrebi dialects. Add contextual disambiguation. Become the standard preprocessing step in any Maghrebi NLP pipeline.

**Technical architecture:** ByT5-small fine-tuned on parallel Arabizi-Arabic data → Python package with simple API → FastAPI microservice → Docker container → HuggingFace Space demo.

**Required tools/models:** `transformers`, ByT5-small or mT5-small, `fastapi`, `uvicorn`, Docker.

**Compute requirements:** Low. ByT5-small fine-tunes on RTX 3050 easily. Inference is CPU-compatible.

**Main technical risks:** (1) Arabizi is inherently ambiguous (same Latin text can map to multiple Arabic words). (2) Need enough parallel data. (3) Handling code-switched French within Arabizi.

**Research/publication potential:** Medium-High. Publishable as a system paper or resource paper at ArabicNLP/WANLP workshop.

**Difficulty:** 5/10