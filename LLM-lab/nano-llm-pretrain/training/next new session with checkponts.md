
Yes. When you start a **new Kaggle session**, think of it as rebuilding the temporary `/kaggle/working` environment, while your persistent Kaggle Datasets remain safe.

For your M4.6 setup, use this order.

### 1. Attach your persistent datasets

In the new Kaggle notebook, add these inputs:

```text
ismailsadouki/nano-llm-m46-packed
ismailsadouki/nano-llm-m46-checkpoints
```

The first contains your packed training data.

The second contains your published checkpoints.

---

### 2. Clone your GitHub repository again

```python
!git clone https://github.com/ismailSadouki/nano-llm-pretrain.git
```

Then:

```python
%cd /kaggle/working/nano-llm-pretrain
```

Check:

```python
!git status
```

---

### 3. Install dependencies

```python
!pip install -r requirements.txt
```

Don't reinstall things unnecessarily if the environment already has compatible versions, but your `requirements.txt` is the reproducible source.

---

### 4. Make the packed data visible where your code expects it

Your `PackedDataset` expects:

```text
data/packed/train_input_ids.npy
data/packed/train_labels.npy
data/packed/train_loss_mask.npy
data/packed/val_input_ids.npy
...
```

So copy/symlink the persistent dataset into that location.

I recommend symlinks because your packed dataset is ~700 MB:

```python
!mkdir -p data/packed
```

Then:

```python
!ln -sf /kaggle/input/datasets/ismailsadouki/nano-llm-m46-packed/train_input_ids.npy data/packed/train_input_ids.npy
!ln -sf /kaggle/input/datasets/ismailsadouki/nano-llm-m46-packed/train_labels.npy data/packed/train_labels.npy
!ln -sf /kaggle/input/datasets/ismailsadouki/nano-llm-m46-packed/train_loss_mask.npy data/packed/train_loss_mask.npy

!ln -sf /kaggle/input/datasets/ismailsadouki/nano-llm-m46-packed/val_input_ids.npy data/packed/val_input_ids.npy
!ln -sf /kaggle/input/datasets/ismailsadouki/nano-llm-m46-packed/val_labels.npy data/packed/val_labels.npy
!ln -sf /kaggle/input/datasets/ismailsadouki/nano-llm-m46-packed/val_loss_mask.npy data/packed/val_loss_mask.npy
```

Verify:

```python
!ls -lh data/packed
```

---

### 5. Check the GPU

```python
import torch

print(torch.cuda.is_available())
print(torch.cuda.get_device_name(0))
```

You want:

```text
True
Tesla T4
```

Then:

```python
!nvidia-smi
```

---

### 6. Check your config

```python
!cat configs/train_first_full.yaml
```

**Do not change it.**

Your M4.6 experiment is frozen.

---

## 7. Now the important question: fresh run or resume?

### If your previous session died but you published a checkpoint

Attach:

```text
nano-llm-m46-checkpoints
```

Find the latest published run, for example:

```text
20260808_115737/
    latest.pt
    best.pt
    log.jsonl
    train.yaml
```

Copy the checkpoint into your new project:

```python
!mkdir -p runs/20260808_115737
```

Then:

```python
!cp /kaggle/input/.../20260808_115737/latest.pt \
    runs/20260808_115737/latest.pt
```

Then resume:

```python
!python train.py \
    --config configs/train_first_full.yaml \
    --resume runs/20260808_115737/latest.pt
```

Your code will restore:

```text
model weights
optimizer state
GradScaler state
step
best_val_loss
```

and continue from the saved step.

---

### If you are starting M4.6 for the first time

Don't use `--resume`.

Just:

```python
!python train.py \
    --config configs/train_first_full.yaml
```

---

## 8. During the run

Your normal training checkpointing handles:

```text
latest.pt
best.pt
log.jsonl
```

inside:

```text
runs/<RUN_ID>/
```

Every ~5,000 steps, publish the current run:

```python
!python scripts/publish_checkpoint.py \
    runs/<RUN_ID>
```

For example:

```python
!python scripts/publish_checkpoint.py \
    runs/20260808_115737
```

That creates a new **version** of:

```text
ismailsadouki/nano-llm-m46-checkpoints
```

with the newest checkpoint.

---

## The whole lifecycle

```text
                 PERSISTENT
        ┌──────────────────────────┐
        │ Kaggle Dataset           │
        │                          │
        │ packed data              │
        │ checkpoints              │
        └────────────┬─────────────┘
                     │
                  attach
                     │
                     ▼
        ┌──────────────────────────┐
        │ /kaggle/working          │
        │                          │
        │ clone GitHub repo        │
        │ install requirements     │
        │ symlink packed data      │
        │ run training             │
        └────────────┬─────────────┘
                     │
                     ▼
                training
                     │
             latest.pt / best.pt
                     │
                     ▼
          publish_checkpoint.py
                     │
                     ▼
        ┌──────────────────────────┐
        │ Kaggle Dataset version   │
        │                          │
        │ persistent checkpoint    │
        └──────────────────────────┘
```

So **you don't need to upload the whole project every time**.

Your GitHub repository provides the **code/config**.

Your Kaggle Dataset provides the **large data + persistent checkpoints**.

That's the clean setup for M4.6.