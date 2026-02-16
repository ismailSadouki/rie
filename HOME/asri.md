




```r
# =======================================================
# Q2-Q14 Structured Workflow
# =======================================================

# ------------------------
# Q2: Libraries & Load Data
# ------------------------
library(tidyverse)
library(tidymodels)
library(doParallel)
library(vip)
library(pROC)
library(xgboost)
library(nnet)

df <- read_csv("/home/ismail/Documents/asri/homework/fin_health.csv")

# ------------------------
# Q2: Descriptive Analysis
# ------------------------
# Numeric summary
summary(df %>% select(where(is.numeric)))

# Categorical summary
df %>% select(where(is.character)) %>% map(unique)

# Numeric histograms
df %>% select(where(is.numeric)) %>%
  pivot_longer(cols = everything(), names_to="variable", values_to="value") %>%
  ggplot(aes(x=value)) + geom_histogram(bins=30, fill="steelblue", color="white") +
  facet_wrap(~variable, scales="free") + theme_minimal() + labs(title="Numeric Distributions")

# Boxplots numeric vs target
num_vars <- df %>% select(where(is.numeric)) %>% names()
df %>%
  pivot_longer(cols=all_of(num_vars), names_to="variable", values_to="value") %>%
  ggplot(aes(x=Target, y=value, fill=Target)) + geom_boxplot() +
  facet_wrap(~variable, scales="free") + theme_minimal() + labs(title="Numeric vs Target")

# Barplots categorical vs target
cat_vars <- df %>% select(where(is.character)) %>% names()
for(v in cat_vars){
  df %>%
    group_by_at(c(v,"Target")) %>% tally() %>%
    ggplot(aes_string(x=v, y="n", fill="Target")) +
    geom_col(position="dodge") + theme_minimal() +
    labs(title=paste("Counts of", v, "by Target")) +
    print()
}

# ------------------------
# Q3: Split Data (20% Test)
# ------------------------
set.seed(2026)
splits <- initial_split(df, prop=0.8, strata=Target)
train_data <- training(splits)
test_data  <- testing(splits)

# Drop ID
train_data <- train_data %>% select(-ID)
test_data  <- test_data %>% select(-ID)

# Clean categorical levels
clean_levels <- function(x){
  x %>% str_trim() %>% str_to_lower() %>% str_replace_all("[^a-z0-9 ]","") %>% str_replace_all("\\s+","_")
}
train_data <- train_data %>% mutate(across(where(is.character), clean_levels))
test_data <- test_data %>% mutate(across(where(is.character), clean_levels))

# ------------------------
# Q4: Recipe Proposal & Justification
# ------------------------
rec <- recipe(Target~., data=train_data) %>%
  step_string2factor(all_nominal_predictors()) %>%  # categorical as factors
  step_unknown(all_nominal_predictors(), new_level="missing") %>% # unseen levels
  step_impute_median(all_numeric_predictors()) %>% # fill missing numeric
  step_log(owner_age, offset=1) %>% # handle skew in owner_age
  step_YeoJohnson(business_expenses, personal_income, business_turnover, business_age_years) %>% # skew correction
  step_center(all_numeric_predictors()) %>% # standardization
  step_scale(all_numeric_predictors()) %>% # standardization
  step_dummy(all_nominal_predictors(), one_hot=TRUE) %>% # categorical to one-hot
  step_zv(all_predictors()) %>% # remove zero variance
  step_nzv(all_predictors()) # remove near-zero variance

# ------------------------
# Q5: Fit Different Models
# ------------------------
# Logistic Regression
log_mod <- multinom_reg(mode="classification") %>% set_engine("nnet")
log_wf <- workflow() %>% add_model(log_mod) %>% add_recipe(rec)
log_fit <- fit(log_wf, data=train_data)

# XGBoost (default hyperparameters for initial comparison)
train_data_xgb <- train_data %>% mutate(Target=as.character(Target))
rec_xgb <- rec %>% update_role(Target, new_role="outcome")  # same preprocessing
xgb_mod <- boost_tree(mode="classification", trees=500, tree_depth=6,
                      learn_rate=0.1, loss_reduction=0, sample_size=0.8,
                      mtry=ncol(train_data)-1) %>% set_engine("xgboost")
xgb_wf <- workflow() %>% add_model(xgb_mod) %>% add_recipe(rec_xgb)
xgb_fit <- fit(xgb_wf, data=train_data_xgb)

# ------------------------
# Q6: Justification of Hyperparameters
# ------------------------
# Logistic: no hyperparameters tuned, default multinomial regression.
# XGBoost:
# - trees=500: enough boosting rounds
# - tree_depth=6: medium depth for multiclass
# - learn_rate=0.1: conservative learning
# - sample_size=0.8: subsample for regularization
# - mtry=ncol-1: all predictors considered

# ------------------------
# Q7-Q8: Evaluate & ROC-AUC
# ------------------------
evaluate_model <- function(fit, test_data, model_name, target_col="Target") {
  original_levels <- levels(factor(test_data[[target_col]]))
  
  preds_prob <- predict(fit, test_data, type="prob")
  preds_class <- predict(fit, test_data, type="class") %>%
    rename(.pred_class=.pred_class) %>%
    mutate(.pred_class=factor(.pred_class, levels=original_levels))
  
  preds <- bind_cols(preds_prob, preds_class, test_data %>% select(all_of(target_col)))
  preds[[target_col]] <- factor(preds[[target_col]], levels=original_levels)
  
  cat("\n---", model_name, "---\n")
  print(metrics(preds, truth=!!sym(target_col), estimate=.pred_class))
  print(conf_mat(preds, truth=!!sym(target_col), estimate=.pred_class))
  
  prob_cols <- paste0(".pred_", original_levels)
  roc_auc_val <- roc_auc(preds, truth=!!sym(target_col), all_of(prob_cols))
  cat("Multiclass ROC-AUC (Hand-Till):\n")
  print(roc_auc_val)
  
  # ROC curve per class
  par(mfrow=c(1,length(original_levels)))
  for(lvl in original_levels){
    binary_target <- factor(ifelse(preds[[target_col]]==lvl, lvl, paste0("not_",lvl)))
    roc(response=binary_target, predictor=preds[[paste0(".pred_",lvl)]],
        levels=c(paste0("not_",lvl), lvl), direction="<",
        plot=TRUE, main=paste("ROC-",model_name,"-",lvl))
  }
  par(mfrow=c(1,1))
  
  return(list(preds=preds, roc_auc=roc_auc_val))
}

log_eval <- evaluate_model(log_fit, test_data, "Logistic Regression")
xgb_eval <- evaluate_model(xgb_fit, test_data %>% mutate(Target=as.character(Target)),
                           "XGBoost", target_col="Target")

# ------------------------
# Q9: Rank Models by AUC
# ------------------------
model_auc <- tibble(
  model=c("Logistic Regression","XGBoost"),
  auc=c(log_eval$roc_auc$.estimate, xgb_eval$roc_auc$.estimate)
) %>% arrange(desc(auc))

ggplot(model_auc, aes(x=reorder(model,auc), y=auc, fill=model)) +
  geom_col() +
  geom_text(aes(label=round(auc,3)), vjust=-0.5) +
  coord_flip() +
  theme_minimal() +
  labs(title="Model Comparison by ROC-AUC")

# ------------------------
# Q10-Q12: Tuning Best 3 Models
# (Logistic has no hyperparameters to tune, XGBoost grid tuning)
# ------------------------
# Define tuning grid
xgb_mod_tune <- boost_tree(
  mode="classification",
  trees=500,
  tree_depth=tune(),
  learn_rate=tune(),
  loss_reduction=tune(),
  sample_size=tune(),
  mtry=tune()
) %>% set_engine("xgboost")

xgb_wf_tune <- workflow() %>% add_model(xgb_mod_tune) %>% add_recipe(rec_xgb)

xgb_grid <- grid_latin_hypercube(
  tree_depth(range=c(3L,15L)),
  learn_rate(range=c(0.01,0.3)),
  loss_reduction(range=c(0,5)),
  sample_prop(range=c(0.5,1)),
  mtry(range=c(5L,ncol(train_data)-1L)),
  size=15
)

folds_xgb <- vfold_cv(train_data_xgb, v=5, strata=Target)

cl <- makePSOCKcluster(parallel::detectCores()-1)
registerDoParallel(cl)
xgb_tune <- tune_grid(
  xgb_wf_tune,
  resamples=folds_xgb,
  grid=xgb_grid,
  metrics=metric_set(roc_auc),
  control=control_grid(save_pred=TRUE)
)
stopCluster(cl)
registerDoSEQ()

best_xgb <- select_best(xgb_tune, metric="roc_auc")
xgb_final_wf <- finalize_workflow(xgb_wf_tune, best_xgb)
xgb_final_fit <- fit(xgb_final_wf, train_data_xgb)

# ------------------------
# Q13-Q14: Metrics & ROC on Testing Set
# ------------------------
xgb_eval_final <- evaluate_model(xgb_final_fit, test_data %>% mutate(Target=as.character(Target)),
                                 "XGBoost Final", target_col="Target")

```










### 🔹 3.3 التعامل مع عدم التوازن (Class Imbalance)

- إذا بعض الفئات قليلة جدًا:
    
    - استخدم **SMOTE** أو **Class Weights** في النموذج.
        
    - مثال: في LightGBM → `is_unbalance=True` أو `class_weight={0:1,1:1,2:2}`.



- sometimes try combining categories inside the categorical var to `others` value.


# ملاحظة عامة مهمة جدًا (Key Insight)

تقريبًا في **كل المتغيرات** نلاحظ نمطًا واضحًا:

> الفئات المرتبطة بـ **الوصول المالي، التأمين، السجلات، والالتزام**  
> ⟶ لديها احتمال أعلى لـ **Medium / High BVS**

بينما:

> _Don't know / Never had / Refused_  
> ⟶ مرتبطة بقوة بـ **Low BVS**

➡️ هذا يعني أن:

- النموذج **يستفيد بشدة من إعادة ترميز (Re-encoding) ذكية**
    
- وليس فقط One-Hot خام


# تحسينات قوية حسب نوع المتغير

## 🟢 (A) COUNTRY — خطر عدم التوازن

### الملاحظة

- Country B و C: تقريبًا **High ≈ صفر**
    
- Country A: أفضل توزيع
    
- Country C: شديد الهشاشة
    

### تحسينات

#### ✅ 1. Country Risk Encoding

بدل One-Hot فقط، أضف متغيرًا مشتقًا:

`country_high_rate = P(Target=High | country)`








### Feature Engineering Suggestions



  


  


  
  


  

### Model Performance Boost Strategies

- **Preprocessing Recipe (Q4)**: Add steps like: step_mutate for new features, step_target_encode for categoricals, step_smote for imbalance, step_interact for key pairs.

- **Models (Q5-Q12)**: Prioritize XGBoost/LightGBM—strong with categoricals and interactions. Tune with class weights (e.g., scale_pos_weight for High).

- **Metrics (Q7)**: Use macro-F1 or Cohen's Kappa for imbalance; AUC for multiclass ROC.

- **Tuning (Q10-Q12)**: Grid search on learning_rate, max_depth; add Bayesian opt for efficiency.

- **Validation**: Stratified K-fold by target and country to handle distributions.

- **Expected Boost**: These could improve AUC by 5-10% (e.g., from feature aggregates) based on strong signals in financial vars—test via CV.

  




---

![](https://i.imgur.com/Dn6YtMP.png)


Top categorical features by MI: ['Target', 'funeral_insurance', 'medical_insurance', 'motor_vehicle_insurance', 'has_credit_card', 'keeps_financial_records']


📌 هذا المتغير بعد الترميز سيكون غالبًا:

> **Top 3 Feature Importance**


## ✅ 3.2 Feature Engineering قوي جدًا (High ROI)


### 🔥 Financial Discipline Score


اقتراحات هندسة خصائص (Feature Engineering) مبنية على هذه النتائج

```
recipe <- recipe(Target ~ ., data = train_data) %>%
  step_rm(ID) %>%  # إزالة المعرف
  # 1. مؤشر تراكمي للوصول إلى المنتجات المالية الرسمية (أقوى إشارة)
  step_mutate(
    formal_finance_count = 
      (has_credit_card == "Have now") +
      (has_loan_account == "Have now") +
      (has_debit_card == "Have now") +
      (has_internet_banking == "Have now") +
      (has_mobile_money == "Have now") +
      (has_insurance == "Yes"),
    insurance_count = 
      (motor_vehicle_insurance == "Have now") +
      (medical_insurance == "Have now") +
      (funeral_insurance == "Have now")
  ) %>%
  # 2. ترميز ترتيبي للمتغيرات ذات الترتيب الطبيعي
  step_ordinalscore(
    has_credit_card, motor_vehicle_insurance, medical_insurance,
    funeral_insurance, keeps_financial_records,
    levels = list(
      "Never had" = 0, "Used to have but don’t have now" = 1,
      "Have now" = 2, "Don't know" = 0
    )
  ) %>%
  # 3. دمج الفئات النادرة أو المشابهة في التوزيع
  step_other(all_nominal_predictors(), threshold = 0.02) %>%
  # 4. معالجة عدم التوازن (اختياري في الـ recipe)
  step_smote(Target, over_ratio = 0.5) %>%
  step_dummy(all_nominal_predictors())
```

### 4. **أفكار عامة أخرى لتعزيز الأداء**
   - **معالجة عدم التوازن**: ركز SMOTE أو class weights على العينات مع قيم عالية في هذه المتغيرات (e.g., oversample حيث insurance_count >1 لتعزيز Robust).
   - **اختبار الاقتراحات**: في Q4 (recipe)، أضف خطوات لإنشاء هذه الخصائص. في Q5-Q12، قارن نماذج مع/بدونها لقياس التحسن (e.g., macro-F1، AUC).
   - **توقع التحسن**: هذه الأفكار قد ترفع أداء Robust F1 بنسبة 10-20%، خاصة في الغير شجرية التي تحتاج تمثيلًا أفضل.
   - **تحذير**: اختبر multicollinearity (VIF) بعد إضافة الخصائص الجديدة، وحذف إذا >10.




----

### Better alternatives than SMOTE for your case

#### Option A — Use `class_weight` in LightGBM


```python
from collections import Counter
import numpy as np

# Compute class weights
counter = Counter(y_train)
total = sum(counter.values())
class_weight = {k: total / v for k, v in counter.items()}

# Pass to LightGBM
param = {
    'objective': 'multiclass',
    'num_class': num_classes,
    'boosting_type': 'gbdt',
    'metric': 'multi_logloss',
    'learning_rate': 0.05,
    'num_leaves': 31,
    'max_depth': -1,
    'class_weight': class_weight
}

```

Ah — now I see what’s happening. The key point is: **applying SMOTE didn’t improve your model**. That’s actually very common for **tree-based models like LightGBM**, and here’s why:

---

### 1️⃣ Why SMOTE might not help for LightGBM

1. **Trees handle imbalance naturally**
    
    - LightGBM and XGBoost split nodes based on **information gain**, not distance.
        
    - Minority classes are still considered in splits, so oversampling is often unnecessary.
        
2. **SMOTE introduces synthetic samples**
    
    - These are **interpolated points in feature space**.
        
    - Tree models can treat them as “new leaves,” but sometimes they **don’t add useful information**.
        
3. **Class imbalance is not extreme for `Medium`**
    
    - Only `High` is small (376).
        
    - LightGBM can handle this by **class weighting**, which often works better than SMOTE.
        

---

### 2️⃣ Better alternatives than SMOTE for your case

#### Option A — Use `class_weight` in LightGBM

```python
from collections import Counter
import numpy as np

# Compute class weights
counter = Counter(y_train)
total = sum(counter.values())
class_weight = {k: total / v for k, v in counter.items()}

# Pass to LightGBM
param = {
    'objective': 'multiclass',
    'num_class': num_classes,
    'boosting_type': 'gbdt',
    'metric': 'multi_logloss',
    'learning_rate': 0.05,
    'num_leaves': 31,
    'max_depth': -1,
    'class_weight': class_weight
}
```

- This tells LightGBM to **pay more attention to the minority class**, without adding synthetic points.
    

---

#### Option B — Use `scale_pos_weight` for multiclass (approximate)

- LightGBM doesn’t directly support `scale_pos_weight` for multiclass, but you can use **`is_unbalance=True`** or the `class_weight` method above.
    

---

#### Option C — Stratified CV only

- You already use `StratifiedKFold`.
    
- That ensures each fold contains **roughly the same proportion of each class**, so imbalance is partially handled automatically.
    

---

#### Option D — Feature engineering

- Sometimes imbalance alone isn’t the bottleneck — improving **feature quality** or **feature selection** helps more than SMOTE.
    

---

### 3️⃣ My recommendation

For your dataset:

1. **Drop SMOTE entirely for now**.
    
2. Use **`class_weight` in LightGBM**.
    
3. Keep **Stratified CV**.
    
4. Focus on **tuning learning_rate, num_leaves, max_depth**, and trying **LightGBM with categorical features handled natively**.
    

---

💡 **TL;DR:**

- SMOTE is usually more helpful for **linear models** or **distance-based models** (like KNN, SVM).
    
- For **tree models**, use **class weights** instead of oversampling — it’s cleaner and often performs better.
    

---

If you want, I can **rewrite your Optuna objective** to:

- Remove SMOTE
    
- Add **class weighting**
    
- Work with **Stratified CV** for multiclass
    

This usually gives a **better result than SMOTE**.

Do you want me to do that?











## 7️⃣ **Data Augmentation for Multiclass**

### Numeric

- Gaussian noise (small)
    
- Mixup (works surprisingly well)
    
- Feature dropout
    

### Categorical

- Category noise (low prob)
    
- Shuffle within same class
    
- Rare class oversampling (carefully)



### **B. Interaction & representation learning**

- Pairwise categorical interactions (Top-K only)
    
- Numeric × categorical cross features
    
- Learned embeddings from CatBoost / NN
    
- Clustering features (cluster id as categorical)
    
- PCA / ICA on numeric blocks
    
- Class-specific feature transformations


### **CatBoost multiclass tricks**

- `loss_function=MultiClassOneVsAll`
    
- Very high `l2_leaf_reg` (20–100)
    
- `class_weights` carefully tuned
    
- Use **leaf embeddings** → feed to meta-model
    
- CatBoost + NN stacking (very powerful)


### **A. Probability-level ensembling**

- Arithmetic mean of softmax outputs
    
- Geometric mean of probabilities
    
- Class-wise weighted averaging
    
- Rank averaging on class probabilities
    
- Median ensemble (robust to bad models)

### **B. Stacking / blending (Mandatory)**

- **Level-1:** 20–50 base models
    
- **Level-2:** Logistic Regression / LightGBM
    
- **Level-3:** NN or LightGBM
    
- Use **OOF class probabilities**
    
- Meta-model trained on **logits**, not probabilities
    

🔥 Pro trick:  
Stack **OVR outputs + multiclass outputs together**



## 6️⃣ **Data Augmentation for Multiclass Tabular**

### **Numeric**

- Gaussian noise (class-aware variance)
    
- Mixup (between same-class samples)
    
- CutMix-Tabular
    
- Adversarial noise (FGSM on logits)
    
- SMOTE-NC / Borderline-SMOTE
    
- Manifold Mixup






















    

### **D. Feature Selection with Shapley Values**

- Use **SHAP importance** to remove weak/noisy features that add variance.
    
- Sometimes fewer features = better generalization.
    

---


## **5️⃣ Noise & Regularization Handling**

- **Label smoothing** for multiclass classification: reduces overconfidence.
    
- **Robust loss functions** (Huber or Fair loss) for regression tasks with outliers.
    




1. Repeat for 2–3 layers (residual stacking).

## **3️⃣ Stacking & Blending Like a Pro**

Forget basic 2–3 model stacks. Go **full-on multi-layer**:

1. **Level 0 (base models)**:
    
    - LightGBM with different seeds & boosting types (`gbdt`, `dart`)
        
    - XGBoost with different `max_depth`, `gamma`, `lambda`
        
    - CatBoost (optional, handles categorical features differently)
        
2. **Level 1 (meta-model)**:
    
    - Ridge, Lasso, or LightGBM itself on OOF predictions
        
    - Optionally add **NN embeddings** from categorical variables as additional features
        
3. **Level 2 (super-meta)**:
    
    - Weighted average of Level 1 models
        
    - Optimize weights with **bayesian search** over CV metric (like `Optuna` on your blending weights)
        
4. **Seed Averaging / Model Bagging**
    
    - Train **10–20 models per type with different seeds** → ensemble predictions → dramatically reduces variance.
        

---


- **Feature Selection**
    
    - Remove noisy features with low importance or high correlation. Sometimes fewer, stronger features beat everything else.
    

<mark>monotone_constraints</mark>

```python
import xgboost as xgb

# Suppose we have 3 features: ['age','income','debt']
# We want predictions to increase with income and decrease with debt
monotone_constraints = (0, 1, -1)

model = xgb.XGBClassifier(
    n_estimators=100,
    learning_rate=0.05,
    max_depth=6,
    monotone_constraints=monotone_constraints
)
model.fit(X_train, y_train)

```


 df %>%
+ select(where(is.character)) %>%
+ pivot_longer(everything(), names_to = "column", values_to = "level") %>%
+ count(level, sort = TRUE) %>%
+ filter(n > 100, level != "Unknown" & !is.na(level)) %>%  # adjust threshold
+ print(n = 20)
# A tibble: 24 × 2
   level                               n
   <chr>                           <int>
 1 No                              63659
 2 Yes                             60984
 3 Never had                       43328
 4 Have now                        10633
 5 Don't know                       6675
 6 Low                              6280
 7 Yes, sometimes                   6228
 8 Used to have but don't have now  4607
 9 Female                           4303
10 Male                             3371
11 Medium                           2868
12 A                                2674
13 D                                2612
14 C                                2388
15 B                                1944
16 Yes, always                      1910
17 Don?t know / doesn?t apply       1629
18 Don’t know or N/A                1608
19 0                                1265
20 Used to have but don’t have now  1108




