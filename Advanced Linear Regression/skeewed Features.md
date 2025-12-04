# Skeweenes
Here is **the advanced, expert-level strategy** for **fixing skewed data _before_ handling outliers**, PLUS the best resources, explanations, and practical steps used by top Kaggle competitors and researchers.

---

# ✅ **Why skew correction should happen BEFORE outlier detection**

This is a key mistake beginners make:  
📌 **outliers are often created by skewed distributions**.

Example:

- Household income
    
- House prices
    
- Area (square feet)
    
- Lot size
    

These features are naturally **right-skewed**.  
If you run outlier detection first, _almost all high values will appear as outliers_ (false outliers).

👉 **After transformation (log/box-cox/yeo-johnson), many of these “outliers” disappear.**

So the correct pipeline is:

---

# 🔥 **Correct Order (used by Kaggle grandmasters & research)**

### **Step 1 — Identify skew**

Use:

- `scipy.stats.skew`
    
- histograms
    
- KDE plots
    
- boxplots
    
- Q-Q plots
    
- Skew threshold: **|skew| > 0.75**
    

### **Step 2 — Apply transformation**

Choose among:

- **Log1p**
    
- **Box–Cox**
    
- **Yeo–Johnson**
    
- **Quantile Transformer (Normal/Uniform)**
    
- **Power Transformer**
    

These remove heavy tails & compress extreme values.

### **Step 3 — Recompute skew**

Most distributions look normal now.

### **Step 4 — Detect true outliers**

Now use:

- IQR
    
- MAD
    
- IsolationForest
    
- LOF
    
- ECOD (PyOD)
    
- Robust covariance estimator
    

👉 Results are MUCH cleaner and more accurate.

---

# 🔥 **Advanced Resources to Learn Skew Correction**

These are the best (high-level) references:

---

## 📘 **1. "Transformations for Skewed Distributions" — Box & Cox (1964)**

The original research paper introducing Box–Cox transforms.

Topics:

- reducing non-normality
    
- variance stabilization
    
- improving linear regression
    

Still fundamental.

---

## 📘 **2. “Modern Applied Statistics with S — Venables & Ripley”**

A classic that covers:

- Box–Cox
    
- log transforms
    
- when each is appropriate
    
- robustness in regression
    

---

## 📘 **3. “An Introduction to Statistical Learning” (ISLR) – Chapter on Feature Engineering**

Explains:

- non-linearity
    
- need for transformations
    
- skew → regression instability
    
- multiplicative vs additive effects
    

---

## 📘 **4. Aggarwal — “Data Mining: The Textbook”**

Includes:

- transforms for skew
    
- effect on clustering
    
- effect on high dimensions
    
- monotonic transformations
    

---

# 🔥 **Advanced Practical Guides (Kaggle style)**

### **1. Kaggle "House Prices" top solutions (Giba, Serigne, 1st place notebooks)**

They used:

- log1p for almost all right-skewed features
    
- box-cox for others
    
- quantile transform for extreme variables
    
- huge improvement in RMSE
    

These notebooks are the BEST real-world examples.

---

### **2. Scikit-learn documentation (PowerTransformer + QuantileTransformer)**

Explains exactly:

- when to use Yeo-Johnson
    
- when Box–Cox fails
    
- when quantile transform is too strong
    

---

# 🔥 Now the important part:

# 🎯 **Which transformation to use & WHY**

### This is the rule used by experts:

---

# **1. Log1p transform (simple, effective)**

Use when:

- values are **strictly positive**
    
- right-skewed
    
- heavy tail
    
- outliers are multiplicative (prices, incomes)
    

Excellent for:

- GrLivArea
    
- LotArea
    
- 1stFlrSF
    
- SalePrice
    

---

# **2. Box–Cox transform**

Use when:

- strictly positive
    
- skew highly positive
    
- you want maximum likelihood optimal transform
    

Stronger than log.

---

# **3. Yeo–Johnson transform**

Use when:

- data contains **zero or negative values**
    
- you want a flexible version of Box–Cox
    
- good for mixed distributions
    

Most robust general-purpose transformation.

---

# **4. Quantile Transformer**

Transforms data to:

- **Normal distribution**  
    OR
    
- **Uniform distribution**
    

Use when:

- extremely skewed
    
- many extreme outliers
    
- tree models don’t mind monotonic transform
    

⚠️ Strongest transformation  
⚠️ Destroys original scale (but monotonic = safe for ML)

---

# **5. PowerTransformer (scikit-learn, Yeo–Johnson or Box–Cox)**

Easy implementation, handles:

- scaling
    
- variance stabilization
    
- Gaussianization
    

Recommended when building pipelines.

---

# 🔥 **How to detect skew automatically**

```python
from scipy.stats import skew

skew_vals = X_train.select_dtypes(include=['float64','int64']).apply(lambda x: skew(x.dropna()))
skewed_cols = skew_vals[skew_vals > 0.75].index
skewed_cols
```

---

# 🔥 **How to apply the transformation properly**

(Without modifying the original dataframe)

```python
from sklearn.preprocessing import PowerTransformer

X_train_trans = X_train.copy()
X_test_trans = X_test.copy()

pt = PowerTransformer(method='yeo-johnson', standardize=False)

cols = skewed_cols.to_list()

X_train_trans[cols] = pt.fit_transform(X_train[cols])
X_test_trans[cols] = pt.transform(X_test[cols])
```

---

# 🔥 If you want, I can build you:

### ✔️ a full notebook

### ✔️ with:

- automatic skew detection
    
- automatic choice of the best transform
    
- advanced outlier detection (PyOD)
    
- robust scaling
    
- ML modeling
    
- full pipeline
    

Just tell me **"yes make the full notebook"**.



