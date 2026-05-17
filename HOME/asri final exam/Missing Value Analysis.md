
![](https://i.imgur.com/0kX8xzZ.png)
##### **Column-Level Missing Value**

Every feature is plotted as a horizontal bar showing its non-null count. Two populations are immediately clear:
- **Fully complete group** (~12 columns):bars reaching the full axis width
- **Structural missing block** (~102 columns): bars uniformly cut off at 43–55% of full width, all landing between 43,356 and 51,508 non-null counts
- **Low-missing outlier group** (~7 columns) : bars nearly full but slightly short
![](https://i.imgur.com/Prqe1vF.png)

##### **Top 20 columns by missing count:**
The tight clustering of missing counts (39,990–48,100) across 102 columns is not coincidence. these are the same rows going absent across almost all features simultaneously.
```
119 columns have missing values in total.
```
![](https://i.imgur.com/NyvIWyi.png)


**Column-wise Null Value Distribution**
`policy_status` and `estimated_settlement_amount` dominate both sides with the highest missing counts.
![](https://i.imgur.com/tAVpcQY.png)


###### **Bimodal Row-Level Distribution**

**The bimodal row-wise distribution** (either ~0 missing or ~100 missing per row) is the defining structural characteristic of this dataset. It means observations belong to one of two populations: near-complete rows or near-empty rows. There is almost no middle ground.
Every treatment decision should account for this structure.
![](https://i.imgur.com/mUWh1ms.png)
X-axis = number of missing columns per row (0–100)



#### Is Missingness Predictive? Target Rate Analysis
we analyzed the relationship between missingness intensity and the target variable by grouping observations into quantile-based bins of total missing values. For each group, we computed the target rate along with standard deviation and 95% confidence intervals. This allows us to assess whether missingness intensity carries statistically meaningful predictive information.
![](https://i.imgur.com/TmhQaYG.png)
The results show that the target rate varies across missingness levels, with higher missingness groups generally having a higher target rate (~0.80 vs ~0.74). However, the relationship is not strictly monotonic, indicating a more complex pattern. Overall, this suggests that missingness is not completely random (not MCAR) and contains predictive information about the target, likely reflecting structured mechanisms such as MAR or partially MNAR.

**Lift in Target Rate by Missingness Level**
![](https://i.imgur.com/UlFd8Rd.png)

The lift analysis shows that missingness intensity has a non-linear relationship with the target variable. While some intermediate missingness levels show near-neutral or slightly negative lift, both low and very high missingness groups exhibit deviations from the global baseline. In particular, the highest missingness group shows the strongest positive lift, indicating elevated risk. This confirms that missingness carries structured predictive information, although its effect is not monotonic.



> [!warning] **Critical**: **Never Drop the 90+ Missing Block**
> dropping high-missing rows would not necessarily improve modeling, because they still represent a large portion of the dataset and contain **structured information rather than pure noise**. However, the evidence here does **not strongly support the idea that they are a completely separate data feed**; instead, it suggests a **partially informative missingness mechanism (likely MAR with localized MNAR behavior)** rather than a fully distinct system.





###### **Missingness Pattern as a Standalone Predictor**

A logistic regression model was trained on **only the binary missingness matrix** (no actual feature values — just which columns are absent per row), evaluated with 5-fold stratified cross-validation:
```python
X_miss = X_train.isna().astype(int).values
y = y_train.values.ravel()
```

![](https://i.imgur.com/jtUW0lb.png)
**AUC of 0.60 from zero feature values** confirms systematic MNAR behaviour. A random model gives 0.50; reaching 0.60 purely from absence patterns means the pattern of which values are missing carries substantial information about claim outcomes. **The model can and should exploit this structure explicitly.**




##### **Missingness Correlation Heatmap (top 20 highest-missing columns)**

![](https://i.imgur.com/fjiK7Hg.png)
- **`policy_status` and `policy_type` vs everything else: correlations of 0.3 and −0.1 respectively.** These two columns have a _different_ missingness pattern from the bulk. When `policy_type` is missing, the other columns are slightly _less_ likely to be missing — and vice versa. This is why they form their own clusters (Clusters 1 and 2) in the dendrogram.
- **(correlation = 1.0):** The columns in the lower-right of the heatmap: `months_since_last_claim`, `claim_approval_probability`, `duplicate_claim_similarity`, `data_source_reliability`, `processing_queue_priority`... show perfect or near-perfect co-missingness. When any one of them is missing, all the others are missing too. These form the core of Cluster 4 in the hierarchical clustering.

The `<1` entries in the matrix denote correlations between 0 and 1 (i.e., near-perfect but not exactly 1). The correlation structure directly motivates the clustering approach.
##### **Hierarchical Clustering of Missing Patterns**

**Hierarchical clustering** of the columns based on how similar their missing-value patterns are
![](https://i.imgur.com/jBgtYvF.png)
**Branch A (upper):** Low-missing and fully complete columns. Contains `policy_type`, `coverage_type`, `claim_type`, `geographic_region`, `previous_claims_value`, `claim_detail_code`, `claim_source`, `policy_premium_annual`, `incurred_loss_estimate`, `claim_processing_days`, `policy_renewal_months`, `prior_denial_indicator`, `location_code`, `cross_sell_index`, all 12 fully complete columns, and `policy_status`. `policy_status` joins this branch rather than Branch B despite its high missingness. confirming its unique missingness pattern (seen in the correlation heatmap as the −0.1 correlation with other Tier 1 columns).

**Branch B (lower):** The structural missing block. This branch itself sub-divides into two sub-branches visible in the dendrogram: a tighter cluster (higher within-group similarity, near-zero linkage distance) containing Cluster 5 and parts of Cluster 4, and a broader cluster containing the rest of Cluster 4 and Cluster 3. The sub-structure within Branch B is what the cluster-based feature engineering exploits.


#### **cluster-based  engineered features**

We first identified variables containing significant missing values and transformed their missingness into binary indicators (1 = missing, 0 = observed). Using these binary missingness patterns, Hierarchical Agglomerative Clustering with Jaccard distance and average linkage was applied to group variables exhibiting similar missing-data behavior. For each resulting cluster, new features were engineered to summarize the missingness structure, including the proportion of missing values, the number of missing variables, and indicators showing whether all variables within a cluster were missing for a given observation. Finally, a baseline Logistic Regression model using only the original numerical variables was compared with an enhanced model incorporating the cluster-based missingness features. Model performance was evaluated using the ROC-AUC metric to assess whether structured missingness information improves predictive capability.

![](https://i.imgur.com/5NrFfBA.png)
the missingness-cluster features improved AUC from about **0.7038 to 0.7089**, which is a gain of roughly **+0.0051 AUC**. This again confirms that missingness in the dataset is not random and that groups of features tend to become missing together in ways related to the target.

***Permutation Importance: Top 20 Features:***
Permutation importance analysis shows that cluster-based missingness features contribute measurable predictive value, with certain clusters (notably Cluster 3 and Cluster 2) ranking among the most important features in the model. However, their predictive contribution is secondary compared to core financial and claim-related variables.
![](https://i.imgur.com/udy3aex.png)



#### **Treatment Strategy**


**Structural Block (40–52% missing) - 102 columns**
1. Add `has_full_data` global binary flag (threshold: >30% missing cols = 0)
2. Add `{col}_missing` binary indicators for top-correlated Tier 1 columns
3. Add 15 cluster summary features (5 clusters × 3 features each: count, proportion, all-missing)
4. Median-impute all Tier 1 numeric columns (using train-set medians)
5. Mode-impute `policy_status` and `policy_type` (using train-set modes)

**Moderate Categorical Missingness (3–6%) - 3 columns**
Fill with `"Unknown"`.

**Low Numeric Missingness (<1%) - 11 columns**
Median imputation for numeric or mode for categorical. No indicator added.



**Tier 1 — Structural block missingness (~43–52%) (100 cols)**
→ Add missingness indicator + median/mode impute
This is the critical finding: ~6,400 rows are missing 90–109 columns simultaneously, and ~6,400 rows are near-complete. This is **not random** — it's almost certainly a structural split (e.g. two data feeds, claim types, or underwriting channels). The missingness itself is a signal. Add a binary `has_full_data` flag and individual `_missing` indicators for the most predictive columns. Then impute the numeric values with median and categoricals with mode — not because the imputed values are accurate, but because tree-based models need something there. XGBoost/LightGBM will learn to ignore the imputed values once the indicator tells them the data was absent.

**Tier 2 — Moderate categorical missingness (3–6%)** (2 cols)
→ Add "Unknown" category
For `coverage_type` (5.9%) and `claim_type` + `geographic_region` (2.9%), missing categories often have meaning — e.g. a missing coverage_type might indicate a non-standard policy. Add an explicit "Unknown" category rather than imputing the mode. This avoids polluting real categories with noise and lets the model learn from the pattern of missingness directly. Never do target encoding before creating this category.


**Tier 3 — Low numeric missingness (<1%)** (10 cols)
→ Simple median impute, no indicator needed
These are genuinely near-complete columns. The missingness is probably just noise — dropped records, occasional system errors. Median imputation is fine. Adding indicators at this rate would just add useless noise columns. Drop-and-impute is equivalent; median wins because it's simpler and keeps row count intact.

**Tier 4 — Single-missing categoricals (<0.01%)** (3 cols)
`payment_method`, `adjuster_recommendation`, `automation_eligible` each have exactly 1 missing row out of 14,767. Either drop those rows (negligible loss) or mode-impute. Both are fine. Don't overthink it.

**Special: Leakage-risk columns**
Several columns look like downstream outputs that may encode target information: `fraud_probability_score`, `anti_fraud_score`, `claim_approval_probability`, `final_decision_confidence`, `adjuster_recommendation`, `settlement_confidence_score`. If these are computed after the claim outcome is known, imputing them (or using them at all) will inflate your AUC dramatically — and produce a model that fails in production. Verify their temporal relationship to `target` before including them.



**Best model choice for this pattern:** LightGBM or XGBoost with native NaN handling. Both can split on "NaN vs not-NaN" internally without you adding indicators manually — but adding explicit indicators still usually helps AUC by 1–3 points.

**What will hurt AUC:** Dropping the 6,400 incomplete rows; using KNN or iterative imputer on 43%-missing columns (extremely slow and no benefit over median for tree models); doing MissForest across 100 columns (same story).



Here's the full picture from analyzing your actual data:

**The single most important finding:** your missingness is not random. There's a hard structural split — about 6,400 rows are missing 90–109 columns simultaneously, while the rest are nearly complete. This almost certainly means two different data sources, claim channels, or process stages are feeding into this table. The good news: those "incomplete" rows have essentially the same target rate (77%) as the complete ones, so they carry real signal. **Do not drop them.**

**The practical strategy by tier:**

For the big Tier 1 block (~43–52% missing), the right approach is: add a `has_full_data` binary flag + individual `_is_missing` indicators for the most predictive columns, then median/mode impute the actual values. The imputed values won't be accurate, but tree models will learn to ignore them once the indicator tells them the data was absent. Don't use KNN or iterative imputer here — with 100 columns at 43% missing, it's extremely slow and gains you nothing over median for gradient boosting.

For the Tier 2 categoricals (`coverage_type`, `claim_type`, `geographic_region`), add an explicit `"Unknown"` level rather than imputing the mode. The missingness may itself be meaningful.

**The leakage risk is your real danger**, not the missing values. Columns like `fraud_probability_score`, `anti_fraud_score`, `claim_approval_probability`, `final_decision_confidence`, and `adjuster_recommendation` look like they were computed after the claim outcome was determined. If so, they'll inflate your AUC in training and collapse in production. Audit those first.

**Best model for this pattern:** LightGBM with `use_missing=True` (the default). It handles NaN splits natively, so even without indicators you get decent behavior — but adding explicit `_is_missing` flags typically squeezes out another 1–3 AUC points.




![](https://i.imgur.com/DEkyBgy.png)
![](https://i.imgur.com/UWI1wKW.png)
![](https://i.imgur.com/WQgU6OH.png)
![](https://i.imgur.com/gzeDRLi.png)
![](https://i.imgur.com/XH12rCQ.png)
![](https://i.imgur.com/0lU67rQ.png)
![](https://i.imgur.com/HYvuSwU.png)




after training performance droped 

Training final model on full training data... ---------------- Train Set -------------------- Metric Value 0 ROC AUC 0.923133 1 Log Loss 0.438079 2 Accuracy 0.791750 The best mean CV score across all trials : 0.768431052059962 OOF auc: 0.76843 --- Test set performance --- Test ROC AUC: 0.77133 Test Log Loss: 0.55702 Test Accuracy: 0.68872

also 

Bottom 10% Features (13 total):
                       feature  importance
71        recovery_probability   -0.009235
109    provider_network_status   -0.001144
61   communication_touchpoints   -0.000509
30           geographic_region   -0.000138
9        claim_processing_days   -0.000040
131              has_full_data    0.000008
66   coverage_utilization_rate    0.000011
82         regional_cost_index    0.000013
4        premium_payment_score    0.000014
2                   claim_type    0.000014
90     adjuster_recommendation    0.000025
63           adjustments_count    0.000031
80         zip_code_risk_score    0.000036


# training lgbm
![](https://i.imgur.com/Tp240Gy.png)
![](https://i.imgur.com/iOokU9J.png)
![](https://i.imgur.com/9buanXJ.png)


![](https://i.imgur.com/AsLK19e.png)


**catboost imputation**
CatBoost approach:  learns a **supervised model per column** and uses all other features to predict missing values. we tested catboost rather then knn based on the previous results of non-linear relationships in missing values and this method captures **complex patterns (non-linear)** add to that it works for high-cardinality features and also it's supervised so its smarter than distance averagin.
![](https://i.imgur.com/hfzXndB.png)
It seems like it didn’t improve performance, even though I had high expectations for this imputation method. It might be worth investigating further to understand why it didn’t work.