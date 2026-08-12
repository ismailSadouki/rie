Yes. There are **two separate things** here:

1. `audit_collator.py` → verifies that your **batch tensors** are correct.
    
2. 50 manually inspected examples → verifies that the **preference labels themselves** look reasonable.
    

Let's implement both.

## 1. `scripts/audit_collator.py`

This script should use your **real tokenizer + real fixture + real `DPODataCollator`**, not synthetic examples.

Create:

```text
scripts/audit_collator.py
```

```python
import json
from pathlib import Path

from transformers import AutoTokenizer

from scratch_dpo.collator import DPODataCollator
from scratch_dpo.data import tokenize_pair


MODEL_NAME = "Qwen/Qwen2.5-0.5B-Instruct"

FIXTURE_PATH = Path(
    "tests/fixtures/tiny_preferences.jsonl"
)

REPORT_PATH = Path(
    "reports/collated_batch_audit.md"
)

MAX_LENGTH = 512


def load_examples():
    rows = []

    for line in FIXTURE_PATH.read_text(
        encoding="utf-8"
    ).splitlines():
        if line.strip():
            rows.append(json.loads(line))

    return rows


def token_region(
    attention: int,
    loss_mask: int,
) -> str:
    if attention == 0:
        return "PAD"

    if loss_mask == 1:
        return "R"

    return "P"


def decode_batch(
    tokenizer,
    input_ids,
    attention_mask,
    loss_mask,
):
    lines = []

    for position, (
        token_id,
        attention,
        loss,
    ) in enumerate(
        zip(
            input_ids,
            attention_mask,
            loss_mask,
        )
    ):
        token_text = tokenizer.decode(
            [token_id],
            skip_special_tokens=False,
        )

        token_text = (
            token_text
            .replace("\n", "\\n")
            .replace("\t", "\\t")
        )

        region = token_region(
            int(attention),
            int(loss),
        )

        lines.append(
            f"{position:04d} | "
            f"{region:3s} | "
            f"{token_id:6d} | "
            f"{token_text!r}"
        )

    return "\n".join(lines)


def validate_batch(batch):
    required = {
        "chosen_input_ids",
        "chosen_attention_mask",
        "chosen_loss_mask",
        "rejected_input_ids",
        "rejected_attention_mask",
        "rejected_loss_mask",
    }

    assert required.issubset(batch.keys())

    chosen_ids = batch["chosen_input_ids"]
    chosen_attention = batch["chosen_attention_mask"]
    chosen_loss = batch["chosen_loss_mask"]

    rejected_ids = batch["rejected_input_ids"]
    rejected_attention = batch["rejected_attention_mask"]
    rejected_loss = batch["rejected_loss_mask"]

    # All tensors must have the same shape within
    # their chosen/rejected branches.
    assert chosen_ids.shape == chosen_attention.shape
    assert chosen_ids.shape == chosen_loss.shape

    assert rejected_ids.shape == rejected_attention.shape
    assert rejected_ids.shape == rejected_loss.shape

    # Chosen and rejected must have a shared padded
    # sequence length.
    assert chosen_ids.shape[1] == rejected_ids.shape[1]

    # Padding must never contribute to the loss.
    assert (
        chosen_loss[chosen_attention == 0] == 0
    ).all()

    assert (
        rejected_loss[rejected_attention == 0] == 0
    ).all()

    # Loss mask can only contain 0 or 1.
    assert set(chosen_loss.unique().tolist()) <= {0, 1}
    assert set(rejected_loss.unique().tolist()) <= {0, 1}

    # Attention mask can only contain 0 or 1.
    assert set(
        chosen_attention.unique().tolist()
    ) <= {0, 1}

    assert set(
        rejected_attention.unique().tolist()
    ) <= {0, 1}


def main():
    tokenizer = AutoTokenizer.from_pretrained(
        MODEL_NAME,
        use_fast=True,
    )

    if tokenizer.pad_token_id is None:
        raise RuntimeError(
            "Tokenizer has no pad_token_id."
        )

    tokenizer.padding_side = "right"

    raw_examples = load_examples()

    tokenized_examples = [
        tokenize_pair(
            tokenizer=tokenizer,
            prompt=row["prompt"],
            chosen=row["chosen"],
            rejected=row["rejected"],
            max_length=MAX_LENGTH,
        )
        for row in raw_examples
    ]

    collator = DPODataCollator(
        pad_token_id=tokenizer.pad_token_id
    )

    batch = collator(tokenized_examples)

    validate_batch(batch)

    lines = [
        "# Collated Batch Audit",
        "",
        f"Model: `{MODEL_NAME}`",
        "",
        f"Batch size: `{len(raw_examples)}`",
        "",
        f"Chosen shape: `{tuple(batch['chosen_input_ids'].shape)}`",
        "",
        f"Rejected shape: "
        f"`{tuple(batch['rejected_input_ids'].shape)}`",
        "",
        f"Padding token ID: `{tokenizer.pad_token_id}`",
        "",
        "## Mask semantics",
        "",
        "- `P` = prompt token, loss mask = 0",
        "- `R` = response token, loss mask = 1",
        "- `PAD` = padding token, attention mask = 0, loss mask = 0",
        "",
        "## Invariants",
        "",
        "- Chosen and rejected have the same padded length.",
        "- Attention masks contain only 0/1.",
        "- Loss masks contain only 0/1.",
        "- Padding positions have loss mask = 0.",
        "",
    ]

    for index, row in enumerate(raw_examples):

        lines.extend([
            f"## Example {index + 1}: `{row['id']}`",
            "",
            "### Chosen",
            "",
            "```text",
            decode_batch(
                tokenizer,
                batch["chosen_input_ids"][index].tolist(),
                batch["chosen_attention_mask"][index].tolist(),
                batch["chosen_loss_mask"][index].tolist(),
            ),
            "```",
            "",
            "### Rejected",
            "",
            "```text",
            decode_batch(
                tokenizer,
                batch["rejected_input_ids"][index].tolist(),
                batch["rejected_attention_mask"][index].tolist(),
                batch["rejected_loss_mask"][index].tolist(),
            ),
            "```",
            "",
        ])

    REPORT_PATH.parent.mkdir(
        parents=True,
        exist_ok=True,
    )

    REPORT_PATH.write_text(
        "\n".join(lines),
        encoding="utf-8",
    )

    print(
        f"Collator audit written to {REPORT_PATH}"
    )


if __name__ == "__main__":
    main()
```

Run:

```bash
python scripts/audit_collator.py
```

Then:

```bash
less reports/collated_batch_audit.md
```

or:

```bash
code reports/collated_batch_audit.md
```

You should see something like:

```text
0000 | P   | ...
0001 | P   | ...
...
0015 | R   | ...
0016 | R   | ...
0017 | PAD | ...
0018 | PAD | ...
```

The critical property is:

```text
region   attention_mask   loss_mask

P        1                0
R        1                1
PAD      0                0
```

If you see:

```text
PAD      0                1
```

that's a **bug**.

---

# 2. What does "manually inspect 50 labels" mean?

This is **not** 50 tokenization examples.

It means you inspect **50 preference pairs** and ask:

> Given this prompt, is `chosen` actually better than `rejected`?

For example:

### Good

```text
Prompt:
What is the capital of France?

Chosen:
The capital of France is Paris.

Rejected:
The capital of France is Berlin.
```

Clearly:

```text
chosen > rejected
```

### Bad / ambiguous

```text
Prompt:
Explain machine learning.

Chosen:
Machine learning allows computers to learn patterns from data.

Rejected:
Machine learning is a method where algorithms learn from examples.
```

Both are reasonable.

You would mark this:

```text
ambiguous
```

### Obviously problematic

```text
Prompt:
What is 2 + 2?

Chosen:
2 + 2 equals 4.

Rejected:
2 + 2 equals 4.
```

This should have been caught by your validator as:

```text
chosen == rejected
```

### Another problem

```text
Prompt:
What is Python?

Chosen:
Python is a programming language.

Rejected:
I don't know.
```

This is a valid preference pair, but potentially **too trivial** for some analyses.

---

# 3. Where do the 50 examples come from?

Your M1.4 says:

> Audit 50 labels if using public data or at least 20 for the smoke slice.

There is an important distinction.

Your current:

```text
tests/fixtures/tiny_preferences.jsonl
```

contains only **3 examples**.

You cannot inspect 50 from that fixture.

For the **M1.4 smoke stage**, inspect your 3 fixture examples plus your English dataset slice if you've already prepared it.

If you haven't downloaded/prepared the 2,000-example HH-RLHF slice yet, **don't pretend you've inspected 50**.

I'd do this:

```text
M1.4 now:
    3/3 fixture examples manually inspected
    ↓
prepare 2,000 English examples
    ↓
sample deterministic 50
    ↓
manually inspect those 50
```

---

# 4. Make the inspection reproducible

Create:

```text
reports/manual_label_audit.md
```

Don't just look at examples and say "looks fine."

Create a table:

```markdown
# Manual Label Audit

Source: Anthropic/hh-rlhf
Subset: helpful-base
Split: train
Sample size: 50
Sampling seed: 42

| # | ID | Label | Reason |
|---|---|---|---|
| 1 | ... | OK | Chosen directly answers the prompt |
| 2 | ... | OK | Chosen is more accurate |
| 3 | ... | AMBIGUOUS | Both responses are reasonable |
| 4 | ... | OK | Chosen is more complete |
| 5 | ... | BAD | Rejected appears preferable |
```

Use a small taxonomy:

```text
OK
AMBIGUOUS
BAD
```

### `OK`

The chosen response is clearly preferable.

### `AMBIGUOUS`

Both responses are reasonably good or the preference is subjective.

### `BAD`

The rejected response appears better, or the chosen response is clearly incorrect.

---

# 5. What are you trying to learn from the 50?

At the end, calculate:

```text
OK          45
AMBIGUOUS    4
BAD          1
----------------
TOTAL       50
```

Then:

# [  
\text{clear preference rate}

\frac{N_{\mathrm{OK}}}{N}  
]

For this hypothetical example:

[  
\frac{45}{50}=0.90  
]

You don't need an arbitrary threshold like "must be >95%" unless you define one beforehand. The purpose is to **characterize the data**, not manufacture a passing score.

Also record recurring problems:

```text
Observed issues:
- some chosen responses substantially longer
- occasional vague prompts
- occasional nearly identical responses
- some preferences depend on stylistic judgment
```

This becomes useful later when interpreting DPO results.

---

# 6. One important correction to your current M1.4 scope

You don't need to manually inspect **50 labels before you can test the collator**.

The order should be:

```text
1. Implement collator
        ↓
2. Test unequal lengths
        ↓
3. Test padding masks
        ↓
4. Generate collated audit
        ↓
5. Manually inspect 3 fixture examples
        ↓
6. Prepare the actual English slice
        ↓
7. Manually inspect 50 public-data labels
        ↓
8. Write data card
        ↓
9. pytest -q
        ↓
10. M1.4 COMPLETE
```

So **right now**, implement `audit_collator.py`, run it, inspect the 3 examples, and make sure your `test_collator.py` passes.

Then we can prepare the deterministic 50-example audit of the actual HH-RLHF slice rather than manually inventing 50 examples.