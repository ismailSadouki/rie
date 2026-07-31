
Yes—but **only the first three**, and exactly at the depth suggested. Don't let M1.1 turn into a literature review.

Here's what I'd do.

|Priority|Resource|Do now?|Why|
|---|---|---|---|
|⭐⭐⭐|**Hugging Face Datasets streaming docs**|**Yes**|You're about to use `streaming=True`, so understanding iterable datasets and streaming limitations will directly influence your implementation.|
|⭐⭐⭐|**Dolma §3 (Filtering Overview)**|**Yes**|This will help you decide _what_ filters your pipeline should have (language, quality, deduplication, etc.), even if you implement much simpler versions.|
|⭐⭐|**Data Statements for NLP**|**Yes, skim only**|Just enough to make sure your `data_statement.md` covers the important sections. Don't spend more than ~15 minutes.|
|⭐|**Dolma code (`taggers/`)**|**No**|That's implementation detail. Wait until M1.2 when you're actually writing the ingestion/filtering pipeline.|

---

## My suggested schedule (≈1 hour)

### 1. HF Streaming Docs (20 min)

Your goal is to answer questions like:

- What is an `IterableDataset`?
    
- How do I stream FineWeb?
    
- Can I use `.map()`?
    
- Can I use `.filter()`?
    
- How do sharding and workers behave?
    

You don't need to memorize the API.

---

### 2. Dolma §3 (30 min)

Don't focus on their scale.

Instead, make notes like:

```text
Filtering categories

✓ URL filtering
✓ Language filtering
✓ Quality filtering
✓ Deduplication
✓ PII handling

Ignore for now:
- distributed infrastructure
- billions of documents
```

Your pipeline will be much smaller, but the categories transfer well.

---

### 3. Data Statements (15 min)

You're not writing an academic paper.

You're checking:

> "Did I forget anything important?"

Typical checklist:

- Why this dataset?
    
- Provenance
    
- License
    
- Language
    
- Risks
    
- Intended use
    
- Planned filtering
    

That's enough.

---

## Don't read Dolma's code yet

This is the biggest trap.

You'll end up looking at code like:

```python
LanguageFilter(...)
URLBlocklist(...)
PIIRedactor(...)
...
```

and spend an hour wondering how everything works.

At M1.1, that knowledge won't help you write a better data statement. Save it for M1.2, when you'll actually implement streaming and filtering.

---

## A concrete outcome for M1.1

By the time you finish, you should have:

- ✅ `notes/data-recipe.md`
    
- ✅ `reports/data_statement.md`
    
- ✅ `data/sources.yaml` (optional but nice)
    
- ✅ A clear list of planned filters
    
- ✅ A decision on your target corpus and approximate training budget
    

Then you're ready to start **M1.2: Streaming ingestion to Parquet shards**, where the Hugging Face streaming documentation becomes immediately useful.