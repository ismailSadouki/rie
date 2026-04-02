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
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260318012635.png]]
---

**Explanation**
![[Pasted image 20260318012314.png]]
![[Pasted image 20260318012424.png]]
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260318012454.png]]
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260318012517.png]]<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260318012606.png]]
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260318012656.png]]
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260318012803.png]]
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260318012847.png]]
![[Pasted image 20260318012828.png]]


---

### Visualization of CV-Stacking

<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260318012947.png]]


### Number of Folds K

<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260318013054.png]]

---
**EXP**
![](https://i.imgur.com/embGszH.png)
![](https://i.imgur.com/kdKb7xK.png)
![](https://i.imgur.com/BZz92qa.png)

---

# Choosing Base Learners and Meta-Learner
**Diversity of Base Learners**

![](https://i.imgur.com/gjvh4XI.png)

---

**EXP**
![](https://i.imgur.com/m3ep8jV.png)
![](https://i.imgur.com/YPQ1skW.png)
![](https://i.imgur.com/YlqO29l.png)
![](https://i.imgur.com/YuUCT1p.png)
![](https://i.imgur.com/0NLbyjG.png)
![](https://i.imgur.com/9oILLNW.png)

---

![](https://i.imgur.com/fNqo0oY.png)

### Meta-Learner Selection
![](https://i.imgur.com/Yc3zgDG.png)
![](https://i.imgur.com/8UOsecj.png)

---

**Exp**
![](https://i.imgur.com/oQ5GrMX.png)
![](https://i.imgur.com/PJPLhvH.png)
![](https://i.imgur.com/RgvMr12.png)
![](https://i.imgur.com/wvx30Lx.png)
![](https://i.imgur.com/VDxP4XF.png)
![](https://i.imgur.com/bpXyS8y.png)

---

### Including Original Features
![](https://i.imgur.com/d47vy7M.png)

---

**EXP**
![](https://i.imgur.com/qhvAAwW.png)
![](https://i.imgur.com/u9YO9MB.png)
![](https://i.imgur.com/X91VZCK.png)
![](https://i.imgur.com/XnCNjrm.png)
![](https://i.imgur.com/YurT6Wm.png)

---

# Theoretical Perspective

### Why Does Stacking Work?
![](https://i.imgur.com/z6FjodT.png)

---
**EXP**
![](https://i.imgur.com/Qt6PrA8.png)
![](https://i.imgur.com/G4fbTGH.png)
![](https://i.imgur.com/1y07kFA.png)
![](https://i.imgur.com/6BXODps.png)
![](https://i.imgur.com/OMxVFXy.png)
![](https://i.imgur.com/pvNU8On.png)
![](https://i.imgur.com/kEkvni3.png)
![](https://i.imgur.com/lWafcJo.png)

---

### Expected MSE of Ensemble
![](https://i.imgur.com/HBE1pbb.png)

---

**EXP**
![](https://i.imgur.com/gu6W2Vj.png)
![](https://i.imgur.com/8o1CTB3.png)
![](https://i.imgur.com/dDvN5ao.png)
![](https://i.imgur.com/gKGUyS7.png)
![](https://i.imgur.com/GFDv3jA.png)
![](https://i.imgur.com/KZg4H25.png)
![](https://i.imgur.com/DmMoa5P.png)
![](https://i.imgur.com/8EzeMt5.png)
![](https://i.imgur.com/xAiXbdk.png)

**Ensembles work not because of many models, but because of **uncorrelated errors****

---

### Universal Approximation
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260319001557.png]]
![[Pasted image 20260319001550.png]]


# Practical Consideration



**Computational Cost**
![[Pasted image 20260319001732.png]]


**Hyperparameter Tuning**
![[Pasted image 20260319002006.png]]

---

**EXP**
![[Pasted image 20260319002048.png]]
![[Pasted image 20260319002102.png]]
![[Pasted image 20260319002125.png]]
![[Pasted image 20260319002202.png]]
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260319002248.png]]
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260319002330.png]]
<!--⚠️Imgur upload failed, check dev console-->
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260319002352.png]]
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260319002405.png]]
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260319002421.png]]


---

### When Does Stacking Help?
![[Pasted image 20260319003801.png]]

**Overfitting in Stacking**
![[Pasted image 20260319003901.png]]


# Advanced Stacking Variants

### Multi-Level Stacking
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260319004017.png]]
**Stacking with Feature Selection**
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260319004048.png]]
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260319004123.png]]

---


---

# 🚀 Advanced Insight (Kaggle-level)
![](https://i.imgur.com/gtfxt7R.png)
![](https://i.imgur.com/xpLI5a8.png)

**🧩 How to Create Diversity**
![](https://i.imgur.com/TL3Cq8M.png)
![](https://i.imgur.com/IatoXuE.png)
![](https://i.imgur.com/TWmit2U.png)
**meta models**
![](https://i.imgur.com/viOBxqM.png)
![](https://i.imgur.com/vr81cRa.png)
![](https://i.imgur.com/DdhV4KA.png)
![](https://i.imgur.com/PpcVASM.png)
![](https://i.imgur.com/0i8wPzT.png)
try ![](https://i.imgur.com/DlfG4ef.png)
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260319001917.png]]<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260319002636.png]]
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20260319002652.png]]For similar models → diversity matters MORE than optimality












<mark>This looks like a very important method, but i didnt understand it, try it latter</mark>
Absolutely! Here’s a **clear, concise summary** of “joint tuning everything together” in stacking that you can save and revisit later:

---

# **Joint Tuning in Stacking — Summary**

### 1️⃣ Normal / Independent Tuning

- Tune each **base model individually** for its own best performance.
    
- Then train the meta-model on base predictions.
    
- Problem: Base models may become **too similar** or **not complementary**, so the ensemble is **not optimal**.
    

---

### 2️⃣ Joint Tuning (Advanced)

- Treat **all hyperparameters** of all base models **and the meta-model together** as one big search space.
    
- Optimize for **final ensemble performance**, not individual models.
    
- Steps:
    
    1. Sample a combination of hyperparameters for **all base models + meta-model**.
        
    2. Train base models → get out-of-fold (OOF) predictions.
        
    3. Train meta-model on OOF predictions.
        
    4. Evaluate **ensemble performance** (objective).
        
    5. Optimizer (e.g., Optuna) updates hyperparameters → repeat.
        

---

### 3️⃣ Why it works

- Individual models may **sacrifice some solo performance** to become more **complementary** for the ensemble.
    
- Optimizes the **team** of models, not just solo stars.
    
- Reduces correlation between base models → better variance reduction.
    

---

### 4️⃣ Analogy

- **Independent tuning:** Each musician practices solo → sounds perfect individually.
    
- **Joint tuning:** Band practices together → some adjust for the team → band sounds amazing together.
    

---

### 5️⃣ Trade-offs

- ✅ Can improve stacking performance significantly.
    
- ❌ Very expensive (all models retrained per trial).
    
- ❌ Only feasible for small number of base models or fast models.
    

---

### 6️⃣ Practical Advice

- Start with **independent tuning** of diverse base models + simple meta-model.
    
- Use **joint tuning** only if you want **maximal ensemble performance** and can afford the computational cost.
    
- Meta-model usually stays simple (linear/logistic/ridge) to avoid overfitting.
    

---

💡 **Takeaway:**

> Joint tuning is about finding **the best combination of hyperparameters for all models as a team**, not just making each model individually perfect.

---

If you want, I can also make a **tiny diagram showing base models → OOF → meta-model → joint tuning**, which usually makes this concept **click instantly**.

Do you want me to do that?