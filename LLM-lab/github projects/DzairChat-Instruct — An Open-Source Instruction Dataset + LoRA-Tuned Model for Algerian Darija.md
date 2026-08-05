

**Description:** Build the Algerian equivalent of what AtlasIA did for Morocco: [39](https://arxiv.org/html/2409.17912v1)construct an instruction dataset by consolidating existing Darija language resources, creating novel datasets both manually and synthetically, and translating English instructions under strict quality control. Create 10K–50K instruction-response pairs in Algerian Darija, then LoRA-fine-tune a small model (Qwen2.5-3B or Gemma-2-2B) to follow Algerian Darija instructions. Publish the dataset AND the adapter.

**Target users:** Algerian developers, chatbot builders targeting Algeria, researchers studying dialectal Arabic, Algerian students learning AI.

**Why people would care:** [21](https://arxiv.org/pdf/2505.17082)Arabic LLMs have advanced rapidly, driven by digital sovereignty and cultural goals. Yet, Moroccan Arabic (Darija) remains underrepresented. Most work targets MSA or pan-Arabic models. If this is true for Moroccan, it's 10x more true for Algerian. There is no Atlas-Chat for Algeria.

**Why GitHub stars/adoption:** The Atlas-Chat models have hundreds of downloads on HuggingFace. [35](https://huggingface.co/collections/BounharAbdelaziz/moroccan-darija-llms)The Moroccan Darija models on HuggingFace show significant community engagement with hundreds of downloads and dozens of likes. An Algerian version would attract the entire Algerian tech community plus international researchers.

**What makes it different:** [37](https://openreview.net/pdf?id=BMVq5MELb9)DziriBERT exists for Algerian Darja, but DarijaBERT and all three language models are trained on social media data only. You'd be making the first _instruction-tuned, conversational_ Algerian model, with DPO alignment from your thesis work.

**My unfair advantages:** DPO/GRPO thesis expertise, Algerian native speaker, LoRA/PEFT skills, ability to build complete end-to-end products with a web demo.

**MVP scope:** (1) Curate 10K instruction pairs (50% synthetic via GPT-4/Claude translation + human review, 30% manually created, 20% from existing Algerian corpora). (2) LoRA-fine-tune Qwen2.5-3B or Gemma-2-2B. (3) Optionally apply DPO with 1K preference pairs. (4) Deploy on HuggingFace Space with Gradio chat UI.

**Long-term vision:** Grow to a full model family. Add speech (Whisper fine-tuned on Algerian). Become the reference Algerian AI project.

**Technical architecture:** Data pipeline (Python scripts + GPT-4 API for synthetic generation + Argilla for annotation) → LoRA fine-tuning (PyTorch + PEFT + TRL) → DPO alignment → Gradio demo on HF Spaces.

**Required tools/models:** `transformers`, `peft`, `trl`, `datasets`, Argilla or Label Studio, Qwen2.5-3B / Gemma-2-2B base model.

**Compute requirements:** LoRA fine-tuning of a 2-3B model fits on RTX 3050 (4-bit quantized). DPO training feasible on Kaggle/Colab T4.

**Main technical risks:** (1) Quality control of synthetic data is the biggest risk. (2) A 3B model may not be impressive enough. (3) Requires sustained effort for data curation.

**Research/publication potential:** Very high. Direct paper at ArabicNLP workshop, LoResLM, or AfricaNLP. The DPO-for-dialects angle is novel and aligns with your thesis.

**Difficulty:** 7/10