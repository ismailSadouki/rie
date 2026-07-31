Excellent question. The answer is:

> **You don't know exactly. You estimate.**

This is exactly what large labs do during planning.

### Option 1 (Best for FineWeb)

FineWeb already provides a `token_count` field for **each document**, computed with the GPT-2 tokenizer.

From the dataset card:

> `token_count (int): number of tokens when applying the GPT-2 tokenizer`

That means you can estimate your target **without training your own tokenizer**.

For example, if you want about 200M tokens, you can stream documents and keep a running sum of `token_count` until you reach ~200M.

Later, after training your own tokenizer, you'll know the exact token count for your tokenizer.

---

### Option 2 (Character estimate)

If a dataset doesn't provide token counts, people often estimate from text length.

A rough English rule is:

```text
1 token ≈ 4 characters
```

So:

```text
800 million characters
≈ 200 million tokens
```

This is only for planning.

---

### Option 3 (Sample and extrapolate)

Suppose you have no token counts.

You can tokenize a small sample (e.g., 10,000 documents) with your final tokenizer later:

```text
10,000 docs
↓

2.1M tokens

↓

210 tokens/document (average)
```

Then estimate:

```text
950,000 documents
×

210

≈ 200M tokens
```

---

## What should **you** do?

Since you're using **FineWeb**, use the provided `token_count` field.

In your planning document, you can honestly write:

> **Target corpus size:** approximately **200M GPT-2 tokens**. The exact token count using the project's custom BPE tokenizer will be measured after tokenizer training.

That's both accurate and standard practice.

### Later in the pipeline

Your workflow will look like this:

```text
FineWeb
        │
        ├── GPT-2 token_count (used for planning)
        │
        ▼
Filtering
        ▼
Train your own 32k BPE tokenizer
        ▼
Retokenize corpus
        ▼
Measure the exact token count
        ▼
Pack sequences
        ▼
Train the model
```

So **M1.1 only needs an estimate**. The exact number naturally comes later, once your tokenizer exists.