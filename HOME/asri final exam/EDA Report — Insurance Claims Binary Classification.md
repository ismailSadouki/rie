





---

## Table of Contents

1. [Dataset Overview & Scale](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#1-dataset-overview--scale)
2. [Target Variable — Deep Visual Analysis](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#2-target-variable--deep-visual-analysis)
3. [Duplicate Analysis & Data Quality](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#3-duplicate-analysis--data-quality)
4. [Feature Inventory & Cardinality](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#4-feature-inventory--cardinality)
5. [Distribution Analysis — Feature by Feature](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#5-distribution-analysis--feature-by-feature)
6. [Boxplot Analysis — Class Separation](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#6-boxplot-analysis--class-separation)
7. [Violin Plot Analysis — Shape & Separation](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#7-violin-plot-analysis--shape--separation)
8. [Categorical Feature Analysis — Bar Charts](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#8-categorical-feature-analysis--bar-charts)
9. [Mutual Information Analysis](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#9-mutual-information-analysis)
10. [Statistical Tests — T-Test & ANOVA](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#10-statistical-tests--t-test--anova)
11. [Correlation & Multicollinearity](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#11-correlation--multicollinearity)
12. [Data Transformation Strategy](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#12-data-transformation-strategy)
13. [Consolidated Feature Importance](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#13-consolidated-feature-importance)
14. [Key Visual Findings Summary](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#14-key-visual-findings-summary)
15. [Modelling Recommendations](https://claude.ai/chat/e8bfd024-7a0a-4686-a562-a1d66d0c24f4#15-modelling-recommendations)

---

## 1. Dataset Overview & Scale

This is a **large-scale binary classification problem** on insurance claims data. Based on the duplicate analysis, the dataset scale is:

- **Training set:** ~90,829 rows
- **Test set:** ~22,731 rows
- **Features:** ~113 numerical + 19 categorical = ~132 total columns
- **Target:** Binary (0 = minority class, 1 = majority class)
- **Class ratio:** 76.1% class 1 vs 23.9% class 0

The dataset represents an insurance claims processing system enriched with customer, policy, geographic, medical, fraud, and operational metadata.

---

## 2. Target Variable

![](https://i.imgur.com/0RbTdG9.png)
The **pie chart** and **bar chart** make the class imbalance immediately clear.


**It is best to not use accuracy as our metric.** A model that always predicts class 1 scores 76.1% accuracy while being completely useless. Rather we will use **ROC-AUC** as the primary metric and **F1 for class 0** or **Precision-Recall AUC** as secondary metrics.

---

## 3. Duplicate Analysis & Data Quality

### Train Set Top Duplicates

|Feature|Max Duplicates|Dominant Value|
|---|---|---|
|`fraud_indicator_flag`|90,829|B (entire dataset)|
|`claim_type`|88,487|C|
|`severity_index`|87,731|0|

### Test Set Top Duplicates

|Feature|Max Duplicates|Dominant Value|
|---|---|---|
|`fraud_indicator_flag`|22,731|B|
|`claim_type`|22,097|C|
|`severity_index`|21,993|0|

- `fraud_indicator_flag = B` appears in virtually every row. This column is near-constant and will contribute almost zero information in its raw form. However, the **minority values (A and C)** likely carry strong signal — the histogram shows `fraud_indicator_flag = C` gives 90.9% class 1 rate vs B's 76%. We will Encode as dummy with B as baseline.
- `severity_index = 0` dominates (87,731/90,829 = 96.6% of rows). From the boxplot, the non-zero values of `severity_index` appear as pure scatter outliers. visually the feature shows zero for both classes with occasional dots up to 12. It acts as a **zero-inflated sparse feature**. We will create a binary indicator `is_nonzero_severity_index` and use the raw value for the non-zero segment.
![](https://i.imgur.com/t8vgXry.png)

- `claim_type = C` is the most common but not overwhelmingly so. The target bar chart shows C has 75.9% class 1 vs A's 96.1% so its still useful.


---

## 4. Feature Inventory & Cardinality

### Categorical Cardinality


| Feature                   | Unique Values | Encoding Strategy                                   |
| ------------------------- | ------------- | --------------------------------------------------- |
| `claim_detail_code`       | 18,210        | Target /Catboost/frequency or hash encoding with CV |
| `coverage_type`           | 122           | Target encoding <br>or Catboost encoding with CV    |
| `policy_type`             | 36            | Target encoding (with CV) or OHE                    |
| `location_code`           | 90            | Target encoding or catboost encoding with CV        |
| `claim_complexity`        | 18            | OHE                                                 |
| `payment_method`          | 12            | OHE                                                 |
| `priority_level`          | 9             | OHE                                                 |
| `document_completeness`   | 10            | OHE                                                 |
| `adjuster_recommendation` | 7             | OHE                                                 |
| `automation_eligible`     | 7             | OHE                                                 |
| `policy_status`           | 7             | OHE                                                 |
| `risk_category`           | 5             | OHE                                                 |
| `customer_segment`        | 4             | OHE                                                 |
| `claim_type`              | 3             | OHE                                                 |
| `geographic_region`       | 3             | OHE                                                 |
| `claim_channel`           | 3             | OHE                                                 |
| `fraud_indicator_flag`    | 3             | OHE                                                 |
| `provider_network_status` | 3             | OHE                                                 |

**`claim_detail_code`** with 18,210 unique values is the most challenging. Direct one-hot encoding is computationally infeasible. Target encoding, frequency encoding, or grouping rare codes into an `OTHER` bucket are the recommended approaches.


### Pseudo-Categorical Numerical Features (Low Unique Counts)
![](https://i.imgur.com/4esoyim.png)
These should be treated as **ordinal or categorical** during feature engineering rather than pure continuous variables. Treating them as continuous may result in spurious interpolation by linear models.

---

## 5. Distribution Analysis 

- **`prior_denial_indicator`** : Shows a sharp spike at 0 and very small counts at 1, 2+. But the class 1  bar at values 1+ is visibly higher relative to class 0. Combined with its t-stat of 75.8, this is the most powerful single predictor in the dataset despite the odd distribution.
![](https://i.imgur.com/MfhvEVJ.png)

- **`coverage_limit`**: Left-skewed, class 0  has noticeably more mass at lower values while class 1  peaks higher. **Very visible separation**.
![](https://i.imgur.com/Kum3eTV.png)

- **`cross_sell_index`** : Left-skewed, mass at 12–14. Class 0 appears to have slightly more low-end mass. Significant.
![](https://i.imgur.com/C0ajWm9.png)


- **`claim_processing_days`** : This visual separation is the clearest in ths entire section which confirms the t-stat of –45.2.
![](https://i.imgur.com/zkDNfs8.png)

- **`underwriting_confidence`** : Nearly entirely zero. We will Create a binary `is_nonzero_underwriting_confidence` flag.
![](https://i.imgur.com/h55Pzjj.png)

- **`reinsurance_amount`** : Right-skewed but with a visible KDE for both classes. class 1 shifted slightly left.
![](https://i.imgur.com/j65LAoR.png)




- **`api_response_quality`** :This is highly unusual and may indicate a discretized or bimodal feature. Both classes seem similarly distributed, making this low in marginal utility.
![](https://i.imgur.com/YyX6S3t.png)




### 5.5 Bimodal / Unusual Distributions 

- **`urban_density_index`** : **The most visually striking distribution in the dataset.** Shows two clear modes: a sharp spike at ~2.5 and a broader second peak at ~8–10. In the KDE by target overlay, class 0 (blue) has a taller spike at 2.5 while class 1 (orange) appears relatively more concentrated at the 8–10 peak. This suggests **urban claimants (higher urban density index) are more likely to be class 1**. 
We will Create a binary feature `is_high_urban_density` (threshold ~5–6). This is a strong candidate for **interaction terms** and **segmentation**.
![](https://i.imgur.com/VtKwg50.png)

- **`customer_value_score`** : The histogram reveals an extreme spike at 0 with rare higher values (1, 2, 3 range). Both classes show this zero-spike but class 1 has a slightly higher orange bar at 1–2. 
This confirms the `sqrt` transformation we suggested, and also suggests a **binary indicator** for `customer_value_score > 0`.
![](https://i.imgur.com/u4X6hoh.png)



### 5.6 Features with Visible Class Separation in Histograms

the features where class 0 (blue) and class 1 (orange) KDE curves are **visually distinguishable** are:

| Feature                     | Direction of Separation                            | Visual Strength |
| --------------------------- | -------------------------------------------------- | --------------- |
| `claim_processing_days`     | Class 1 has wider/right-shifted distribution       | +++++           |
| `coverage_limit`            | Class 0 has more low-value mass                    | +++++           |
| `customer_value_score`      | Class 1 has higher mass at non-zero values         | ++++            |
| `prior_denial_indicator`    | Class 1 has more mass at 1+                        | ++++            |
| `policy_premium_annual`     | Class 0 has more mass at low values                | ++++            |
| `communication_touchpoints` | Class 1 has higher mass at 1 vs 0                  | +++             |
| `urban_density_index`       | Class 0 peaks more at low density, class 1 at high | +++             |
| `deductible_ratio`          | Class 1 slightly right-shifted                     | ++              |
| `fraud_probability_score`   | Class 0 has more very-high-value tail              | ++              |
| `customer_loyalty_score`    | Slight differential at ceiling                     | +               |

---

### Important Outlier Observations from Boxplots
![](https://i.imgur.com/KWpVE6S.png)


- **`estimated_settlement_amount`**: The boxplot shows class 0 has **zero** as both the median AND the IQR box, while class 1 shows the same flat zero line. Both have extreme upper whiskers to ~20. This confirms the feature is zero-inflated for both classes.
	`is_nonzero_estimated_settlement_amount` binary flag might be useful.

- **`prior_denial_indicator`**: Class 0 box is extremely tight near 0–1, while class 1 box extends noticeably higher (1–2.5 range visible). This is the clearest boxplot separation in the dataset.
- **`customer_value_score`**: Flat lines at 0 for both classes, identical to `severity_index` pattern. It's a zero-dominated sparse feature. However its t-stat of –43.4 means the tiny number of non-zero values carry massive predictive power.
- **`cross_sell_index`** (p.28): Class 1 box is visibly shifted up relative to class 0, confirms t-stat of –23.2.
- **`recovery_probability`**: Both boxes near 1–2, near-identical. Low marginal value despite significant t-stat (–22.8), the difference must be at the tails.



**Outlier treatment strategy:**

- For tree-based models: No transformation needed; trees are robust to outliers.
- For linear models: Winsorize at 1st–99th percentiles before applying the transformations.
- `estimated_settlement_amount` and `underwriting_confidence` may benefit from a **binary indicator** ("is non-zero") as an auxiliary feature
---

## 7. Violin Plot Analysis



**`urban_density_index`** (p.34): **Unmistakable bimodal violin shape.** Both classes have a figure-8 shape, two distinct bulges at ~2–3 and ~8–9. However, the class 0  violin has a **larger upper bulge at 2–3** while class 1  has a **relatively larger lower bulge at 8–9**. This inverted emphasis confirms: higher urban density → more class 1.
We might Create `urban_density_group` binary feature.
![](https://i.imgur.com/07JJcZ4.png)


**`claim_processing_days`** : The most visually distinctive violin in the dataset. Class 0 (green) has a very thin, needlelike violin, almost all mass at 0. Class 1 (orange) has a wide, rotund violin with significant mass spread between 0 and 5. The difference in body width is dramatic.
![](https://i.imgur.com/AfWB7Pj.png)



**`underwriting_confidence`** (p.32): Both violins are essentially flat needles at 0. The feature is nearly constant, both classes show a thin vertical line with no violin body. **Drop or binary-encode only.**
![](https://i.imgur.com/HGEy8G6.png)





**`api_response_quality`**: The distinctive **U-shaped violin** is visible here, both classes have mass at low AND high values with a waist in the middle. Class 0 has a slightly wider upper bubble. Unusual distribution that may reflect a bimodal operational metric.
![](https://i.imgur.com/rv8VeuJ.png)


**`witnesses_count`** (p.33): Wide hourglass violin for both classes, high variance. Both classes appear nearly identical in shape but the violin bodies are broad, confirming high variance and the high correlation with `api_response_quality` (r=0.998). This suspicious pair likely represents the same latent signal.

![](https://i.imgur.com/8Aju83Q.png)



# CDF comparison

For each numerical feature, we compared the distribution of the full population vs. the positive class (target=1) using CDF. The scaled difference between CDFs (KS-style) quantifies separation.
Features with large KS peaks = high discriminative power = useful for classification. 
Features where the CDFs overlap = no signal = candidates for removal.
**Top Features:**
![](https://i.imgur.com/MzkINIQ.png)
![](https://i.imgur.com/O0NzHld.png)
![](https://i.imgur.com/1YCaOXV.png)
![](https://i.imgur.com/5wnPDaI.png)
![](https://i.imgur.com/G16dMCD.png)
![](https://i.imgur.com/cLEwmGP.png)
![](https://i.imgur.com/zNVi6hP.png)
![](https://i.imgur.com/ywVHlAY.png)
![](https://i.imgur.com/SQ9lUCc.png)
![](https://i.imgur.com/NnjBr7i.png)
![](https://i.imgur.com/hoAqchW.png)



---

## 8. Categorical Feature Analysis

### Perfect or Near-Perfect Separating Levels

| Feature          | Category | Class 0 Rate | Class 1 Rate | Action                     |
| ---------------- | -------- | ------------ | ------------ | -------------------------- |
| `priority_level` | A        | 100%         | 0%           | Hard rule: predict class 0 |
| `priority_level` | K        | 0%           | 100%         | Hard rule: predict class 1 |
| `priority_level` | L        | 0%           | 100%         | Hard rule: predict class 1 |
| `claim_type`     | A        | 3.9%         | 96.1%        | Near-certain class 1       |
|                  |          |              |              |                            |
Priority level A is an **exact rule** for class 0 prediction. This is gold: any claim with `priority_level = A` should be predicted as class 0 with certainty.

![](https://i.imgur.com/mjCHaxv.png)


![](https://i.imgur.com/H8QgVwM.png)

### The `claim_detail_code` 

 `claim_detail_code` shows the chaotic striped pattern of thousands of categories with wildly varying class proportions some codes are 100% class 1, some 0% class 1. This extreme variation is exactly what gives it MI = 0.115 (the highest of all features). Target encoding this column is essential.
![](https://i.imgur.com/NTCli9s.png)


### High-Discrimination Categories

| Feature                   | Most Class-0-Prone | Most Class-1-Prone |
| ------------------------- | ------------------ | ------------------ |
| `geographic_region`       | A (27.9% class 0)  | C (91.8% class 1)  |
| `document_completeness`   | E (37.1% class 0)  | H (100% class 1)   |
| `claim_channel`           | B (35.6% class 0)  | C (85.6% class 1)  |
| `provider_network_status` | A (31.1% class 0)  | B (83.2% class 1)  |
| `claim_type`              | C (24% class 0)    | A (96.1% class 1)  |
| `fraud_indicator_flag`    | A (14.3% class 0)  | C (90.9% class 1)  |

### Low-Discrimination Features (Visually Uniform Bars)

The bar chart shows these features have nearly identical class proportions across all categories:

- `payment_method`: all bars ≈ 75%/25%, near flat
![](https://i.imgur.com/IUpR580.png)

- `claim_source`  all 22 values ≈ 75%/25%
![](https://i.imgur.com/TokNjIo.png)

- `location_code`: bottom chart completely flat , **entirely indistinguishable across 90+ location codes**. This feature shows nearly zero separation in the bar chart and has MI = 0.0009. Extremely low utility for classification, despite being high-cardinality.
![](https://i.imgur.com/VBuSwnf.png)



---

## 9. Mutual Information Analysis

### Numerical MI 
![](https://i.imgur.com/5w7KkzH.png)
![](https://i.imgur.com/mSBRsob.png)

one dominant feature `prior_denial_indicator` followed by a long flat tail. The implication: **no single numerical feature is highly predictive alone**, and we need many features working together for strong performance.

this is a complex, non-linear classification problem where ensemble methods (XGBoost, LightGBM, Random Forest) are likely to outperform linear models.

Bottom 5 (near-zero MI):

- `estimated_settlement_amount` — 0.0019
- `straight_through_processing_score` — 0.0033
- `severity_index` — 0.0034
- `claim_approval_probability` — 0.0034
- `final_decision_confidence` — 0.0038

### Categorical MI

![](https://i.imgur.com/NH8kv6F.png)

`claim_detail_code` at MI ≈ 0.115  a full **3.8× higher than the next feature** (`coverage_type` at 0.030). This towering dominance makes encoding `claim_detail_code` correctly the single most impactful preprocessing decision for  competitive model performance.


The tail is rapid: `policy_status` and below all have MI < 0.002  essentially noise. Notably:

- `risk_category` MI = 0.001 very low, despite being flagged as interesting
- `priority_level` MI = 0.0007  surprisingly low given the perfect separation rules (MI is a marginal metric; the rules are conditional)
- `customer_segment` MI = 0.000037 effectively zero. **Drop or deprioritize this feature.**

---

## 10. Statistical Tests: T-Test & ANOVA and Chi-Square

### Features that are Statistically Non-Significant (p > 0.05) : Drop Candidates

Based on t-test results, these 16 features show **no marginal association** with the target:

|Feature|p-value|Recommendation|
|---|---|---|
|`composite_risk_index`|0.945|Drop|
|`deductible_amount`|0.940|Drop|
|`data_completeness_score`|0.733|Drop|
|`depreciation_rate`|0.727|Drop|
|`medical_necessity_score`|0.400|Drop from linear; keep for tree|
|`customer_satisfaction_score`|0.804|Drop|
|`follow_up_responsiveness_score`|0.369|Drop|
|`payment_timeliness_score`|0.448|Drop|
|`policy_modifications_count`|0.816|Drop|
|`medication_count`|0.793|Drop|
|`claim_to_premium_ratio`|0.565|Drop|
|`claim_validity_score`|0.187|Borderline; keep for tree|
|`endorsement_value_ratio`|0.276|Drop|
|`duplicate_claim_similarity`|0.118|Drop|
|`straight_through_processing_score`|0.150|Drop|
|`days_since_policy_start`|0.095|Borderline|

Note: These are **marginal** non-significant features. In tree-based models, they may still contribute through interaction effects, we might use permutation importance after fitting before final removal.

The features that show **no statistically significant marginal association** with the target are candidates for removal in linear models .

### Top Significant Features (Sorted by |F-statistic|)

|Feature|F-statistic|Interpretation|
|---|---|---|
|`prior_denial_indicator`|5,742|Overwhelmingly dominant|
|`customer_value_score`|1,884|2nd most powerful|
|`claim_processing_days`|2,044|Top 3|
|`coverage_limit`|1,542|Top 4|
|`policy_premium_annual`|844|Top 5|
|`communication_touchpoints`|796|Top 6|
|`cross_sell_index`|536|Strong|
|`recovery_probability`|520|Strong|
|`previous_claims_value`|400|Strong|
|`severity_index`|340|Strong|

### Chi-Square Test Results

All categorical features were tested for independence with the target using the Chi-square test. :

|Feature|Chi² Statistic|Significance|
|---|---|---|
|`claim_detail_code`|18,343|Highly significant|
|`coverage_type`|4,817|Highly significant|
|`claim_complexity`|3,834|Highly significant|
|`geographic_region`|2,864|Highly significant|
|`document_completeness`|2,653|Highly significant|
|`provider_network_status`|2,494|Highly significant|
|`claim_channel`|2,132|Highly significant|
|`policy_type`|822|Highly significant|
|`customer_segment`|7.3|Marginal (p ≈ 0.064)|

`customer_segment` is the only categorical feature with borderline significance. All others are clearly informative. 


---

## 11. Correlation & Multicollinearity

### Top Correlated Pairs (r > 0.97)



| Feature 1                 | Feature 2                      | r     | Suspicion                       |
| ------------------------- | ------------------------------ | ----- | ------------------------------- |
| `witnesses_count`         | `api_response_quality`         | 0.998 | Likely duplicated data          |
| `network_discount_rate`   | `renewal_count`                | 0.994 | Strong conceptual link          |
| `claim_complexity_score`  | `liability_percentage`         | 0.993 | Conceptual cluster              |
| `claim_validity_score`    | `external_verifications_count` | 0.992 | Conceptual link                 |
| `claim_complexity_score`  | `adjustments_count`            | 0.982 | Complexity cluster              |
| `medical_necessity_score` | `claim_validity_score`         | 0.982 | Medical cluster                 |
| `customer_tenure_years`   | `line_items_count`             | 0.981 | Surprising  check for data leak |
| `reinsurance_amount`      | `specialist_referrals`         | 0.980 | Semantic cluster                |
| `predictive_risk_score`   | `payment_frequency_annual`     | 0.978 | Surprising pair                 |


>[!warning] Note: **Suspicion** column in the table above is chatGPT generated and i will assume that its based on domain knowledge. 
>I dont see any relation between the "column names" and the data itself to apply domain knowledge :( ! That's been said i will consider all of the columns with strong correlation as duplicated data. 

or we follow the next strategy:
- High correlation between features in tree-based models is less problematic but may cause feature importance instability.
- For linear models we drop one feature from each highly correlated pair (or might use PCA/regularization) to address multicollinearity.




---

## 12. Data Transformation Strategy

For **linear models only**:
![](https://i.imgur.com/Dzpp4zu.png)

|Transformation|Features|
|---|---|
|**Original (no transform)**|`deductible_ratio`|
|**Log**|`claim_validity_score`|
|**Square root**|`customer_value_score`|
|**Box-Cox**|`previous_claims_value`, `medical_necessity_score`, `policy_modifications_count`, `external_verifications_count`, `coverage_utilization_rate`, `household_size`, `data_completeness_score`, `medication_count`|
|**Yeo-Johnson**|All remaining numerical features (handles zero/negative values)|

Additional engineering based on visual findings:

- **Binary indicators** to create: `is_nonzero_severity_index`, `is_nonzero_underwriting_confidence`, `is_nonzero_estimated_settlement_amount`, `is_nonzero_customer_value_score`, `is_nonzero_prior_denial_indicator`
- **Bimodal split**: `urban_density_group = (urban_density_index > 5).astype(int)`
- **Communication touchpoint ordinal**: `comm_touchpoints_capped = min(communication_touchpoints, 3)` (8 unique values but most mass at 0/1)

---

## 13. Consolidated Feature Importance

Combining MI, t-tests, ANOVA, and Chi-square into a unified ranking:

### Tier 1 — Critical (Use First, Engineer From)

|Feature|Type|Key Evidence|
|---|---|---|
|`prior_denial_indicator`|Numerical|F=5742, MI=0.042, clear violin/boxplot separation|
|`customer_value_score`|Numerical|F=1884, t=–43.4, visual: zero-inflated spike|
|`claim_processing_days`|Numerical|F=2044, MI=0.024, **most visually discriminating histogram**|
|`coverage_limit`|Numerical|F=1542, MI=0.021, visible left-shift of class 0|
|`claim_detail_code`|Categorical|MI=0.115, Chi²=18343, **#1 categorical predictor**|
|`policy_premium_annual`|Numerical|F=844, t=–29, clear in violin plots|
|`communication_touchpoints`|Numerical|F=796, MI=0.020, visible in violins|

### Tier 2 — High Value

|Feature|Type|Key Evidence|
|---|---|---|
|`cross_sell_index`|Numerical|F=536, t=–23.2|
|`recovery_probability`|Numerical|F=520, t=–22.8|
|`severity_index`|Numerical|F=340 (sparse — use binary indicator)|
|`previous_claims_value`|Numerical|F=400, t=–20|
|`coverage_type`|Categorical|MI=0.030, Chi²=4817, visual bar variation|
|`claim_complexity`|Categorical|MI=0.023, Chi²=3834|
|`geographic_region`|Categorical|MI=0.019, Chi²=2864, visual: B and C are ~91% class 1|
|`document_completeness`|Categorical|MI=0.015, Chi²=2653, Grade E = 37% class 0|
|`policy_renewal_months`|Numerical|F=256|
|`reinsurance_amount`|Numerical|F=171, t=13.1|

### Tier 3 — Moderate (Include, Don't Prioritize)

`claim_type` (rule-based + Chi²), `priority_level` (perfect rules), `provider_network_status`, `claim_channel`, `deductible_ratio`, `adjustments_count`, `liability_percentage`, `image_quality_score`, `policy_compliance_score`, `household_size`, `income_bracket_index`

### Tier 4 — Low / Drop

`customer_segment` (MI=0.000037), `location_code` (flat bar chart, MI=0.0009), `payment_method` (flat bar chart), `composite_risk_index`, `deductible_amount`, `customer_satisfaction_score`, `depreciation_rate`, `payment_timeliness_score`, `estimated_settlement_amount` (use binary flag only), `underwriting_confidence` (use binary flag only)

---

## 14. Key Visual Findings Summary

1. **`claim_processing_days` histogram is the single most visually informative chart in the EDA.** Class 0 has a needle-thin spike at near-zero while class 1 has a wide, right-shifted KDE. This single feature visually separates the classes better than any other continuous variable.
    
2. **`urban_density_index` is bimodal** with distinct class emphases on each mode — class 0 peaks at low density (~2.5), class 1 at higher density (~9). This is a segmentation opportunity: create a binary split feature.
    
3. **`underwriting_confidence` and `estimated_settlement_amount` are degenerate** — both are flat lines at 0 in both boxplots and violin plots. Only create binary flags for these.
    
4. **`witnesses_count` and `api_response_quality` are functionally identical** (r = 0.998). This is either a data error or intentional duplication. Drop one entirely.
    
5. **`api_response_quality`'s U-shaped distribution** (high mass at both low AND high values) is the most unusual distribution shape in the dataset. Visually both classes are nearly identical in this pattern.
    
6. **`customer_loyalty_score` has a ceiling effect** — almost all values at 17–20 for both classes. The violin shows near-zero spread. This feature adds minimal discriminatory power despite conceptual relevance.
    
7. **The categorical bar chart for `location_code`** (page 53 bottom) shows a completely flat orange pattern across all 90+ codes — virtually no variation in class proportion. This is the clearest visual confirmation of a feature to deprioritize or drop despite its high cardinality.
    
8. **`communication_touchpoints` appears discrete/sparse** from the violin — essentially 0 or 1 for most values, with class 1 having more mass at 1. Treat as binary or ordinal rather than continuous.
    
9. **The MI spike chart** (page 31) visually proves this is a hard problem: one feature towers above a flat baseline, meaning no feature is independently dominant and ensemble methods are essential.
    
10. **`claim_detail_code`'s bar chart** shows extreme within-feature variation — some codes are 100% class 1, others 0% — making it the highest-potential categorical predictor once encoded correctly.
    

---

## 15. Modelling Recommendations

### Primary Architecture

**LightGBM + CatBoost ensemble**, with optional XGBoost as a third blender.

CatBoost is particularly suited here because it handles `claim_detail_code` natively as a categorical without manual target encoding, avoiding leakage risk.

### Complete Preprocessing Pipeline

```python
# Step 1: Hard rules — create rule-based features
df['rule_priority_A'] = (df['priority_level'] == 'A').astype(int)  # → certain class 0
df['rule_priority_KL'] = df['priority_level'].isin(['K', 'L']).astype(int)  # → certain class 1
df['rule_claim_type_A'] = (df['claim_type'] == 'A').astype(int)  # → 96% class 1

# Step 2: Binary flags for degenerate features
df['flag_severity_nonzero'] = (df['severity_index'] > 0).astype(int)
df['flag_underwriting_nonzero'] = (df['underwriting_confidence'] > 0).astype(int)
df['flag_settlement_nonzero'] = (df['estimated_settlement_amount'] > 0).astype(int)
df['flag_customer_value_nonzero'] = (df['customer_value_score'] > 0).astype(int)
df['flag_prior_denial'] = (df['prior_denial_indicator'] > 0).astype(int)

# Step 3: Bimodal segmentation
df['urban_density_group'] = (df['urban_density_index'] > 5).astype(int)

# Step 4: Missing values for categoricals
for col in cat_cols:
    df[col] = df[col].fillna('MISSING')

# Step 5: Drop redundant correlated feature
df.drop(columns=['api_response_quality'], inplace=True)  # Duplicate of witnesses_count

# Step 6: Drop confirmed useless features
drop_cols = ['customer_segment', 'location_code', 'composite_risk_index',
             'deductible_amount', 'customer_satisfaction_score',
             'depreciation_rate', 'payment_timeliness_score',
             'follow_up_responsiveness_score', 'data_completeness_score']
df.drop(columns=drop_cols, inplace=True)

# Step 7: Encode high-cardinality categoricals (with CV to avoid leakage)
# claim_detail_code, coverage_type, location_code (if kept), policy_type
# Use: category_encoders.TargetEncoder with cv=5

# Step 8: Class imbalance
# LightGBM: is_unbalance=True or scale_pos_weight=3.17
# CatBoost: auto_class_weights='Balanced'
```

### Feature Engineering Additions

Based on visual analysis, create these engineered features:

```python
# Interaction: the two most powerful features
df['denial_x_processing'] = df['prior_denial_indicator'] * df['claim_processing_days']

# Ratio interactions
df['premium_to_coverage_ratio'] = df['policy_premium_annual'] / (df['coverage_limit'] + 1)

# Communication efficiency
df['comm_per_processing_day'] = df['communication_touchpoints'] / (df['claim_processing_days'] + 1)

# Claim intensity
df['claim_amount_per_premium'] = df['claim_amount_ratio'] / (df['policy_premium_annual'] + 1)
```

### Evaluation & Submission Strategy

- **Primary metric:** AUC-ROC
- **Validation:** Stratified 5-fold (maintains 76/24 ratio in every fold)
- **Threshold:** Do NOT use 0.5 by default. After training, find optimal threshold on validation set using F1 for class 0 or the competition metric.
- **Ensemble blend:** LightGBM (weight 0.5) + CatBoost (weight 0.5), or tune weights via hill-climbing on held-out validation fold.

### Expected Performance

This is a genuinely hard classification problem. The MI scores are low, distributions overlap heavily, and the clearest visual separator (`claim_processing_days`) still shows substantial within-class variance. Realistic expected AUC-ROC range: **0.82–0.91** for a well-tuned ensemble. The most impactful individual decisions for closing the gap to the top are:

1. Correct encoding of `claim_detail_code` (target encoding with CV)
2. Using the hard rules for `priority_level = A/K/L` and `claim_type = A`
3. Creating the `urban_density_group` binary split feature
4. Combining `prior_denial_indicator` and `claim_processing_days` as interaction terms
5. Ensembling LightGBM + CatBoost rather than single model

---

_Full visual analysis performed across all 55 pages of the EDA PDF._


- **Class weighting** (`class_weight='balanced'` in sklearn) or **oversampling/undersampling** (SMOTE, random undersampling) should be strongly considered.

- **Threshold tuning** post-training can recover additional recall for the minority class without retraining.