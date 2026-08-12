
```
dziri-dpo/
├── configs/
│   ├── scratch_dpo.yaml
│   ├── english_smoke.yaml
│   ├── sft_baseline.yaml
│   ├── sft_sweep.yaml
│   └── dpo_sweep.yaml
│
├── scratch_dpo/
│   ├── dataset.py
│   ├── collator.py
│   ├── logprobs.py
│   ├── reference.py
│   ├── loss.py
│   └── train.py
│
├── darija_alignment/
│   ├── data/
│   │   ├── survey.md
│   │   ├── guideline.md
│   │   ├── build_sft.py
│   │   └── build_preferences.py
│   │
│   ├── annotation/
│   │   └── agreement.py
│   │
│   ├── sft.py
│   ├── dpo.py
│   └── evaluate.py
│
├── eval/
│   ├── preference_accuracy.py
│   ├── win_rate.py
│   ├── algerian_mmlu.py
│   ├── judge.py
│   └── qualitative.py
│
├── tests/
│   ├── test_masks.py
│   ├── test_logprobs.py
│   ├── test_reference.py
│   ├── test_dpo_loss.py
│   ├── test_trl_parity.py
│   └── test_overfit_tiny.py
│
├── scripts/
│   ├── prepare_english.py
│   ├── run_scratch_dpo.py
│   ├── run_sft.py
│   ├── run_dpo.py
│   └── evaluate.py
│
├── reports/
│   ├── scratch_parity.md
│   ├── dpo_health.md
│   ├── annotation_agreement.md
│   ├── sft_sweep.md
│   ├── dpo_sweep.md
│   ├── evaluation.md
│   └── failure_taxonomy.md
│
├── notes/
│   ├── decisions.md
│   ├── dpo-derivation.md
│   ├── data-guideline.md
│   ├── runs.md
│   └── bugs/
│
├── README.md
├── pyproject.toml
└── LICENSE
```

