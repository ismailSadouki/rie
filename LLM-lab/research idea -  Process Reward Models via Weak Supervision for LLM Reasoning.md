

1. **Title:** Bootstrapping Process Reward Models for LLM Reasoning without Human Step-Level Labels
2. **Core Question:** Can we train process reward models (PRMs) using only outcome supervision + Monte Carlo rollouts, matching human-labeled PRMs on GSM8K/MATH?
3. **Why it matters:** OpenAI's o1, DeepSeek-R1 lit up this field. PRM800K required massive human labeling.
4. **Gap:** Math-Shepherd (2024) showed MC labeling helps; efficiency + generalization remain open.
5. **Novelty:** Medium-High
6. **Difficulty:** 7/10
7. **Time:** 4 months
8. **Math:** Monte Carlo estimation, credit assignment
9. **Compute:** 2 GPUs (Qwen 1.5B or Llama-3-8B student)
10. **Datasets:** GSM8K, MATH, PRM800K for comparison
11. **Venue:** EMNLP, ACL, ICLR, NeurIPS ENLSP
12. **Risks:** DeepSeek R1 changed the game; you need a specific niche.
13. **PhD extension:** Automated verifier construction, RL for reasoning.

**Read first:** Lightman 2023 "Let's Verify Step by Step"; Wang 2024 "Math-Shepherd"; DeepSeek-R1; Snell 2024 "Scaling Test-Time Compute". **Saturation:** VERY high. Only pursue if you can move fast with a specific angle.