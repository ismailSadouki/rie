
#### body_mass_idx


```
count    18581.000000
mean        27.436316
std         17.783410
min         16.300000
25%         24.000000
50%         25.900000
75%         27.900000
max        535.286443
Name: body_mass_idx, dtype: float64
```
### 🧠 تفسير علمي

- BMI الطبيعي عادة بين **15 و 45**
    
- 535 = **خطأ بيانات أو synthetic noise**
    
- هذا هو السبب في أن:
    
    - histogram skewed جدًا
        
    - skewness nan

✅ 6) BMI Categories (Medical Interpretation)
```python
bins = [0, 18.5, 25, 30, 100]
labels = ['Underweight', 'Normal', 'Overweight', 'Obese']
df_train['BMI_category'] = pd.cut(df_train['body_mass_idx'], bins=bins, labels=labels)

df_train['BMI_category'].value_counts()

```
### 📌 الشرح

نقسم BMI إلى فئات طبية:

- Underweight
    
- Normal
    
- Overweight
    
- Obese
    

هذا مهم جدًا لاحقًا في EDA وML.


في مشاريع السكري، غالبًا:

- BMI هو **Top 3 predictive features**
    
- Interaction بين BMI و age يكون قوي جدًا
    
- Tree models تستفيد من raw BMI أكثر من الفئات

**✅ Extreme outliers**
```
286 rows × 24 columns => larger then 45 or smaller then 10
```

head:
```
557      535.286443
15524    342.000000
7391     338.000000
12817    324.000000
9450     318.000000
2623     316.000000
12457    312.000000
8123     303.000000
18360    302.000000
3019     300.000000
Name: body_mass_idx, dtype: float64
```
![](https://i.imgur.com/805CJ00.png)


![](https://i.imgur.com/Q3YcV4L.png)


### years_old

The age variable shows a realistic distribution ranging from 15 to 94 years, with a mean age of approximately 50 years. The distribution appears approximately symmetric, with the majority of individuals between 42 and 59 years. No implausible age values were detected, indicating good data quality

potential outliers
![](https://i.imgur.com/O0D66Zg.png)
## Age is usually:

- Strong predictor for Type 2 diabetes
    
- Non-linear relationship (risk increases exponentially after 45)
    
- Interaction with BMI and exercise is critical


✅ Detect Age Heaping (Advanced Statistical Check)
```
years_old
52.0    630
53.0    606
57.0    605
51.0    597
48.0    586
49.0    574
50.0    572
46.0    570
47.0    564
56.0    561
55.0    554
54.0    551
45.0    546
58.0    536
43.0    531
41.0    520
44.0    500
42.0    472
59.0    450
60.0    424
Name: count, dtype: int64
```

العمر هنا **ليس uniform** بل:

- dataset biased toward middle-aged people
    
- وهذا قد يجعل النموذج أقل دقة للشباب
    

👉 يسمى: **Sampling Bias**



### `whr`



Feature Engineering
5️⃣ تقسيم WHR إلى فئات صحية
```python
# المعيار الطبي للذكور والإناث (تقريبًا)
# Male WHR > 0.9 = high risk, Female WHR > 0.85 = high risk

def whr_category(row):
    if row['sex'] == 'M':
        if row['whr'] <= 0.9:
            return 'Normal'
        else:
            return 'High'
    elif row['sex'] == 'F':
        if row['whr'] <= 0.85:
            return 'Normal'
        else:
            return 'High'
    else:
        return 'Unknown'

X_train['whr_category'] = X_train.apply(whr_category, axis=1)
X_train['whr_category'].value_counts()

# علاقة WHR بالسكري
`pd.crosstab(X_train['whr_category'], y_train, normalize='index')`

```
- هذا يعطي فئة WHR **Normal vs High**
    
- High WHR → خطر أعلى للسكري وأمراض القلب
# ⚠️ Insight مهم جدًا

- WHR أفضل من BMI أحيانًا لتحديد **الدهون الحشوية (visceral fat)**
    
- Machine learning models **تحب هذا المتغير قوي** كـ feature
    
- يمكن دمجه مع `body_mass_idx` لتحسين الدقة

# 🧠 Interpretation for Diabetes

- WHR هو **مؤشر للدهون الحشوية (visceral fat)**
    
- High WHR → correlated with higher diabetes risk
    
- Model tip: استخدم **WHR + sex** لإنشاء feature category (Normal / High)


## `bp_systolic`

4. **bp_systolic (Systolic Blood Pressure)**
    

- Mean ≈ 124, std initially very high due to extreme outliers (max = 2162)
    
- 479 rows > 180 mmHg → true hypertensive extremes
    
- After cleaning (or capping at 250 mmHg), distribution becomes more realistic
    
- Categorized into Normal / Elevated / Hypertension Stage 1 / Stage 2 / Hypertensive Crisis
    
- Higher systolic BP strongly associated with higher diabetes prevalence

```
count 18078.000000 mean 124.235331 std 82.168411 min 91.000000 25% 109.000000 50% 117.000000 75% 125.000000 max 2162.424650 Name: bp_systolic, dtype: float64
```
![](https://i.imgur.com/gbWF2oV.png)

feature eng
```python
def bp_category(bp):
    if bp < 120:
        return 'Normal'
    elif bp < 130:
        return 'Elevated'
    elif bp < 140:
        return 'Hypertension Stage 1'
    elif bp < 180:
        return 'Hypertension Stage 2'
    else:
        return 'Hypertensive Crisis'

X_train['bp_systolic_category'] = X_train['bp_systolic'].apply(bp_category)
X_train['bp_systolic_category'].value_counts()


`pd.crosstab(X_train['bp_systolic_category'], y_train, normalize='index')`
```


## `bp_diastolic`
375 row with value > 120
and 167 less then 60
![](https://i.imgur.com/cfkTekw.png)
![](https://i.imgur.com/0OmgraY.png)


```python
def bp_diastolic_category(bp):
    if bp < 80:
        return 'Normal'
    elif bp < 90:
        return 'Elevated / Stage 1'
    elif bp <= 120:
        return 'Stage 2'
    else:
        return 'Hypertensive Crisis'

X_train['bp_diastolic_category'] = X_train['bp_diastolic'].apply(bp_diastolic_category)


`pd.crosstab(X_train['bp_diastolic_category'], y_train, normalize='index')`
# يعطي insight مباشر لكيفية تأثير الضغط الانبساطي على الإصابة بالسكري
```

## **`pulse_rate`**




feature eng

28 row less then 50
291 greater then 120
![](https://i.imgur.com/D8BuTDG.png)
![](https://i.imgur.com/8XbIZ04.png)



```python
def pulse_category(p):
    if p < 60:
        return 'Low'
    elif p <= 100:
        return 'Normal'
    else:
        return 'High'

X_train['pulse_category'] = X_train['pulse_rate'].apply(pulse_category)
X_train['pulse_category'].value_counts()

`pd.crosstab(X_train['pulse_category'], y_train, normalize='index')`
```
Feature مهم للنموذج:

- High pulse rate → قد يرتبط بمخاطر صحية أعلى
    
- Normal → أغلب السكان

## total_chol
extreme outliers

```
count    17461.000000
mean       201.064717
std        136.244019
min        132.000000
25%        175.000000
50%        187.000000
75%        200.000000
max       4287.937377
Name: total_chol, dtype: float64
```
![](https://i.imgur.com/pKluIik.png)
![](https://i.imgur.com/jh5Y08p.png)


featur
```python
def chol_category(chol):
    if chol < 200:
        return 'Desirable'
    elif chol <= 239:
        return 'Borderline High'
    else:
        return 'High'

X_train['total_chol_category'] = X_train['total_chol'].apply(chol_category)
X_train['total_chol_category'].value_counts()

```
High cholesterol → مرتبط بزيادة مخاطر السكري وأمراض القلب


## **`hdl_chol`**
in the normal range 20-100
![](https://i.imgur.com/YLUm7FW.png)


```python
def hdl_category(hdl):
    if hdl < 40:
        return 'Low'
    elif hdl <= 60:
        return 'Normal'
    else:
        return 'High'

X_train['hdl_category'] = X_train['hdl_chol'].apply(hdl_category)
X_train['hdl_category'].value_counts()

```

## ldl_chol
a little of outliers althouth its in the normal range 50-200
![](https://i.imgur.com/I5ObvOF.png)
![](https://i.imgur.com/66ZVWMv.png)


```python
def ldl_category(ldl):
    if ldl < 100:
        return 'Optimal'
    elif ldl <= 129:
        return 'Near Optimal'
    elif ldl <= 159:
        return 'Borderline High'
    else:
        return 'High'

X_train['ldl_category'] = X_train['ldl_chol'].apply(ldl_category)
X_train['ldl_category'].value_counts()

```


## trig_level
the normal range 50-500
```
count    17112.000000
mean        53.942032
std         71.303731
min         34.000000
25%         40.768765
50%         44.342475
75%         48.670881
max        799.870859
Name: trig_level, dtype: float64
```
hence lots of extreme outliers
![](https://i.imgur.com/SYrKQKT.png)
![](https://i.imgur.com/CZ0nrBI.png)


```python
def trig_category(trig):
    if trig < 150:
        return 'Normal'
    elif trig <= 199:
        return 'Borderline High'
    elif trig <= 499:
        return 'High'
    else:
        return 'Very High'

X_train['trig_category'] = X_train['trig_level'].apply(trig_category)
X_train['trig_category'].value_counts()

```









- بعد هذه التحليلات المتقدمة، يمكننا عمل **Feature Importance** باستخدام نموذج مثل Random Forest أو Gradient Boosting لنعرف أي المتغيرات أهم في توقع السكري.


## **Interaction Plots & Partial Dependence**

- لمعرفة العلاقة بين feature محددة و target بعد التعامل مع كل المتغيرات الأخرى.
    
- يمكن استخدام **Partial Dependence Plots (PDP)** أو **SHAP values** لتفسير تأثير كل feature على القرار.
    
```

import shap  explainer = shap.TreeExplainer(rf) shap_values = explainer.shap_values(X_train) shap.summary_plot(shap_values[1], X_train)

```




## Partial Dependence Plots (PDP)

بعد بناء نموذج مثل **Random Forest أو Gradient Boosting**، يمكننا رسم **Partial Dependence** لتقدير تأثير كل variable على الهدف:

`from sklearn.ensemble import RandomForestClassifier from sklearn.inspection import plot_partial_dependence  rf = RandomForestClassifier(n_estimators=100, random_state=42) rf.fit(X_train[numeric_cols], y_train)  plot_partial_dependence(rf, X_train[numeric_cols], features=[0,6,8], grid_resolution=50) plt.show()`

**هدف:** رؤية التأثير الحقيقي لكل variable على احتمال السكري، حتى مع التفاعلات والغير خطية.


---

# Realtionship to target


## Categorical Variables (Proportion Tables + Chi2)

- **أقوى تأثير**:
    
    - `family_diabetes` → نسبة المصابين بالسكري مع وجود التاريخ العائلي 86.9%!
        
    - `outlier` → نسبة عالية للسكري، يدل أن وجود outlier يشير لمشكلة صحية.
        
    - `has_hypertension` → 65.4% من المصابين بالسكري لديهم ارتفاع ضغط، مهم لكن أقل من التاريخ العائلي.
        
    - `edu_status` → مستوى تعليمي منخفض (Level_0) مرتبط بنسبة عالية للسكري 67.8%.
        
- **ضعيف/غير مهم إحصائيًا** (Chi2 p-value > 0.05):
    
    - `sex`, `ethnic_group`, `income_class`, `work_status`, `tobacco_use` → لا يوجد اختلاف كبير بين الفئات بالنسبة للسكري.
        

✅ **الاستنتاج:**

- أهم categorical predictors للسكري: `family_diabetes`, `outlier`, `has_hypertension`, `edu_status`.
    
- الجنس، العرق، الدخل، العمل، التدخين تقريبًا لا تأثير كبير.
## Visual Insights (من Boxplots & Countplots)

- المتغيرات مثل `bp_systolic`, `body_mass_idx`, `whr`, `total_chol` تحتوي على **outliers كبيرة** → يمكن التفكير في **winsorization أو clipping** قبل النمذجة.
    
- الفروق في numeric variables مثل `years_old`, `body_mass_idx` بين المصابين وغير المصابين واضحة جزئيًا، لكنها تحتاج normalization أو transformation عند بناء النموذج.
### 🔹 Recommendation for Advanced Analysis

1. **Feature Engineering:**
    
    - استخدم `family_diabetes` كـ strong predictor.
        
    - يمكن دمج المتغيرات المرتبطة بالصحة مثل `bp_systolic`, `bp_diastolic`, `body_mass_idx`, `whr` في composite score.
        
2. **Handle Outliers:**
    
    - `bp_systolic > 180` و `body_mass_idx > 50` → قد تؤثر على النموذج، يمكن clipping أو robust scaler.







## أهم الاستنتاجات من **Odds Ratio**

- **family_diabetes** → الأعلى تأثيرًا على السكري (OR ≈ 6.64). الأشخاص لديهم تاريخ عائلي للسكري لديهم فرصة أعلى بكثير للإصابة.
    
- **has_cardiovascular** و **has_hypertension** → OR أكبر من 1.6، مما يشير إلى أن أمراض القلب أو ارتفاع ضغط الدم تزيد من احتمالية الإصابة بالسكري.
    
- **edu_status** → المستويات الأدنى (Level_0) لها OR أعلى (2.11)، ما يشير إلى أن التعليم الأقل مرتبط بارتفاع احتمالية السكري.
    
- **income_class** → الفئة الأدنى (L) لديها OR أعلى (1.80) مقارنة بالفئة العالية (H)، تشير إلى عامل اقتصادي.
    
- المتغيرات الأخرى (sex, ethnic_group, tobacco_use, work_status) → OR قريبة من 1.6 تقريبًا، أي تأثير متوسط.
    

**ملاحظات:**

- أي variable OR > 1 → تزيد فرصة السكري عند هذه الفئة مقارنة بالفئة المرجعية.
    
- أي variable OR ≈ 1 → تأثير ضعيف أو متوسط.



## Advanced Insights

- **التأثيرات القوية:** family_diabetes, has_cardiovascular, has_hypertension, edu_status, income_class
    
- **تأثير متوسط:** work_status, ethnic_group
    
- **تأثير ضعيف:** sex, tobacco_use
    
- يمكن أيضًا دمج هذه النتائج مع **Logistic Regression Coefficients** لمعرفة **الاتجاه (positive/negative)** لكل numeric variable مقابل السكري.
    
- لاحقًا يمكن دراسة **التفاعلات (Interactions)** بين:
    
    - family_diabetes × BMI
        
    - has_hypertension × age
        
    - edu_status × income_class
        

وهذا يعطيك صورة أكثر **تقدماً واستنتاجية** من مجرد نسبة بسيطة أو Boxplots.








**Feature Engineering from above (VERY HIGH SCORE BOOST)**

## ✅ A) Create Risk Index Features

`X_train['chol_ratio'] = X_train['ldl_chol'] / X_train['hdl_chol'] X_test['chol_ratio'] = X_test['ldl_chol'] / X_test['hdl_chol']`
Medical papers use this → **huge predictive power**.
## ✅ B) Obesity Indicator

`X_train['obese'] = (X_train['body_mass_idx'] > 30).astype(int)`
## ✅ C) Lifestyle Score

`X_train['lifestyle_score'] = X_train['weekly_exercise_min'] + X_train['nutrition_index'] - X_train['daily_screen_hrs']`

🔥 Professors LOVE this innovation.

Some variables are correlated with each other:

- LDL, HDL, triglycerides
    
- BMI, WHR
    

👉 Use:

- PCA
    
- L1 regularization (Lasso Logistic Regression)
# ⭐ 8) Very High Score Trick (Interaction Features)

`X_train['age_bmi'] = X_train['years_old'] * X_train['body_mass_idx']`

Medical logic: age × obesity risk.











<mark>To try</mark>
# round int columns that have a max value with 3 digits
- - Class imbalance may require: class weights, SMOTE, or threshold optimization
















<mark>try latter</mark>


Ah! You’re asking about **“CatBoost 2‑ways combos”**, which is a **feature engineering technique** often used in Kaggle competitions, especially in structured/tabular data problems like your diabetes challenge. Let me explain clearly.

---

## 📌 What are 2‑ways combos?

**2‑ways combos** = **pairwise feature combinations**.

- Take **two categorical features** (or numeric binned/categorical features).
    
- Combine them into **a new categorical feature** representing their interaction.
    

Example:

|age_group|bmi_category|combined_feature|
|---|---|---|
|young|normal|young_normal|
|adult|overweight|adult_overweight|
|senior|obese|senior_obese|

This lets your model **capture interactions** that single features alone can’t.

---

### 🔹 Why it works for CatBoost

- CatBoost handles categorical features **directly** without one-hot encoding.
    
- Pairing features lets CatBoost learn **interaction effects** automatically.
    
- Adding 2‑ways combos is cheap computationally and often improves performance in **stacking or standalone models**.
    

---

## 🔹 How to create 2‑ways combos

### **Step 1: Identify categorical features**

```python
cat_cols = ['age_group', 'bmi_category', 'lifestyle_level', 'bp_category']
```

### **Step 2: Make all pairs**

```python
from itertools import combinations

pairs = list(combinations(cat_cols, 2))
```

### **Step 3: Combine each pair**

```python
for col1, col2 in pairs:
    new_col = f"{col1}_{col2}"
    df[new_col] = df[col1].astype(str) + "_" + df[col2].astype(str)
```

Now `df` has **all 2-way combos** as new categorical features.

---

### 🔹 Optional: Encode if needed

For **CatBoost**, you can just pass them as `cat_features`, no encoding required:

```python
from catboost import CatBoostClassifier

cat_features = [col for col in df.columns if df[col].dtype == 'object']

model = CatBoostClassifier(iterations=1000, learning_rate=0.05, depth=6)
model.fit(df, y, cat_features=cat_features)
```

---

### 🔹 Tips

1. Don’t create **all possible combos** if you have hundreds of categories → can explode the feature space.
    
2. You can also create combos of **numeric features that are binned**. Example:
    

```python
df['age_bin'] = pd.qcut(df['age'], 5, labels=False)
df['bmi_bin'] = pd.qcut(df['bmi'], 4, labels=False)

df['age_bmi_combo'] = df['age_bin'].astype(str) + "_" + df['bmi_bin'].astype(str)
```

3. **Select only top combos** by correlation with target or feature importance.
    

---

💡 **Key Idea:**  
2‑ways combos = “let CatBoost learn interactions explicitly that it might otherwise take longer to discover automatically.”

---

If you want, I can make a **ready-to-use Python function** that automatically creates **all 2‑ways combos for categorical columns**, optionally including binned numeric features, **ready for CatBoost**.

Do you want me to do that?



# Potential Improvements
To further improve accuracy beyond this baseline, the following approaches could be effective:

- **Interaction Features (High-Cardinality & Target Encoding):** Creating pairwise (bigrams) or higher-order combinations of discrete features is a powerful technique. This process intentionally creates **high-cardinality** features representing specific subgroups (e.g., "AgeGroup_BMI_Gender"). Applying **Target Encoding** to these specific combinations allows the model to capture fine-grained risk signals that single features cannot represent.















# <mark>Conclusion</mark>
he only robust path is an ensemble of diverse, strong models basically accepting the mess, but refusing to overfit it. That means minimal feature engineering and leaving the original dataset out of it.


So why are our models so bad if diabetes can generally be predicted well? Part of the answer was given in the post with which I started above: a feature called HbA1c (glycated hemoglobin) has been removed in the dataset we have, even though it is present in original data.



the data looks wierd so I didn't perform any aggressive Feature Engineering, this dataset is very strange.




Why encode all of the column? some columns like age has wide range of values, I mean they are totally 'numerical'. Encoding those which are medium-cardinality (nunique \in [3,9] )may be more plausible

- You make a very valid point. In real-world datasets, keeping continuous variables (like Age) as numerical is usually the standard approach.However, in past Playground Series competitions involving synthetic data, I've often seen that treating numerical columns as categorical features (or applying Target Encoding to them) leads to better scores. My hypothesis is that since the data is generated by Deep Learning models, the numerical features might behave more like specific "modes" rather than smooth distributions.Therefore, adding these encoded features (while keeping the original numerical columns) sometimes helps GBDTs capture these synthetic artifacts more effectively. I am still verifying if this strategy works for this specific competition, but finding the right balance (like your suggestion of nunique in [3,9]) is definitely worth exploring!
