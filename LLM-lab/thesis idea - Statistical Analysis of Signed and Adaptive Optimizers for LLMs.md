<mark>deep, novel, thesis-scale. best</mark>

- **Motivation:** Adam, Lion, signSGD dominate LLM training. Why? Existing theory (Bernstein 2018, Kunstner 2023) is partial. A rigorous statistical framework connecting gradient distribution properties to optimizer performance is missing.
- **Objectives:**
    1. Empirically characterize gradient distributions (heavy-tailedness, coordinate variance, alignment) across models and stages.
    2. Derive convergence bounds under specific noise models.
    3. Predict optimizer choice from gradient statistics.
- **Literature:** Bernstein 2018 signSGD; Chen 2023 Lion; Kunstner 2023; Simsekli 2019 heavy tails; Zhang 2020 "Why Attention Needs Adam."
- **Gap:** No unified framework linking noise statistics → optimizer choice for LLMs.
- **Methodology:**
    - Instrument training runs (GPT-2 small, Pythia)
    - Log per-coordinate gradient stats
    - Fit distributional models (Student-t, α-stable)
    - Predict Adam vs Lion vs signSGD gap
    - Prove convergence bounds under fitted noise
- **Tools:** High-dim stats, α-stable distributions, convergence analysis.
- **Datasets:** OpenWebText, C4 subsets.
- **Baselines:** SGD, Adam, Lion, signSGD, Adafactor.
- **Metrics:** Final loss, wall-clock, memory, coordinate-level convergence.
- **Timeline (7 months):**
    - M1: Reproduce baselines, instrument runs
    - M2: Gradient distribution analysis
    - M3–4: Theoretical bounds under noise models
    - M5: Predictive framework
    - M6: Larger-scale validation
    - M7: Write-up
- **Contributions:** First systematic statistical framework for optimizer selection in LLM training.
- **Publication:** AISTATS, TMLR, HiLD@ICML, OPT@NeurIPS.