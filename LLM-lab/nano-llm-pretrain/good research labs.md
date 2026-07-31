

# `decisions.md`

Instead of writing random notes like

```text
Changed vocab to 32k.
```

you keep a structured decision log.

| Date | Decision | Evidence | Consequence |
|------|----------|----------|-------------|
| 2026-08-02 | Use 32k vocabulary | Fertility dropped from 1.95 → 1.71 | Larger embedding matrix (+2M params) |
| 2026-08-04 | Switch to RoPE | LLaMA architecture; easier KV cache | Removed learned positional embeddings |
| 2026-08-10 | Use bf16 | Stable training and faster on T4 | fp32 checkpoints no longer produced |

The important thing is the **Evidence** column.

Don't write

> I like 32k.

Write

> Fertility experiment showed 1.71 vs 1.95.

Now every design choice is justified.

---

# `data-recipe.md`

Think of this as the **recipe** used to create your training data.

For example:

```text
Dataset:
FineWeb

↓

Language filter
English only

↓

Remove documents shorter than 200 characters

↓

Remove HTML

↓

Normalize Unicode (NFC)

↓

MinHash
threshold = 0.85

↓

Train tokenizer

↓

Pack into 1024-token sequences
```

If someone asks

> "How did you build your dataset?"

You point them here.

---

# `runs.md`

This is a chronological log of every important run.

Example:

```text
Run 001
---------
Config:
configs/train_baseline.yaml

Status:
Failed

Reason:
Loss became NaN after 300 steps.

----------

Run 002

Config:
configs/train_lr_3e4.yaml

Result:
Converged

Validation loss:
3.91

Checkpoint:
ckpt_step10000.pt
```

Think of it like a laboratory notebook.

---

# `bugs/`

Instead of forgetting bugs, document them.

Example

```
bugs/

BUG-001.md
BUG-002.md
BUG-003.md
```

---

`BUG-001.md`

```text
Problem

RoPE output was incorrect.

Cause

Applied rotation after attention scores.

Fix

Rotate Q and K before QK^T.

Lesson

Always verify tensor shapes.
```

---

Six months later, if you encounter the same bug, you'll solve it in minutes instead of hours.

---

# Reproducibility

This is **very important**.

Imagine someone clones your repository.

They shouldn't have to ask:

> "How do I reproduce the tokenizer experiment?"

Instead, your README (or Makefile) should contain exact commands.

Example:

```bash
python scripts/ingest.py \
    --config configs/data_filter_v0.yaml

python scripts/filter.py \
    --config configs/data_filter_v0.yaml

python scripts/dedup.py \
    --threshold 0.85

python scripts/train_tokenizer.py \
    --config configs/tokenizer_32k.yaml

python scripts/pack.py

python train.py \
    --config configs/train_first_full.yaml
```

Or even better, with a `Makefile`:

```bash
make ingest
make filter
make dedup
make tokenizer
make pack
make train
make evaluate
```

A new user can reproduce your pipeline without guessing.

---

# `snippets/`

These are reusable pieces of code you've written and may use in future projects.

For example:

```
snippets/

rope.py
gqa_repeat.py
train_loop_skeleton.py
mfu.py
bootstrap_ci.py
```

These are **not** part of the main model. They are your personal library of well-tested implementations.

- `rope.py` → reusable Rotary Position Embedding implementation.
- `gqa_repeat.py` → helper for repeating key/value heads in GQA.
- `train_loop_skeleton.py` → a clean template for training loops (optimizer, scheduler, AMP, checkpointing).
- `mfu.py` → compute **Model FLOPs Utilization**, useful for measuring training efficiency.
- `bootstrap_ci.py` → utility to compute **bootstrap confidence intervals** for metrics, making your experiment reporting statistically sound.

As you build more projects, you'll copy these snippets instead of rewriting them from scratch.



