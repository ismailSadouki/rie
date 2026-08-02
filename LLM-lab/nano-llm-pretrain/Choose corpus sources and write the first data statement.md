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


---

---



# from DOLMA


## 1. Think in terms of a pipeline ⭐⭐⭐⭐⭐

The biggest lesson is that filtering is **not one rule**.

It's a sequence of filters:

```text
Raw documents
      ↓
Language filter
      ↓
Encoding / parsing
      ↓
Boilerplate removal
      ↓
Quality filter
      ↓
Deduplication
      ↓
Safety / PII filtering
      ↓
Final dataset
```

For your project, design your pipeline similarly.

---

## 2. Categorize filters ⭐⭐⭐⭐⭐

Instead of asking

> "Should I use this regex?"

Ask

> "What category of problem does this filter solve?"

For example:

|Category|Why it exists|
|---|---|
|Language filtering|Remove non-target languages|
|Quality filtering|Remove junk pages|
|Deduplication|Avoid repeated content|
|Boilerplate removal|Remove menus, headers, footers|
|Safety filtering|Remove sensitive content|
|Document normalization|Standardize formatting|

These categories will remain useful even if your implementation differs.

---

## 3. Filtering is mostly heuristics ⭐⭐⭐⭐

One important lesson from Dolma is:

Most filters are **heuristics**, not machine learning.

Examples:

- document too short
    
- too many repeated words
    
- too many URLs
    
- too much HTML
    
- bad Unicode
    
- excessive punctuation
    
- repeated characters
    
- low language confidence
    

Don't feel that everything must be AI.

---

## 4. Filters are composable ⭐⭐⭐⭐⭐

Don't build one huge function.

Instead:

```python
document
    ↓
filter_1()

    ↓
filter_2()

    ↓
filter_3()

    ↓
filter_4()
```

Much easier to debug and improve.

---

## 5. Record why documents are removed ⭐⭐⭐⭐

This is something many beginners forget.

Instead of

```python
return False
```

record

```python
reason = "language"

reason = "duplicate"

reason = "too_short"

reason = "html_noise"
```

Later you can compute:

```
30% removed because duplicates

18% because language mismatch

12% because spam
```

That makes your dataset much more reproducible.

---

## 6. Every filter has a tradeoff ⭐⭐⭐⭐⭐

Don't think:

> More filtering = better.

Think:

```
More filtering
        ↓
Higher quality

BUT

Less diversity
```

Example:

If you remove every document with spelling mistakes,

you may accidentally remove authentic Algerian Darija posts.

---

## 7. Build for experimentation ⭐⭐⭐⭐

Instead of hardcoding:

```python
MIN_LENGTH = 200
```

make it configurable:

```python
config = {
    "min_length": 200,
    "max_url_ratio": 0.2,
    "min_language_score": 0.9,
}
```

Then you can compare different filtering settings.

---

# What not to copy

Don't assume Dolma's thresholds are right for your data.

For example:

```
Minimum document length

Language confidence threshold

Spam threshold

URL ratio
```

These were tuned for **English web data**, not for Algerian multilingual content (Darija, Arabic, French, Tamazight).

---

# What you should write down while reading §3

For each heuristic, make notes like:

|Heuristic|Why?|Keep?|Modify?|
|---|---|---|---|
|Remove duplicates|Prevent memorization|✅|Same|
|Remove boilerplate|Improve quality|✅|Same|
|Language filtering|Keep target languages|✅|Adapt to Arabic/French/Darija/Tamazight|
|Length filter|Remove junk|✅|Retune thresholds|
|HTML cleanup|Remove noise|✅|Same|
|Toxicity filter|Safety|?|Depends on project goals|
