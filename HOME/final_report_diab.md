
# **Exploratory Data Analysis and Feature Engineering for Diabetes Prediction**

![](https://i.imgur.com/LSpVPTU.png)


## **Body Mass Index (BMI)**

**Summary Statistics:**

```
count    18,581
mean       27.44
std        17.78
min        16.3
25%        24.0
50%        25.9
75%        27.9
max       535.29
```

Values such as 535 are clearly **data errors or synthetic noise**, causing:
BMI is often **one of the top predictive features** in diabetes datasets.

**Extreme Outliers:**

- 286 rows have BMI > 45 or < 10.
    
- Example values:
    

```
535.29, 342, 338, 324, 318, 316, 312, 303, 302, 300
```

> Outliers must be addressed during preprocessing.

![](https://i.imgur.com/805CJ00.png)  


---

## **Age (`years_old`)**

The age variable shows a realistic distribution ranging from 15 to 94 years, with a mean age of approximately 50 years. The distribution appears approximately symmetric, with the majority of individuals between 42 and 59 years. No implausible age values were detected, indicating good data quality


![](https://i.imgur.com/O0D66Zg.png)

## **Skewness & Kurtosis Summary**


| Variable            | Skewness | Kurtosis |
| ------------------- | -------- | -------- |
| years_old           | 0.0456   | -0.2620  |
| weekly_alcohol      | 6.6888   | 44.4553  |
| weekly_exercise_min | 5.3203   | 32.0801  |
| nutrition_index     | -0.0392  | -0.1869  |
| daily_sleep_hrs     | 1.0453   | 7.2508   |
| daily_screen_hrs    | 1.5502   | 5.9090   |
| body_mass_idx       | 13.3740  | 198.0803 |
| whr                 | 0.0586   | 0.0089   |
| bp_systolic         | 13.1431  | 190.1980 |
| bp_diastolic        | 3.4799   | 17.8407  |
| pulse_rate          | 3.6060   | 20.6198  |
| total_chol          | 13.0102  | 198.6296 |
| hdl_chol            | -0.0243  | -0.1322  |
| ldl_chol            | 0.1380   | -0.0885  |
| trig_level          | 8.1454   | 66.7664  |
| family_diabetes     | 2.0201   | 2.0811   |
| has_hypertension    | 1.6354   | 0.6747   |
| has_cardiovascular  | 5.4018   | 27.1820  |

Most variables like **age, nutrition index, WHR, HDL, and LDL cholesterol** are roughly normally distributed. **BMI, blood pressure, cholesterol, triglycerides, alcohol, and exercise** are highly right-skewed with heavy tails, indicating a few extreme values. **Sleep, screen time, and health conditions** show moderate skewness, reflecting that most participants have typical values while a minority have higher values.


# **Mutual Information Analysis of Numeric Features**

![](https://i.imgur.com/FucH7tk.png)
Mutual information confirms that **family history and lifestyle factors** (exercise, age) are the most relevant predictors, while several physiological measurements contribute little individually. These results can help guide **feature selection** and preprocessing for model training.

# **Mutual Information of Categorical Features**
![](https://i.imgur.com/qXQ1twK.png)
Categorical variables in this dataset contribute very little information compared to numeric features, with `edu_status` being the only slightly informative feature. This suggests that **feature selection or preprocessing efforts should focus primarily on numeric predictors**.


# **Categorical vs Target Analysis**
![](https://i.imgur.com/rV1uoPe.png)
most categorical features show **small differences in target distribution**, with **education level and income showing slightly more variation**. This aligns with the mutual information results, confirming that **categorical features contribute very little predictive power individually** compared to numeric features.

# Distribution by target
![](https://i.imgur.com/btWuVmY.png)


---

## **8️⃣ Correlation Analysis**
Spearman correlation is non-parametric and measures monotonic relationships (how variables rank-order together), making it more robust to outliers, non-normality, or non-linear patterns than Pearson (which assumes linearity).
![](https://i.imgur.com/fRCFiqF.png)
![](https://i.imgur.com/8HMsCWJ.png)
- **Strong Positive Correlations with Diabetes:**

| Variable                | Correlation | Interpretation                                  |
| ----------------------- | ----------- | ----------------------------------------------- |
| **family_diabetes**     | **0.21**    | Family history strongly increases diabetes risk |
| **years_old**           | 0.16        | Older age → higher diabetes risk                |
| **body_mass_idx (BMI)** | 0.10        | Obesity is associated with diabetes             |
| **bp_systolic**         | 0.10        | High blood pressure linked to diabetes          |
| **ldl_chol**            | 0.10        | Bad cholesterol increases risk                  |
| **trig_level**          | 0.09        | High triglycerides associated                   |
| **total_chol**          | 0.08        | Cholesterol related to metabolic syndrome       |


- **Negative Correlations:** 

|Variable|Correlation|Meaning|
|---|---|---|
|**weekly_exercise_min**|**-0.16**|Exercise reduces diabetes risk|
|**nutrition_index**|-0.05|Healthy diet reduces risk|
|**hdl_chol**|-0.05|Good cholesterol is protective|


- **Weak/No Relationship:** sleep, screen time, alcohol, pulse rate, cardiovascular history.



**What This Means:**  
Family history, age, BMI, blood pressure, and lipid-related measures such as LDL cholesterol and triglycerides are strong predictors of diabetes. making them highly informative for predictive models. Including them will help the model accurately identify individuals at higher risk of developing diabetes.

**Multicollinearity Risk**
We observe a strong correlation (**0.76**) between `total_chol` and `ldl_chol`, `BMI ↔ WHR` (**0.72**). While tree-based models like XGBoost can handle this, we should be aware of this redundancy when analyzing feature importance. and for  linear models, we should remove one variable or use regularization (LASSO, Ridge)


**Grouped Means by Diabetes Diagnosis**
![](https://i.imgur.com/lLrrCwR.png)

Individuals diagnosed with diabetes are older on average and have higher BMI than non-diabetic individuals. They also show slightly higher total cholesterol, indicating greater metabolic and cardiovascular risk.



**Target Distribution**
We examine the distribution of the target variable `has_condition`. This helps us determine if we need to address class imbalance techniques or use stratified cross-validation.
![](https://i.imgur.com/8sWK7I0.png)

The dataset seems imbalanced toward diabetes


**Feature Distributions (Train vs Test)**
Next, we visualize the distributions of numerical features across the Train, Test It is crucial to confirm that the Test set follows a similar distribution to the Train set to ensure model generalization.
![](https://i.imgur.com/lswQlri.png)

# Feature Engineering

- **Orig Mean (Target Encoding):** The probability of diabetes for a given category/value in the real-world dataset. This serves as a robust, leakage-free risk indicator.
- **Orig Count (Frequency Encoding):** How frequently a value appears in the original medical records.






# prepro

# Duplicate Rows Check
Out of **24,706 rows**, **23,706 rows (≈97.7%) are unique**, while **575 rows (≈2.3%) are duplicates** .

## missing values
![](https://i.imgur.com/4a6oGdd.png)
![[Pasted image 20260131160725.png]]

| Missing Values      | % of Total Values | %    |
| ------------------- | ----------------- | ---- |
| income_class        | 16450             | 86.7 |
| weekly_alcohol      | 2789              | 14.7 |
| ldl_chol            | 2661              | 14.0 |
| hdl_chol            | 2499              | 13.2 |
| whr                 | 2054              | 10.8 |
| weekly_exercise_min | 1861              | 9.8  |
| trig_level          | 1852              | 9.8  |
| daily_screen_hrs    | 1683              | 8.9  |
| edu_status          | 1526              | 8.0  |
| total_chol          | 1503              | 7.9  |
| nutrition_index     | 1323              | 7.0  |
| work_status         | 1145              | 6.0  |
| pulse_rate          | 1103              | 5.8  |
| bp_diastolic        | 940               | 5.0  |
| ethnic_group        | 932               | 4.9  |
| bp_systolic         | 886               | 4.7  |
| daily_sleep_hrs     | 720               | 3.8  |
| tobacco_use         | 555               | 2.9  |
| body_mass_idx       | 383               | 2.0  |
| years_old           | 378               | 2.0  |
**income_class** (86.7% missing)  Imputing this column may not make sense, as the imputed values would mostly be guesses. We might consider **dropping this column** or maybe treat as a separate "Unknown" category.

These can be imputed. For numeric columns like cholesterol, alcohol, or WHR, **median imputation** is often safer than mean when the data is skewed.

For categorical variables (edu_status, work_status, ethnic_group, tobacco_use) → **mode imputation**

When it comes time to build our machine learning models, we will have to fill in these missing values . we can also use models such as XGBoost that can handle missing values with no need for imputation. Another option would be to drop columns with a high percentage of missing values (income class), although it is impossible to know ahead of time if these columns will be helpful to our model. Therefore, we will keep all of the columns for now.


# Outliers
```
IQR Outlier Summary:
               Feature Num_Outliers  \
0     has_hypertension         3480   
0      family_diabetes         2744   
0  weekly_exercise_min         1045   
0       weekly_alcohol          718   
0           trig_level          650   
0   has_cardiovascular          590   
0         bp_diastolic          534   
0           total_chol          504   
0          bp_systolic          490   
0           pulse_rate          484   
0      daily_sleep_hrs          390   
0        body_mass_idx          389   
0     daily_screen_hrs          350   
0                  whr          137   
0             hdl_chol          106   
0             ldl_chol           60   
0            years_old           45   
0      nutrition_index           36   

                                        Top_Outliers  
0                                    [1, 1, 1, 1, 1]  
0                                    [1, 1, 1, 1, 1]  
0  [899.1989245657701, 898.6834464699065, 898.492...  
0  [49.97606406564057, 49.899889021577096, 49.885...  
0  [799.8708590169454, 798.3263018400402, 797.659...  
0                                    [1, 1, 1, 1, 1]  
0  [149.8929777631203, 149.79357711772053, 149.79...  
0  [4287.937377098991, 3667.313182116376, 2190.0,...  
0  [2162.42465026183, 2056.0471832318376, 2009.30...  
0  [149.8486559746295, 149.7350988822427, 149.716...  
0  [14.967249144325338, 14.90385671479661, 14.888...  
0     [535.286443240861, 342.0, 338.0, 324.0, 318.0]  
0  [19.996789274235176, 19.99528087819411, 19.990...  
0                        [1.0, 1.0, 1.0, 0.99, 0.99]  
0                     [86.0, 81.0, 81.0, 80.0, 80.0]  
0                [189.0, 185.0, 183.0, 176.0, 176.0]  
0  [93.65675658365878, 93.33108983753584, 93.1820...  
0                          [1.9, 1.9, 1.9, 1.9, 1.9]
```
These outliers are likely **real anomalies** in medical measurements that could affect model performance.

- Binary/Boolean columns like `has_hypertension`, `family_diabetes`, `has_cardiovascular` should **not be treated as having outliers** in a numeric sense. Their “outliers” are artifacts of numeric detection methods (like Z-score or IQR).
    
- Continuous/numeric health measurements (cholesterol, triglycerides, BMI, blood pressure, etc.) have **real extreme values** that could skew analysis.
    
- Some columns like `daily_sleep_hrs` or `weekly_exercise_min` have unusually large values (`14.96 hours/day`, `899 min/week`) that could be **data entry errors or true extremes**.



# Data dist
<mark>(Ignore ordinal features)</mark>
![](https://i.imgur.com/LdI1gA6.png)

Skewed numeric features (|skew| > 0.75) were identified and transformed using the **Yeo–Johnson power transformation** to reduce skewness and mitigate the impact of extreme values. Outliers were detected using the IQR method before and after transformation.

The transformation was **effective for highly right-skewed continuous variables**, particularly **triglycerides** and **weekly exercise minutes**, where the number of detected outliers was substantially reduced (−349 and −254, respectively).
![](https://i.imgur.com/d3EJyrP.png)
![](https://i.imgur.com/vw76Nwq.png)
![](https://i.imgur.com/yOaTcqY.png)
For several physiological measurements (e.g., **BMI, blood pressure, pulse rate, cholesterol**), the number of IQR-based outliers increased after transformation, indicating that Yeo–Johnson was **not suitable for these variables** and could distort their natural distributions.
![](https://i.imgur.com/6gVEprR.png)
![](https://i.imgur.com/SsuDQs4.png)
![](https://i.imgur.com/8MLOqYQ.png)


During model training, applying the Yeo–Johnson/log transformation to all numerical variables did not lead to performance improvements. Restricting the transformation to **triglycerides** and **weekly exercise minutes**, or combining Yeo–Johnson on these variables with outlier capping, resulted in only a **marginal gain in performance (≈ +0.001)**, indicating limited practical benefit.



**Yeo–Johnson + log1p** had limited practical impact on outlier reduction. Consequently, these transformations were **not retained for model training**
![](https://i.imgur.com/143mc6u.png)
![](https://i.imgur.com/2C6ppjC.png)
![](https://i.imgur.com/bMEJJkj.png)
![](https://i.imgur.com/BBOMrFF.png)




### **Model Specification**

- XGBoost classifier with tunable hyperparameters (`tree_depth`, `learn_rate`, `min_n`, `loss_reduction`).
    
- `scale_pos_weight` applied to balance minority class.
### **Hyperparameter Tuning**

- Bayesian optimization over 25 iterations (5 initial points) using 5-fold CV.
    
- Metric: ROC AUC.
### **Evaluation on Test Set**

- **ROC AUC:** `0.696`

![](https://i.imgur.com/6aIq7ug.png)
![](https://i.imgur.com/uGuwGOw.png)

- Top predictors identified using **VIP** plot (XGBoost built-in importance):
    
    - Features like `family_diabetes`, `weekly_exercise_min`, `age_bim`








# **Potential Improvements**

### **Target Encoding and Count Encoding Enhancement**

To improve model performance, **K-Fold target encoding** and **count encoding** were applied to categorical features in **Python**. Target encoding was performed using out-of-fold means to prevent data leakage, while count encoding captured category frequency information.

These encodings resulted in a **substantial improvement in model performance (+0.10 to +0.30)** across multiple models. However, this enhancement was **not implemented in the R pipeline** due to time constraints and unfamiliarity with efficient K-Fold target encoding in R as i started working on the project late.

