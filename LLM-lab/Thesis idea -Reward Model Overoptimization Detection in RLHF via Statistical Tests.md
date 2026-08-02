
- **Motivation:** RLHF reward hacking is a well-known problem; detection is ad hoc.
- **Objectives:**
    1. Characterize reward model drift during PPO/GRPO training.
    2. Build MMD/kernel-based online detectors.
    3. Trigger early stopping / KL adjustment.
- **Literature:** Gao 2023 scaling laws; Coste 2024 ensembles; Moskovitz 2024 constrained RLHF.
- **Gap:** Statistical detection framework does not exist.
- **Methodology:** Train PPO on small LMs (Pythia-1B); monitor via MMD; compare with ensemble disagreement.
- **Datasets:** UltraFeedback, HH-RLHF.
- **Baselines:** Fixed KL, ensemble disagreement, WARM (2024).
- **Metrics:** Correlation with gold reward, detection latency, downstream win rate.
- **Timeline (7 months):** Standard.
- **Publication:** EMNLP, ACL Findings, ICLR workshop.
- **Excellent LLM + stats fit.**

---