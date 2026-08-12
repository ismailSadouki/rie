
Yes. Two separate things:

### 1. Add the checkpoint Dataset to a new Kaggle notebook

In Kaggle:

1. Open your **new notebook**.
    
2. On the right-hand side, find **Add Input**.
    
3. Click **Add Input**.
    
4. Select **Datasets**.
    
5. Search for:
    

```text
nano-llm-m46-checkpoints
```

6. You should see:
    

```text
nano-llm M4.6 Checkpoints
ismailsadouki/nano-llm-m46-checkpoints
```

7. Click **Add**.
    

Do the same for:

```text
nano-llm M4.6 Packed Training Data
ismailsadouki/nano-llm-m46-packed
```

After adding them, verify:

```python
!find /kaggle/input -maxdepth 5 -type f | head -30
```

You should see paths for both datasets.

---

### 2. Do you need to recreate `publish_checkpoint.py`?

**No, not if you pushed it to GitHub.**

This is exactly why we're keeping your code in Git.

When you start the new session:

```python
!git clone https://github.com/ismailSadouki/nano-llm-pretrain.git
%cd /kaggle/working/nano-llm-pretrain
```

your:

```text
scripts/publish_checkpoint.py
```

will come with the repository.

Check:

```python
!ls scripts/publish_checkpoint.py
```

If it exists, **do not create it again**.

If you haven't committed/pushed it to GitHub yet, then do that from your laptop first:

```bash
git add scripts/publish_checkpoint.py
git commit -m "Add Kaggle checkpoint publisher"
git push
```

Then the next Kaggle notebook gets it automatically when you clone.

### Your new-session setup is therefore:

```text
Kaggle notebook
│
├── Add Input
│   ├── nano-llm-m46-packed
│   └── nano-llm-m46-checkpoints
│
├── git clone GitHub repo
│
├── pip install -r requirements.txt
│
├── connect packed dataset → data/packed/
│
├── restore checkpoint if resuming
│
└── train.py --resume ...
```

**You only create `publish_checkpoint.py` once and keep it in GitHub.**