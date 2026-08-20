Yes. **M4.1 is where Track A becomes an actual training system.** We should build it incrementally rather than writing a 300-line training script immediately.

The core loop we want is:

```text
batch
  │
  ├── chosen/rejected
  │
  ▼
concatenated_forward()
  │
  ├── policy_chosen_logps
  └── policy_rejected_logps
  │
  ▼
reference_forward() ── no_grad
  │
  ├── ref_chosen_logps
  └── ref_rejected_logps
  │
  ▼
dpo_loss()
  │
  ├── loss
  ├── chosen_reward
  ├── rejected_reward
  ├── margin
  └── reward_accuracy
  │
  ▼
backward()
  │
  ▼
gradient clipping
  │
  ▼
optimizer.step()
  │
  ▼
metrics + checkpoint
```

## Step 1 — define the training contract

Before `train_scratch_dpo.py`, create:

```text
configs/english_smoke.yaml
```

Start deliberately small:

```yaml
seed: 42

model:
  name: "Qwen/Qwen2.5-0.5B-Instruct"

training:
  output_dir: "outputs/dpo_english_smoke"

  num_epochs: 1
  max_steps: 50

  batch_size: 2
  gradient_accumulation_steps: 1

  learning_rate: 5.0e-5
  weight_decay: 0.0

  beta: 0.1
  max_grad_norm: 1.0

  eval_every_steps: 10
  save_every_steps: 25
  log_every_steps: 1

  tiny_overfit: false

data:
  max_length: 512

checkpoint:
  resume_from: null
```

The important thing here is that **training behavior is configuration, not hard-coded inside the loop**.

---

# Step 2 — understand what M4.1 actually needs

Your training script needs these objects:

```python
tokenizer
model
optimizer
train_dataloader
eval_dataloader
```

and your existing functions:

```python
concatenated_forward()
reference_forward()
dpo_loss()
```

So your training step should eventually be approximately:

```python
policy_chosen_logps, policy_rejected_logps = concatenated_forward(
    model,
    batch,
)

reference_chosen_logps, reference_rejected_logps = ...
```

Then:

```python
loss, chosen_rewards, rejected_rewards, margin, reward_accuracy = dpo_loss(
    policy_chosen_logps,
    policy_rejected_logps,
    reference_chosen_logps,
    reference_rejected_logps,
    beta=config["training"]["beta"],
)
```

Then:

```python
loss.backward()
```

then:

```python
torch.nn.utils.clip_grad_norm_(
    trainable_parameters,
    max_grad_norm,
)
```

then:

```python
optimizer.step()
optimizer.zero_grad()
```

---

# Step 3 — the reference model is especially important

Your reference computation must be:

```python
with torch.no_grad():
    reference_outputs = reference_forward(
        model,
        model_inputs,
    )
```

or, if your `reference_forward()` already owns the `no_grad()` context, don't duplicate it.

The important invariant is:

$$  
\nabla_\theta \log \pi_{\mathrm{ref}}(y|x)=0  
$$

The reference model exists only to provide the baseline log-probabilities.

The policy is the thing being optimized.

---

# Step 4 — only train intended parameters

This is particularly important because you're using LoRA.

Don't do:

```python
optimizer = AdamW(model.parameters(), ...)
```

blindly.

Instead:

```python
trainable_parameters = [
    p for p in model.parameters()
    if p.requires_grad
]
```

Then:

```python
optimizer = torch.optim.AdamW(
    trainable_parameters,
    lr=learning_rate,
    weight_decay=weight_decay,
)
```

This gives us an invariant:

```text
requires_grad=True
        ↓
optimizer
        ↓
updated
```

Everything else should remain unchanged.

---

# Step 5 — metrics

M4.1 specifically says metrics are mandatory.

At every logging step, record at least:

```text
loss
reward_accuracy
margin
chosen_reward
rejected_reward
policy_chosen_logp
policy_rejected_logp
reference_chosen_logp
reference_rejected_logp
KL proxy
chosen length
rejected length
```

The most important conceptual metric is:

# $$  
\text{reward accuracy}

\frac{1}{B}  
\sum_{i=1}^{B}  
\mathbf{1}  
\left[  
r_i^{chosen}>r_i^{rejected}  
\right]  
$$

where:

# $$  
r^{chosen}

## \beta  
\left(  
\log\pi_\theta(y_w|x)

\log\pi_{\mathrm{ref}}(y_w|x)  
\right)  
$$

and similarly:

# $$  
r^{rejected}

## \beta  
\left(  
\log\pi_\theta(y_l|x)

\log\pi_{\mathrm{ref}}(y_l|x)  
\right)  
$$

Your `dpo_loss()` already computes these.

---

# Step 6 — KL proxy

For M4.1, we don't need to implement a full token-level KL estimator.

A useful sequence-level proxy is:

# $$  
\Delta_{\mathrm{KL}}

## \frac{1}{B}  
\sum_{i=1}^{B}  
\left[  
\log\pi_\theta(y_i|x_i)

\log\pi_{\mathrm{ref}}(y_i|x_i)  
\right]  
$$

For chosen responses:

```python
chosen_kl_proxy = (
    policy_chosen_logps
    - reference_chosen_logps
).mean()
```

For rejected:

```python
rejected_kl_proxy = (
    policy_rejected_logps
    - reference_rejected_logps
).mean()
```

We should label these clearly as **proxies**, not claim they are the exact sequence KL divergence.

---

# Step 7 — lengths

You already have:

```python
chosen_loss_mask
rejected_loss_mask
```

So:

```python
chosen_lengths = batch["chosen_loss_mask"].sum(dim=1)
rejected_lengths = batch["rejected_loss_mask"].sum(dim=1)
```

Then log:

```python
chosen_lengths.float().mean()
rejected_lengths.float().mean()
```

This will become particularly important in **M4.3**, where we investigate length drift.

---

# Step 8 — checkpoint

Don't save only the model.

A checkpoint should contain:

```python
checkpoint = {
    "step": step,
    "model": model.state_dict(),
    "optimizer": optimizer.state_dict(),
    "config": config,
    "seed": seed,
}
```

Then:

```python
torch.save(
    checkpoint,
    checkpoint_path,
)
```

When resuming:

```python
checkpoint = torch.load(
    checkpoint_path,
    map_location="cpu",
)

model.load_state_dict(
    checkpoint["model"]
)

optimizer.load_state_dict(
    checkpoint["optimizer"]
)

step = checkpoint["step"]
```

The key idea is:

> **Resume means resume the training state, not merely reload the model weights.**

---

# Step 9 — tiny overfit mode

This is one of the most useful parts of M4.1.

Instead of training on the whole dataset:

```python
if tiny_overfit:
    train_dataset = train_dataset.select(
        range(4)
    )
```

Then repeatedly train on those same examples.

We want to observe something like:

```text
step     loss
----------------
0        0.69
10       0.54
20       0.31
30       0.14
40       0.06
50       0.02
```

The exact numbers don't matter.

The important property is:

$$  
L_{t+1}<L_t  
$$

for the tiny overfit experiment.

If the loss doesn't decrease on four examples, **do not proceed to a full run**. Something in the training machinery is wrong.

---

# What I recommend we implement now

Don't write the entire script yet.

Create these two files first:

```text
train_scratch_dpo.py
tests/test_training_step.py
```

And implement **one function** in `train_scratch_dpo.py`:

```python
def training_step(
    model,
    batch,
    optimizer,
    beta,
    max_grad_norm,
):
    ...
```

Its contract should be:

```text
input:
    model
    batch
    optimizer
    beta
    max_grad_norm

output:
    metrics
```

Internally:

```text
zero_grad
   ↓
policy forward
   ↓
reference forward [NO GRAD]
   ↓
DPO loss
   ↓
backward
   ↓
clip
   ↓
optimizer step
   ↓
metrics
```

Then `tests/test_training_step.py` verifies the most important invariant:

> **one training step changes trainable policy parameters and does not change frozen/reference parameters.**

Once that passes, we wrap it in the 50-step training loop, then checkpointing, then logging, then tiny-overfit mode.

**That is the cleanest way to attack M4.1 without turning the first training script into an un-debuggable monolith.**