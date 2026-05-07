
![](https://i.imgur.com/0kX8xzZ.png)
![](https://i.imgur.com/Prqe1vF.png)
![](https://i.imgur.com/fjiK7Hg.png)
There are 119 columns that have missing values.
![](https://i.imgur.com/NyvIWyi.png)
![](https://i.imgur.com/tAVpcQY.png)


**The bimodal distribution** (either ~0 missing or ~100 missing per row) is the defining characteristic of this dataset. Every treatment decision should account for this structure.
![](https://i.imgur.com/mUWh1ms.png)


**Target Rate vs Missingness Level**
I analyzed the relationship between missingness intensity and the target variable by grouping observations into quantile-based bins of total missing values. For each group, we computed the target rate along with standard deviation and 95% confidence intervals. This allows us to assess whether missingness intensity carries statistically meaningful predictive information.
![](https://i.imgur.com/TmhQaYG.png)
The results show that the target rate varies across missingness levels, with higher missingness groups generally having a higher target rate (~0.80 vs ~0.74). However, the relationship is not strictly monotonic, indicating a more complex pattern. Overall, this suggests that missingness is not completely random (not MCAR) and contains predictive information about the target, likely reflecting structured mechanisms such as MAR or partially MNAR.

![](https://i.imgur.com/UlFd8Rd.png)
The lift analysis shows that missingness intensity has a non-linear relationship with the target variable. While some intermediate missingness levels show near-neutral or slightly negative lift, both low and very high missingness groups exhibit deviations from the global baseline. In particular, the highest missingness group shows the strongest positive lift, indicating elevated risk. This confirms that missingness carries structured predictive information, although its effect is not monotonic.

> [!warning] **Critical**: **Never Drop the 90+ Missing Block**
> dropping high-missing rows would not necessarily improve modeling, because they still represent a large portion of the dataset and contain **structured information rather than pure noise**. However, the evidence here does **not strongly support the idea that they are a completely separate data feed**; instead, it suggests a **partially informative missingness mechanism (likely MAR with localized MNAR behavior)** rather than a fully distinct system.






**is there a signal in the _pattern of missing values_ with respect to the target?**

we train a logistic regression model using only these missingness patterns to predict the target.
```python
X_miss = X_train.isna().astype(int).values
y = y_train.values.ravel()
```
and evaluated it with 5-fold stratified cross-validation using ROC-AUC to check whether the structure of missing data alone carries any consistent predictive signal (meaning whether missing values are random noise or actually correlated with the target).
![](https://i.imgur.com/jtUW0lb.png)
so the way values are missing contains some relationship with the target.
The dataset has **systematic missingness (likely MNAR)** **(Missing Not At Random)**, and the model can exploit it.




**Hierarchical clustering** of the columns based on how similar their missing-value patterns are
![](https://i.imgur.com/jBgtYvF.png)


**Clustering engineered features**

We first identified variables containing significant missing values and transformed their missingness into binary indicators (1 = missing, 0 = observed). Using these binary missingness patterns, Hierarchical Agglomerative Clustering with Jaccard distance and average linkage was applied to group variables exhibiting similar missing-data behavior. For each resulting cluster, new features were engineered to summarize the missingness structure, including the proportion of missing values, the number of missing variables, and indicators showing whether all variables within a cluster were missing for a given observation. Finally, a baseline Logistic Regression model using only the original numerical variables was compared with an enhanced model incorporating the cluster-based missingness features. Model performance was evaluated using the ROC-AUC metric to assess whether structured missingness information improves predictive capability.

![](https://i.imgur.com/5NrFfBA.png)
the missingness-cluster features improved AUC from about **0.7038 to 0.7089**, which is a gain of roughly **+0.0051 AUC**. This again confirms that missingness in the dataset is not random and that groups of features tend to become missing together in ways related to the target.


**Permutation importance** analysis shows that cluster-based missingness features contribute measurable predictive value, with certain clusters (notably Cluster 3 and Cluster 2) ranking among the most important features in the model. However, their predictive contribution is secondary compared to core financial and claim-related variables.
![](https://i.imgur.com/udy3aex.png)



**Treatment**

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