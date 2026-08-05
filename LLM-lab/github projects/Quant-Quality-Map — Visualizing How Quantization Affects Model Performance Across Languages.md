

**Description:** A study + interactive tool showing how 4-bit, 8-bit, and GPTQ/AWQ quantization degrades model quality differently across languages. Hypothesis: quantization hurts low-resource languages disproportionately.

**Target users:** ML engineers deploying multilingual models, researchers studying quantization effects. **MVP:** Run 5 models × 4 quantization levels × 10 languages. Interactive Streamlit dashboard showing results. **Compute:** Moderate (inference at multiple quantization levels on free-tier GPUs). **Research potential:** High. Novel finding if confirmed. **Difficulty:** 6/10