
Exactly. Since you're using a **Kaggle notebook + Kaggle Dataset**, after a session restart you need to recreate the runtime environment, but you **do not need to regenerate or re-upload the packed dataset**.

Use this checklist every time.

### 1. Verify the GPU

```python
import torch

print(torch.cuda.is_available())
print(torch.cuda.get_device_name(0))
```

Expected:

```text
True
Tesla T4
```

### 2. Go to your repository

If the repo is not already in `/kaggle/working`:

```python
%cd /kaggle/working
!git clone https://github.com/ismailSadouki/nano-llm-pretrain.git
%cd nano-llm-pretrain
```

If it already exists:

```python
%cd /kaggle/working/nano-llm-pretrain
!git pull
```

Because M4.6 is frozen, **don't change the code/config after this point**.

### 3. Install dependencies

Since you committed `requirements.txt`:

```python
!pip install -r requirements.txt
```

If you already installed them in the current session, you don't need to repeat it.

### 4. Attach the Kaggle Dataset

Your dataset is:

```text
ismailsadouki/nano-llm-m46-packed
```

After adding it as an input, verify:

```python
!find /kaggle/input -maxdepth 5 -type f | head -20
```

You should see:

```text
/kaggle/input/datasets/ismailsadouki/nano-llm-m46-packed/train_input_ids.npy
...
```

### 5. Recreate `data/packed`

Your `PackedDataset` expects:

```text
data/packed/train_input_ids.npy
data/packed/train_labels.npy
...
```

but Kaggle mounts the dataset under `/kaggle/input/...`.

So after every fresh session:

```python
%cd /kaggle/working/nano-llm-pretrain

!mkdir -p data
!rm -rf data/packed
!ln -s /kaggle/input/datasets/ismailsadouki/nano-llm-m46-packed data/packed
```

Verify:

```python
!ls -lh data/packed
```

### 6. Test the loader

Do this **before training**:

```python
from utils.data import PackedDataset
import torch

train_dataset = PackedDataset("train")
val_dataset = PackedDataset("val")

print("train:", len(train_dataset))
print("val:", len(val_dataset))

x, y, mask = train_dataset.get_batch(
    batch_size=8,
    device=torch.device("cuda")
)

print(x.shape)
print(y.shape)
print(mask.shape)
```

Expected shapes:

```text
torch.Size([8, 1024])
torch.Size([8, 1024])
torch.Size([8, 1024])
```

### 7. Check the config

```python
!cat configs/train_first_full.yaml
```

Make sure it's still your frozen M4.6 configuration:

```text
20,610,880 parameters
12 layers
d_model=320
8 heads
4 KV heads
batch_size=8
gradient_accumulation_steps=4
...
```

Don't modify it just because the session restarted.

### 8. Check that CUDA can actually run the model

Then:

```python
!python train.py --config configs/train_first_full.yaml
```

For the **first session after restarting**, let it run through step 0.

You want:

```text
Device      : cuda
AMP dtype   : torch.bfloat16
GradScaler  : disabled

Layers:        12
Hidden dim:    320
Heads:         8
KV heads:      4

Parameters:
20,610,880

step      0 | train ... | val ... | lr ...
Saved best checkpoint at step 0
```

Then check the run directory:

```python
!find runs -maxdepth 2 -type f | sort
```

---

## But there's one important difference after a restart

If **M4.6 is already running** and the Kaggle session dies/timeouts, **do NOT start a new run from step 0.**

You resume from the latest checkpoint.

For example:

```python
!python train.py \
    --config configs/train_first_full.yaml \
    --resume runs/20260807_XXXXXX/latest.pt
```

Your code does:

```python
start_step = ckpt["step"] + 1
```

so if the checkpoint contains:

```text
step = 12499
```

training resumes at:

```text
step = 12500
```

with the model, optimizer, and GradScaler state restored.

### Your restart routine, condensed

```text
Kaggle session starts
        ↓
GPU check
        ↓
clone/pull repo
        ↓
pip install requirements
        ↓
attach nano-llm-m46-packed
        ↓
recreate data/packed symlink
        ↓
test PackedDataset
        ↓
check frozen config
        ↓
FIRST RUN? ── yes → python train.py ...
        │
        └─ no → python train.py ... --resume latest.pt
```

**The 551 MB packed dataset survives because it's a Kaggle Dataset.** The `/kaggle/working` directory itself is disposable, which is why the symlink needs to be recreated after a new session.