
Absolutely. Let's lock a **small, thesis-grade AlgerianMMLU** rather than accidentally turning this into a 2-week benchmark project.

# AlgerianMMLU — Build Plan

### Objective

Build a **validated multiple-choice benchmark for Algerian-context knowledge** that we can use in B3.3 to compare:

- Base
    
- SFT
    
- DPO β=0.10
    
- DPO β=0.30
    
- DPO β=0.50
    

The benchmark's role is **evaluation**, not a standalone research contribution.

---

## 0. Scope lock

**Target:** 180 questions.

**Format:** 4-choice MCQ.

Each item:

```json
{
  "id": "dzmmlu_001",
  "category": "...",
  "question": "...",
  "choices": ["A", "B", "C", "D"],
  "answer": "B",
  "source": "...",
  "source_type": "...",
  "difficulty": "...",
  "language": "darija"
}
```

We'll keep the benchmark **fixed once validated**.

No model gets to see the test questions during SFT/DPO training.

---

# 1. Categories

I propose **6 categories × 30 questions = 180**.

|Category|N|What it tests|
|---|--:|---|
|🇩🇿 Algerian History|30|major historical events, figures, periods|
|🗺️ Geography & Places|30|wilayas, geography, landmarks, regions|
|🏛️ Society, Civics & Institutions|30|institutions, administrative structure, society|
|🎭 Culture & Heritage|30|traditions, food, music, clothing, heritage|
|🗣️ Algerian Language / Darija|30|vocabulary, expressions, linguistic usage|
|🔬 Science, Education & General Knowledge in Algerian Context|30|science/education/general facts relevant to Algeria|

This gives us a mixture of:

**local knowledge + cultural knowledge + language knowledge + general factual knowledge.**

---

# 2. Important distinction: not everything must be “Darija”

The benchmark should test **knowledge**, not simply whether the model can understand Darija.

So I'd make the **question language predominantly Algerian Darija**, but allow:

- Arabic terminology
    
- French-derived Algerian terms
    
- proper nouns
    
- standard Arabic where naturally required
    

Example:

> شكون هي أكبر ولاية في الجزائر من حيث المساحة؟

rather than forcing unnatural Darija everywhere.

For the language category, however, Darija itself becomes the object being tested.

---

# 3. Difficulty distribution

Don't make 180 trivia questions.

Per category:

- **10 easy**
    
- **15 medium**
    
- **5 hard**
    

So:

```text
60 easy
90 medium
30 hard
= 180
```

This gives us a useful difficulty distribution without making the benchmark ridiculous.

---

# 4. Question quality rules

This is probably the **most important part**.

A question is rejected if:

### ❌ Ambiguous

Two answers could reasonably be defended.

### ❌ Time-sensitive

For example:

> "Who is currently the minister of X?"

unless we explicitly freeze the benchmark to a date.

### ❌ Politically/evaluatively controversial

We want factual questions, not political opinions.

### ❌ Obscure trivia

Something only a specialist would know isn't useful for evaluating a 1.5B LLM.

### ❌ Source-dependent without evidence

If we can't establish the answer, don't include it.

### ❌ Multiple correct choices

Exactly **one answer** must be defensible.

### ❌ Linguistically broken

Especially important for the Darija questions.

### ❌ Training leakage

Anything appearing in DziriDPO training data or generated from it gets excluded.

---

# 5. Source strategy

This is where I want to be more rigorous than simply asking an LLM:

> "Generate 180 Algerian questions."

Instead:

```text
Reliable source
      ↓
Factual statement
      ↓
Question construction
      ↓
Distractors
      ↓
Validation
      ↓
Final item
```

Sources can include things such as:

- official Algerian government sources
    
- official institutional websites
    
- UNESCO
    
- reputable historical/academic sources
    
- geographic/reference sources
    
- educational materials
    
- established linguistic resources
    
- carefully selected encyclopedic/reference material
    

For culture and Darija, some items will inevitably require more specialized sources.

Each question gets a provenance field.

---

# 6. LLM generation is allowed — but not trusted

This is important.

We can use an LLM to accelerate:

- converting facts → questions
    
- generating distractors
    
- translating/adapting wording into Darija
    
- proposing difficulty
    

But:

> **LLM-generated ≠ validated.**

The final benchmark needs human validation.

So we maintain two stages:

```text
candidate_items.jsonl
        ↓
human validation
        ↓
algerianmmlu_v1.jsonl
```

---

# 7. Validation protocol

Each candidate gets:

```text
✓ factual correctness
✓ exactly one correct answer
✓ question clarity
✓ distractor quality
✓ Algerian relevance
✓ Darija quality where applicable
✓ difficulty
✓ source/provenance
✓ leakage check
```

We don't need three human annotators for every question. That would blow up the project.

Instead:

### First pass

You validate the full candidate set.

### Second pass

Do a stricter review of questionable items.

### Third check

Run automated consistency checks.

For example:

```text
180 questions
→ 180 validated
→ maybe 15–30 rejected
→ replace rejected items
→ final 180
```

---

# 8. Distractor design

Bad:

> What is the capital of Algeria?

A. Paris  
B. London  
C. Algiers  
D. Madrid

That's basically a language-model sanity check.

Better distractors should be **plausible Algerian alternatives** when possible.

For example, geographic questions can use other Algerian cities/wilayas.

This prevents the benchmark from being artificially easy.

But we must avoid distractors that are technically also correct.

---

# 9. Difficulty validation

Difficulty should not be based solely on our intuition.

After we have the first benchmark version, we can inspect model performance.

For example:

```text
easy      ~80–95%
medium    ~50–80%
hard      ~20–50%
```

These are **diagnostic ranges**, not hard acceptance thresholds.

If a supposedly hard question is answered correctly by every model, it's probably not hard.

If an "easy" question is missed by everyone, investigate the question.

---

# 10. Benchmark splits

For B3.3, I would **not** create a conventional train/dev/test split.

This benchmark is an **evaluation-only benchmark**.

Instead:

```text
AlgerianMMLU v1
└── test
    └── 180 questions
```

That's cleaner.

The models never train on it.

---

# 11. Evaluation protocol

For every model:

```text
same 180 questions
same four choices
same prompt format
same decoding/evaluation procedure
```

Prefer **log-probability scoring of the four answer choices** if our evaluation setup supports it.

Otherwise use constrained generation:

```text
A / B / C / D
```

rather than allowing the model to produce arbitrary explanations.

This reduces generation-format noise.

---

# 12. Statistics

The headline metric:

### Accuracy

Accuracy=correct answers180\text{Accuracy} = \frac{\text{correct answers}}{180}

Then bootstrap the **180 individual items** to obtain a 95% CI.

Example eventual report:

```text
Model       Accuracy       95% CI
Base        61.1%          [54.4, 67.8]
SFT         63.9%          [57.2, 70.6]
DPO β=.10   62.8%          [56.1, 69.4]
DPO β=.30   60.6%          [53.9, 67.2]
DPO β=.50   58.9%          [52.2, 65.6]
```

Those numbers are **illustrative only**, obviously.

Then B3.3 can test paired differences because every model answers the **same items**.

---

# 13. Category-level analysis

We shouldn't only report the overall score.

We'll also calculate:

```text
                 History Geography Civics Culture Darija Science
Base
SFT
DPO .10
DPO .30
DPO .50
```

This could reveal something much more interesting.

For example, DPO might:

- improve Darija performance
    
- preserve history
    
- hurt science
    
- etc.
    

We don't know beforehand — **that's exactly why we're measuring it.**

---

# 14. Leakage audit

Before B3.3, we need to check:

```text
AlgerianMMLU
      ↕
SFT dataset
      ↕
DPO preference dataset
```

Potential overlap must be removed.

Especially because your DPO data contains Algerian cultural questions/conversations.

We don't want a reviewer saying:

> "The model already saw essentially the same question during training."

---

# 15. Files we should create

I'd structure it approximately like:

```text
data/
└── algerianmmlu/
    ├── candidates.jsonl
    ├── validated.jsonl
    ├── rejected.jsonl
    ├── sources.json
    └── README.md

eval/
└── algerianmmlu.py

reports/
└── algerianmmlu/
    ├── validation_report.md
    └── evaluation_results.json
```

Then B3.3's final report consumes those results.

---

# 16. Time budget

This is the part you're worried about.

I'd impose:

|Task|Target|
|---|--:|
|Specification|1–2 h|
|Source collection|3–5 h|
|Candidate generation|2–3 h|
|Validation|4–6 h|
|Cleanup/replacement|1–2 h|
|Evaluation harness|2–3 h|
|**Total**|**~13–21 h**|

So roughly **2–3 focused days**, not a week+.

And if we're approaching ~20 hours, **we freeze it and move to B3.3 evaluation**.

---

# Final locked pipeline

```text
                 ALGERIANMMLU
                      │
          ┌───────────┴───────────┐
          │                       │
    6 categories             180 items
          │                       │
          └───────────┬───────────┘
                      ↓
              Source + generate
                      ↓
              Human validation
                      ↓
               Leakage audit
                      ↓
             AlgerianMMLU v1
                      ↓
       ┌──────────────┼──────────────┐
       ↓              ↓              ↓
     Base            SFT          DPO .10/.30/.50
       │              │              │
       └──────────────┼──────────────┘
                      ↓
                180-item MCQ
                      ↓
              Accuracy + 95% CI
                      ↓
             Paired comparisons
                      ↓
              B3.3 triple axis
```

**I think this is the right scope for you.** The benchmark is substantial enough that you can defend the evaluation methodology, but deliberately **not substantial enough to hijack the thesis**.

Next, I'd lock the **exact 6 categories + 30-question blueprint for each category**, including what kinds of questions are allowed/rejected. Then we can start building the actual dataset.