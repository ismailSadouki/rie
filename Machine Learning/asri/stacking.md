# Why combine multiple models?
- Different learners have different inductive biases
---

**Inductive bias** = the **assumptions a model makes** to learn patterns from data.
Each model has its own way of thinking:

- What patterns are “simple”
    
- What relationships are “likely”
    
- What structure the data has
    

👉 That “way of thinking” = **inductive bias**
hence: Different models make **different mistakes** because of their different inductive biases.

---

- They make errors on different parts of X

---

### 🔹 Logistic Regression

- Good in → linear regions
    
- Bad in → nonlinear regions
### 🔹 Decision Tree / LightGBM

- Good in → nonlinear regions
    
- Bad in → very noisy regions (can overfit)
    

### 🔹 kNN

- Good in → dense local clusters
    
- Bad in → sparse areas
## 🔥 Key Idea

> Errors are **not overlapping**

This is the gold of stacking.

If all models fail on the same samples:  
❌ Stacking useless

If they fail on different samples:  
✅ Stacking powerful

## 📊 More Formal View

Let’s define error:

```
error_i(x) = model_i(x) ≠ y

```

Stacking works when:
```
error_1(x), error_2(x), ... are weakly correlated

```

👉 i.e., models disagree in different places

---

Linear models: fast, interpretable, but limited flexibility
Trees: handle non-linearity, but high variance
Neural networks: flexible, but require lots of data
Idea: Leverage strengths of each, compensate for weaknesses

## Comparison with other ensemble methods
![](https://i.imgur.com/dTUiMBf.png)


# Stacking Framework
![](https://i.imgur.com/LIPUxkL.png)

---

**Explanation:**

![](https://i.imgur.com/Zc7UKhl.png)
![](https://i.imgur.com/5y09SPZ.png)
![](https://i.imgur.com/h749toN.png)
![](https://i.imgur.com/xvlsteS.png)
![](https://i.imgur.com/GeNCaxV.png)
![](https://i.imgur.com/klfQuJQ.png)
![](https://i.imgur.com/NrifUuC.png)
![](https://i.imgur.com/Yi07OcE.png)

---

![](https://i.imgur.com/n0Ag9uZ.png)

---

**Expl**
![](https://i.imgur.com/2D04WUD.png)
![](https://i.imgur.com/AcYJ3bf.png)
![](https://i.imgur.com/5PlKkto.png)
![](https://i.imgur.com/7qFVWrJ.png)
![](https://i.imgur.com/rJha8Mn.png)
![](https://i.imgur.com/BWvOft0.png)

---
![](https://i.imgur.com/HfWYfaf.png)
![](https://i.imgur.com/eyQe1La.png)

---
**Exp**
![](https://i.imgur.com/FqR98Ej.png)

The meta-model learns a function that best maps **base predictions → true labels**
## 🎯 Intuition

You are solving:

> “Given what all models predicted, what is the best way to combine them?”

![](https://i.imgur.com/nnFItFH.png)
![](https://i.imgur.com/MDmwqd2.png)
![](https://i.imgur.com/vCnhKOr.png)
![](https://i.imgur.com/ed5w9T6.png)

# ⚡ 4. Why Linear Meta-Learner Works So Well

Even though it's simple, it’s powerful because:

### ✅ Base models already did the hard work

- Extracted nonlinear patterns
    
- Captured interactions
    

### ✅ Meta-model just combines them

## 🧠 Important Insight

Stacking =

> Nonlinear learning (base) + linear combination (meta)

![](https://i.imgur.com/YmMuT6z.png)
## 🔍 Why?

Prevents:

- Overfitting
    
- Over-reliance on one model
![](https://i.imgur.com/J0fpGW2.png)
![](https://i.imgur.com/ZSbyDio.png)


---

**Final Prediction**
![](https://i.imgur.com/avUrBUF.png)



# The Data Leakage Problem

![](https://i.imgur.com/Jct3ygJ.png)

---

**Exp**
![](https://i.imgur.com/oRfbY6V.png)
![](https://i.imgur.com/VixPlGi.png)
![](https://i.imgur.com/ViwUNNZ.png)

# 🧠 Key Principle (VERY IMPORTANT)

> In stacking, **every prediction used for training must be out-of-sample**


# 🎯 Final takeaway

Your statement can be summarized as:

> Stacking fails when the meta-model learns from **overfitted base predictions instead of generalization behavior**


---
![](https://i.imgur.com/gdgjk2m.png)
**Solution: Cross-Validation**
![](https://i.imgur.com/RNXwTkv.png)

---

**EXP**
![](https://i.imgur.com/trng9mf.png)
![](https://i.imgur.com/pQqFwxy.png)
![](https://i.imgur.com/Wi1rDz5.png)
![](https://i.imgur.com/xtqe0cz.png)
![](https://i.imgur.com/lYz6QOX.png)
![](https://i.imgur.com/Dm22MR8.png)
![](https://i.imgur.com/hqD3vKV.png)
![](https://i.imgur.com/q62DXSc.png)
![](https://i.imgur.com/0gVXed4.png)



**Other explanation**

![](https://i.imgur.com/E9LC1sd.png)
![](https://i.imgur.com/1WQXRkB.png)
![[Pasted image 20260318010540.png]]

**Other explanatoin with example**

# 🧩 Example: Binary Classification

Suppose we have **6 training samples** and 2 base learners:

|Sample|X|y|
|---|---|---|
|1|0.1|0|
|2|0.4|0|
|3|0.35|1|
|4|0.8|1|
|5|0.65|1|
|6|0.9|0|

- Base learners: **Logistic Regression (LR)**, **Decision Tree (DT)**
    
- Meta-learner: **Linear Regression** (for simplicity)
    
- K = 3 folds
    

---

## Step 1: Split into 3 folds

- Fold1 = Samples [1,2]
    
- Fold2 = Samples [3,4]
    
- Fold3 = Samples [5,6]
    

---

## Step 2: Generate Out-of-Fold Predictions

### Fold 1: train on Fold2+Fold3 → predict Fold1

- Train LR and DT on samples [3,4,5,6]
    
- Predict samples 1 and 2
    

|Sample|LR pred|DT pred|
|---|---|---|
|1|0.2|0.0|
|2|0.3|0.0|

---

### Fold 2: train on Fold1+Fold3 → predict Fold2

- Train on [1,2,5,6] → predict [3,4]
    

|Sample|LR pred|DT pred|
|---|---|---|
|3|0.6|1.0|
|4|0.7|1.0|

---

### Fold 3: train on Fold1+Fold2 → predict Fold3

- Train on [1,2,3,4] → predict [5,6]
    

|Sample|LR pred|DT pred|
|---|---|---|
|5|0.8|1.0|
|6|0.4|0.0|

---

## Step 3: Build Dmeta

Now stack **base predictions** as features:

|Sample|LR pred|DT pred|y|
|---|---|---|---|
|1|0.2|0.0|0|
|2|0.3|0.0|0|
|3|0.6|1.0|1|
|4|0.7|1.0|1|
|5|0.8|1.0|1|
|6|0.4|0.0|0|

✅ Each sample’s prediction is **out-of-fold**, so meta-learner is honest.

---

## Step 4: Train Meta-Learner

- Meta input: columns [LR pred, DT pred]
    
- Target: y
    
- Meta-model learns **weights θ1, θ2** to combine LR and DT predictions
    

Example (hypothetical learned weights):

[  
\theta_{\text{LR}} = 0.6, \quad \theta_{\text{DT}} = 0.4  
]

---

## Step 5: Retrain Base Models on Full Dtrain

- Train LR and DT on all 6 samples
    
- Now ready to predict **new test samples**
    

---

## Step 6: Predict New Sample

Test sample: X_test = 0.5

1. Base predictions:
    

- LR → 0.55
    
- DT → 1.0
    

2. Meta-model combines:
    

[  
\hat{y} = 0.6 * 0.55 + 0.4 * 1.0 = 0.33 + 0.4 = 0.73  
]

- Final prediction → class 1 (because >0.5)
    

---

# ✅ Key Takeaways from Example

1. **Out-of-Fold Predictions** → prevent meta-learner from overfitting
    
2. **Meta-learner** → learns to weight base models
    
    - If LR is usually better → higher θ
        
    - If DT sometimes overshoots → lower θ
        
3. **Base models retrained on full data** → better final performance on test data
    

**Code example**

```python
# Step 0: Imports
import numpy as np
import pandas as pd
from sklearn.model_selection import KFold
from sklearn.linear_model import LogisticRegression, LinearRegression
from sklearn.tree import DecisionTreeClassifier

# For demonstration, tiny dataset
X = np.array([[0.1],[0.4],[0.35],[0.8],[0.65],[0.9]])
y = np.array([0, 0, 1, 1, 1, 0])

# Base learners
base_learners = [
    ('LR', LogisticRegression()),
    ('DT', DecisionTreeClassifier())
]

K = 3  # number of folds
kf = KFold(n_splits=K, shuffle=True, random_state=42)

# Step 1: Prepare OOF predictions container
oof_predictions = {name: np.zeros(len(y)) for name,_ in base_learners}

# Step 2: Generate Out-of-Fold Predictions
for fold_idx, (train_idx, val_idx) in enumerate(kf.split(X)):
    X_train, X_val = X[train_idx], X[val_idx]
    y_train, y_val = y[train_idx], y[val_idx]

    for name, model in base_learners:
        model.fit(X_train, y_train)
        preds = model.predict_proba(X_val)[:,1]  # probability for class 1
        oof_predictions[name][val_idx] = preds

# Step 3: Create Dmeta
Dmeta = pd.DataFrame(oof_predictions)
Dmeta['y'] = y
print("Meta dataset (OOF predictions):\n", Dmeta)

# Step 4: Train Meta-Learner
meta_X = Dmeta[['LR','DT']].values
meta_y = Dmeta['y'].values
meta_model = LinearRegression()
meta_model.fit(meta_X, meta_y)

# Step 5: Retrain Base Learners on full data
for name, model in base_learners:
    model.fit(X, y)

# Step 6: Predict on new data
X_test = np.array([[0.5],[0.7]])
base_test_preds = np.column_stack([
    model.predict_proba(X_test)[:,1] for _, model in base_learners
])

meta_preds = meta_model.predict(base_test_preds)
print("\nTest set predictions (meta-model):", meta_preds)
```

---


# Stacking Algorithm
![[Pasted image 20260318011448.png]]
![[Pasted image 20260318012028.png]]
---

**Exp**
![[Pasted image 20260318011529.png]]
![[Pasted image 20260318011633.png]]
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260318011755.png]]
![[Pasted image 20260318011818.png]]

![[Pasted image 20260318011857.png]]
![[Pasted image 20260318011908.png]]

---

# Full algorithm

![[Pasted image 20260318012050.png]]

---

**Explanation**
![[Pasted image 20260318012314.png]]


---

---


---

# 🚀 Advanced Insight (Kaggle-level)
![](https://i.imgur.com/gtfxt7R.png)
![](https://i.imgur.com/xpLI5a8.png)

