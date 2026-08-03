

- **Motivation:** Second-order methods (Shampoo, SOAP, Muon) are winning at LLM training, but memory cost prohibitive.
- **Objectives:**
    1. Design sketched/low-rank preconditioners.
    2. Prove convergence under approximation error.
    3. Benchmark memory vs. speed vs. quality.
- **Literature:** Shampoo (Gupta 2018), SOAP (Vyas 2024), K-FAC, Muon (Jordan 2024), GaLore (Zhao 2024).
- **Gap:** Preconditioner sketching is understudied.
- **Methodology:** Nyström/CountSketch approximations of Shampoo blocks; convergence proof under noise; empirical validation on Pythia-410M.
- **Baselines:** AdamW, Shampoo, SOAP, GaLore, Adafactor.
- **Metrics:** Loss curves, memory footprint, wall-clock.
- **Publication:** MLSys, TMLR, OPT workshop.
- **Very strong fit.**