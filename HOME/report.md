


### Introduction

 The workflow involved exploring numeric and categorical features, handling missing values, and creating engineered features capturing financial access, formality, insurance coverage, credit usage, and interactions with owner age and country. Multiple models—including Logistic Regression, LDA, XGBoost, KNN, and Random Forest were trained and tuned using grid search, Bayesian optimization, and other strategies.

### Q2 perform some descriptive analysis on the different variables of the dataset and study their relationship to the target variable
![](https://i.imgur.com/YmSQ77m.png)
![](https://i.imgur.com/calvhCZ.png)
![](https://i.imgur.com/gXaBtAa.png)

- **Highly skewed variables**:  
`personal_income`, `business_expenses`, and `business_turnover` have extremely high maximums and mean >> median, right-skewed, possibly due to outliers or very large businesses.
   
- **Age distribution**: 
- Most business owners are between approximately 25 and 50 years old. 
- There are some outliers at higher ages, but the majority of the data is concentrated around middle age.

Business age and turnover are the strongest numerical indicators of vitality.
<mark>Skewed numeric variables should be transformed (log/Yeo-Johnson), and missing values should be imputed before modeling.</mark>



#### **Feature Importance via Mutual Information**

Analysis of numeric variables shows their relative predictive power for Business Vitality (`Target`):
- **Most Informative Numeric Features:**
    - `personal_income` (0.035)
    - `business_turnover` (0.019)
    - `business_age_months` (0.012)
    - `business_expenses` (0.012)
    - `owner_age` (0.007)

Personal income and business turnover are the strongest numeric indicators of vitality, while age and expenses provide modest contributions. These variables complement categorical features (like insurance and credit) in predictive modeling.


### **Categorical Variables**

**Relationship Between Categorical Variables and Target**

![](https://i.imgur.com/Nj7vgXt.png)
![](https://i.imgur.com/04vPkt1.png)
![](https://i.imgur.com/arIyYwV.png)

- **Country:** Businesses in Country A have the highest proportion of High vitality (11%), while Country C has the lowest (4%), indicating regional differences in business performance.
- **Attitudes and Perceptions:** Positive perceptions about stability, tax compliance, and insurance availability generally correspond to slightly higher vitality, though the differences are moderate. For example, businesses that comply with income tax have a higher share of High vitality (17%) compared to those that don’t (3–9%).
- **Financial Services & Technology Access:** Ownership of mobile money, credit cards, loan accounts, and internet banking strongly correlates with higher vitality. Businesses with these services show significantly higher Medium and High scores. For instance, businesses with a credit card have 58% in High vitality versus 4–8% for those without.
- **Insurance Coverage:** Businesses that have insurance (general, medical, or funeral) are more likely to be in the Medium or High vitality categories.
- **Marketing and Motivation:** Using word-of-mouth marketing, offering credit to customers, and a motivation to make more money slightly increase the chances of being Medium or High vitality.
- **Business Problems & Funding:** Businesses experiencing cash flow problems or sourcing money from informal lenders tend to have lower vitality, while those relying on formal financial sources or personal savings perform slightly better.


#### **Feature Importance via Mutual Information**

**Top Features:** `funeral_insurance` (0.23), `medical_insurance` (0.065), `motor_vehicle_insurance` (0.059), and `has_credit_card` (0.055) provide the most predictive power among the variables.

<mark>Financial service access (insurance and credit) is a critical driver of business vitality and should be prioritized in predictive modeling.</mark>
![](https://i.imgur.com/xppfLzY.png)



#### **Missing Values**
![](https://i.imgur.com/JNzH94Y.png)


Several features in the dataset contained a substantial proportion of missing values.



**Data Issue**
The variable `current_problem_cash_flow` contains inconsistent entries: `'Yes'`, `'No'`, `'0'`, and missing values. These should be **cleaned**.


### Q4. Propose any recipe and justify the use of every step function

```r
rec <- recipe(Target~., data=train_data) %>%
  step_string2factor(all_nominal_predictors()) %>% 
  step_unknown(all_nominal_predictors(), new_level="missing") %>% 
  step_impute_median(all_numeric_predictors()) %>% 
  step_winsorize(all_numeric_predictors(), limits = c(0.01, 0.99)) %>% 
  step_log(all_numeric_predictors()) %>% 
  step_dummy(all_nominal_predictors(), one_hot=TRUE) %>% 
  step_zv(all_predictors()) %>% 
  step_nzv(all_predictors())
  
  
for xgboost/randomforest
rec_tree <- recipe(Target~., data=train_data) %>%
  step_string2factor(all_nominal_predictors()) %>% 
  step_unknown(all_nominal_predictors(), new_level="missing") %>% 
  step_impute_median(all_numeric_predictors()) %>% 
  step_dummy(all_nominal_predictors(), one_hot=TRUE) %>% 
  step_zv(all_predictors()) %>% 
  step_nzv(all_predictors())


```

```
step_unknown(all_nominal_predictors(), new_level="missing")
```
```
`step_impute_median(all_numeric_predictors())`
```
all models can be trained without missing-value errors.
```
  step_log(all_numeric_predictors()) %>% 
```
A logarithmic transformation is applied to reduce skewness and outliers.

```
step_normalize(all_numeric_predictors())

```
**Rescaling numeric predictors to have comparable magnitudes**

```
step_dummy(all_nominal_predictors(), one_hot = TRUE)
```
one-hot encoding, Converts categorical variables into numeric format.
```
step_zv(all_predictors())
step_nzv(all_predictors())
```
Predictors with **no variability** carry no information

```
  step_winsorize(all_numeric_predictors(), limits = c(0.01, 0.99)) %>% 
```
 Cap outliers at 1st and 99th percentile

# Q5
```r
# Logistic Regression
log_mod <- multinom_reg(mode="classification") %>% set_engine("nnet")
log_wf <- workflow() %>% add_model(log_mod) %>% add_recipe(rec)
log_fit <- fit(log_wf, data=train_data)

# XGBoost
train_data_xgb <- train_data %>% mutate(Target=as.character(Target))
rec_xgb <- rec %>% update_role(Target, new_role="outcome")
xgb_mod <- boost_tree(mode="classification", trees=500, tree_depth=6,
                      learn_rate=0.1, loss_reduction=0, sample_size=0.8,
                      mtry=ncol(train_data)-1) %>% set_engine("xgboost")
xgb_wf <- workflow() %>% add_model(xgb_mod) %>% add_recipe(rec_xgb)
xgb_fit <- fit(xgb_wf, data=train_data_xgb)

# LDA
lda_mod <- discrim_linear(mode="classification") %>% set_engine("MASS")
lda_wf <- workflow() %>% add_model(lda_mod) %>% add_recipe(rec)
lda_fit <- fit(lda_wf, data=train_data)
# KNN
knn_mod <- nearest_neighbor(
  mode = "classification",
  neighbors = 20,       # you can tune this later
  weight_func = "rectangular",
  dist_power = 2
) %>% set_engine("kknn")
knn_wf <- workflow() %>% add_model(knn_mod) %>% add_recipe(rec)
knn_fit <- fit(knn_wf, data = train_data)
# Random Forest
rf_mod <- rand_forest(
  mode = "classification",
  trees = 500,
  mtry = floor(sqrt(ncol(train_data) - 1)),  # default: sqrt(p)
  min_n = 5                                  # minimum node size
) %>% set_engine("ranger")
rf_wf <- workflow() %>% add_model(rf_mod) %>% add_recipe(rec)
rf_fit <- fit(rf_wf, data = train_data)

```

# Q6
- Logistic: Default settings provide a stable baseline for comparison with more complex models.
- XGBoost:
	- **trees = 500**: Ensures sufficient model complexity to capture nonlinear relationships.
	- **tree_depth = 6**: Balances model expressiveness and overfitting; deep enough to capture interactions but shallow enough to generalize.
	- **learn_rate = 0.1**: A moderate learning rate for stable convergence.
	- **loss_reduction = 0**: No minimum reduction threshold; allows splits as long as the gain is positive.
	- **sample_size = 0.8**: Introduces row subsampling to reduce overfitting.
	- **mtry = ncol(train_data)-1**: Uses all features at each split to maximize predictive power for a small-medium feature set.
- Linear Discriminant Analysis (LDA):No explicit hyperparameters were tuned.
LDA works well when numeric variables are moderately normally distributed and classes are linearly separable.
- k-Nearest Neighbors (KNN)
	- **neighbors = 20**: Provides a smooth class estimate, reducing variance compared to smaller k; larger k prevents overfitting to noisy samples.
	- **dist_power = 2**: Uses Euclidean distance, the default and widely effective metric for continuous features.
 - Random Forest:
	- **trees = 500**: Ensures the ensemble is large enough to stabilize predictions and reduce variance.
	- **mtry = floor(sqrt(ncol(train_data)-1))**: Standard default for classification; balances feature randomness and tree diversity.
	- **min_n = 5**: Prevents overly small terminal nodes, reducing overfitting to noise.


# Q7-Q9
![](https://i.imgur.com/MrqQRNA.png)
![](https://i.imgur.com/27CduHr.png)
![](https://i.imgur.com/SvnBsjZ.png)
![](https://i.imgur.com/sv9bCoE.png)
![](https://i.imgur.com/97ola9m.png)

**Model Evaluation on Test Set**:We used **ROC-AUC** because it measures the model’s ability to **correctly rank and discriminate between classes** using predicted probabilities, **regardless of class imbalance or chosen threshold**, making it ideal for evaluating multi-class business vitality predictions.
and also Kappa to account for class imbalance.
![](https://i.imgur.com/XvtCF2Z.png)

**XGBoost** achieves the highest overall performance, with the best accuracy, Kappa, and slightly better ROC-AUC, indicating it balances discrimination and class prediction well.
These results justify choosing **XGBoost** as the strongest predictive model, while simpler models like Logistic Regression and LDA remain viable for interpretability.


Logistic Regression achieved the highest overall performance after SMOTE was applied to balance the class distribution in the training set. <mark>but it was later removed</mark>.
![](https://i.imgur.com/ExTSFsA.png)




# Q11
![](https://i.imgur.com/j4gG0nf.png)
Yes, the ranking changed slightly after tuning based on ROC-AUC.
**LDA now has the highest ROC-AUC**, meaning it provides the best probability-based discrimination across the classes.

# Q12
![](https://i.imgur.com/Y3g8blk.png)
the **ROC-AUC improved slightly** from 0.921 (Grid Search) to **0.926** for Latin Hypercube, Racing, and Successive Halving, showing better probability discrimination across classes. Accuracy and Kappa also increased, indicating improved overall classification performance.

# Q13
The **best model** on the testing set is **LDA**:

| Metric       | Value |
| ------------ | ----- |
| **Accuracy** | 0.845 |
| **Kappa**    | 0.666 |
| **ROC-AUC**  | 0.926 |
The model Achieves **excellent class discrimination** across all target levels (ROC-AUC 0.926).


# Q14
![](https://i.imgur.com/LQZ92Pe.png)
![](https://i.imgur.com/vk0hyw9.png)
**Low class**: highest precision and recall.
**High class**: moderate performance, lower recall
**Medium class**: lowest precision and recall






<mark>The homework ends here.</mark>

---

---


---

Other work of the analysis and modeling were performed using **Python**, and the code is available at: [https://github.com/ismailSadouki/asri-homework]. 
The following tasks were performed:

- **Model building** using:
    - SGD
    - Logistic Regression with **lbfgs** solver
    - Logistic Regression with **Saga** solver
    - SVM with **RBF, polynomial, and linear** kernels


For these models:
    - **Bayesian optimization** was applied using **Optuna** for hyperparameter tuning.
    - **Log transformation** was applied to `owner_age`.
    - **Yeo-Johnson transformation** was applied to `['funeral_insurance', 'medical_insurance', 'motor_vehicle_insurance', 'has_credit_card', 'keeps_financial_records']`.
    - **Outliers were capped** to limit extreme values and reduce their impact on the models.
    - **SMOTE** was used to handle imbalanced classes.
![](https://i.imgur.com/jeevl8e.png)
A **VotingClassifier** was built using the top three individual models. Combining their predictions improved overall performance, achieving a **ROC AUC of 0.95**.


- **Ensemble and gradient boosting models**:
    - XGBoost, LightGBM, CatBoost, Random Forest, ExtraBoost, Adaboost, and HistGradientBoosting were also trained.
    - **Optuna** was used for hyperparameter tuning for all these models.
    - **Class weights** were applied to handle class imbalance during training.
    - All models showed **similar accuracy** around 94/95 ROC AUC.

**Engineered Features**
- **`funeral_insurance_Have_now`** – whether the individual currently has funeral insurance.
- **`insurance_coverage_score`** – total number of insurance types the individual has.
- **`insurance_count`** – count of all insurance products owned.
- **`insurance_age`** – insurance count multiplied by owner’s age.
- **`insurance_country_weighted`** – insurance count adjusted by country factor.
- **`insurance_ratio`** – proportion of insurance products owned.
- **`financial_access_score`** – total access to financial services.
- **`financial_formality_index`** – total formal financial products held.

> These features showed importance in the Xgboost/Random Forest model using SHAP values.

SHAP with RandomForest
![](https://i.imgur.com/B7Wjw1j.png)

SHAP with XGboost
![](https://i.imgur.com/9yH62Rz.png)
![](https://i.imgur.com/4OZTWx0.png)
