

- **Motivation:** OPE is used everywhere in offline RL for model selection, but confidence intervals lack finite-sample guarantees. Conformal prediction offers a distribution-free alternative.
- **Objectives:**
    1. Develop conformal wrappers for FQE, WIS, DR estimators.
    2. Handle exchangeability violations via weighted/adaptive conformal.
    3. Evaluate on D4RL and healthcare simulators.
- **Literature direction:** Conformal prediction (Angelopoulos), OPE (Precup, Voloshin), high-confidence OPE (Thomas), weighted conformal (Tibshirani 2019).
- **Gap:** Existing conformal OPE assumes stationarity; deep OPE with distribution shift is unresolved.
- **Methodology:**
    - Reproduce OPE baselines on D4RL
    - Build conformal wrappers with adaptive scoring
    - Establish coverage vs. width tradeoffs
    - Apply to policy selection
- **Environments:** D4RL, RL Unplugged, Sepsis simulator.
- **Baselines:** Bootstrap CIs, HCOPE, delta-method.
- **Metrics:** Empirical coverage of nominal 90/95%, interval width, regret in downstream policy selection.
- **Timeline (7 months):**
    - M1: Reproduce OPE baselines
    - M2: Vanilla conformal + coverage tests
    - M3–4: Adaptive/weighted conformal
    - M5: Healthcare experiments
    - M6: Policy selection application
    - M7: Write-up
- **Contributions:** First deep-RL conformal OPE library; empirical study of coverage properties.
- **Publication:** UAI, AISTATS, TMLR, ML4H.
- **This is my top pick for you.**