

**2. Description:** A small, focused Python library for evaluating LLM outputs in dialectal/low-resource languages, where standard metrics (BLEU, ROUGE) and English-centric LLM judges fail badly. Provides dialect-aware judges, transliteration-normalized matching, code-switch handling, and calibration against human labels.

**3. Target users:** Low-resource NLP researchers, anyone fine-tuning Arabic/African-language models who needs to _measure_ improvement.

**4. Why people care:** Everyone fine-tuning a Darija/Swahili/Hausa model hits the wall of "how do I even evaluate this?" Judges default to English norms and mis-score. This is a real, repeated complaint.

**5. Stars/adoption:** Dev tools that drop into an existing workflow (`pip install`, wrap your eval loop) spread fast if they solve a felt pain. Pairs naturally with Idea 1.

**6. Differentiation:** Existing eval libs (`deepeval`, `ragas`, `lm-eval`) are English/generic. None handle Arabizi normalization or dialect calibration.

**7. Your advantage:** You've lived the "my eval numbers are lying to me" problem in your thesis. You have human labels to calibrate against.

**8. MVP:** Transliteration/normalization utilities + one well-calibrated dialect judge + BLEU/chrF wrappers, with a notebook showing judge-vs-human correlation.

**9. Vision:** The go-to eval toolkit for dialectal generation; extend to other language families.

**10. Architecture:** Pure Python package, pluggable judge backends (API or local), HF integration.

**11. Tools:** Transformers, chrF/sacreBLEU, a judge model, your human-labeled set.

**12. Compute:** Trivial.

**13. Risks:** Hard to prove the judge is "better" without solid human correlation data — that's the whole credibility story.

**14. Publication:** Medium-high — a methods/resource paper. Strong when bundled with Idea 1.

**15. Difficulty:** 5/10.