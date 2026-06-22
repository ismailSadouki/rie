
---

## Table of Contents

1. [Dataset & Problem Context (from EDA)](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#1-dataset--problem-context-from-eda)
2. [Global Metrics on Test Set](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#2-global-metrics-on-test-set)
3. [Confusion Matrix](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#3-confusion-matrix)
4. [Class-wise Error Summary](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#4-class-wise-error-summary)
5. [Probability & Uncertainty Distributions](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#5-probability--uncertainty-distributions)
6. [ROC / PR / Threshold Analysis](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#6-roc--pr--threshold-analysis)
7. [Calibration Analysis](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#7-calibration-analysis)
8. [Gains & Lift](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#8-gains--lift)
9. [Feature-level Error Rate](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#9-feature-level-error-rate)
10. [Top Worst Errors](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#10-top-worst-errors)
11. [Error Clustering (PCA + K-Means)](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#11-error-clustering-pca--k-means)
12. [SHAP Analysis on Errors](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#12-shap-analysis-on-errors)
13. [Statistical Separation: FP vs FN (Mann-Whitney U)](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#13-statistical-separation-fp-vs-fn-mann-whitney-u)
14. [Sanity Checks](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#14-sanity-checks)
15. [EDA vs. Test Error: Alignment & Discrepancies](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#15-eda-vs-test-error-alignment--discrepancies)
16. [Final Summary & Prioritised Recommendations](https://claude.ai/chat/5cce9f79-0987-4727-9fda-206b25753e61#16-final-summary--prioritised-recommendations)




## Global Metrics on Test Set

| Metric           | Value  | Interpretation                                                           |
| ---------------- | ------ | ------------------------------------------------------------------------ |
| ROC-AUC          | 0.7755 | Moderate discrimination; below the 0.80 target flagged in diagnostics    |
| PR-AUC           | 0.9140 | High, but inflated by 76% class-1 prevalence (no-skill baseline = 0.761) |
| Brier Score      | 0.1778 | Reasonable sharpness; calibration improvement would lower this           |
| F1  Class 0      | 0.5253 | Weak; minority class is heavily underfit                                 |
| F1  Class 1      | 0.7872 | Acceptable for the majority class                                        |
| F1  Macro        | 0.6563 | Below par; gap between classes is 0.262                                  |
| Overall Accuracy | 0.7062 | Misleading given imbalance; always-class-1 baseline = 76.1%              |

**Per-class classification :**

|Class|Precision|Recall|F1|Support|
|---|---|---|---|---|
|0 (minority)|0.4276|0.6808|0.5253|8,190|
|1 (majority)|0.8770|0.7141|0.7872|26,107|
|Macro avg|0.6523|0.6975|0.6563|34,297|
|Weighted avg|0.7697|0.7062|0.7247|34,297|

The ROC-AUC of 0.7755 is consistent with the EDA's finding that the strongest single feature has AUC = 0.687. Combining 131 features brings discriminability to 0.7755  a meaningful improvement but still below 0.80, consistent with the inherent difficulty of this dataset.

---

##  Confusion Matrix

![](https://i.imgur.com/CKkCvgL.png)


The model correctly identifies 68.1% of class-0 cases and 71.4% of class-1 cases. The 31.9% miss-rate on the minority class directly reflects the EDA's predicted consequence of the 3.17:1 imbalance: the model's prior is biased toward class 1, causing it to systematically under-predict class 0.

False Positives (2,614) are cases where the model predicts approval but the true label is denial. False Negatives (7,463) are legitimate approvals the model fails to recognise, nearly three times more numerous than FPs, and operationally the more critical failure.

---

## Class-wise Error Summary
![](https://i.imgur.com/hfWQ1GC.png)

- Class 0 error rate 0.319: nearly 1 in 3 minority-class test cases misclassified
- Class 1 error rate 0.286: more than 1 in 4 majority-class test cases misclassified
- Class 1: FN (7,463) >> FP (2,614): strong recall problem.


Average confidence on class-0 errors is 0.6589, the model makes wrong predictions about class-0 cases with moderate confidence, not just at the boundary. This points to systematic feature-level confusion, not just threshold misplacement. 
The EDA identified that `claim_channel = C` gives 85.6% class-1 rate; when the model encounters class-0 cases with channel C characteristics, it confidently predicts class 1  producing these high-confidence FPs.

For class-1 errors, lower confidence (0.3934) and higher uncertainty (0.2133) suggest genuine ambiguity in the test cases that are missed the model hesitates but still gets the label wrong.

---

## Probability & Uncertainty Distributions

![](https://i.imgur.com/acfHhnn.png)

**Score Distribution by True Class**


The predicted probability distributions for the two classes overlap heavily in the 0.25–0.65 range on the test set. Class 0 is centred around 0.3–0.4 and class 1 around 0.5–0.7. The threshold at 0.52 cuts through the overlap zone, directly generating errors in both classes. This pattern is entirely consistent with the EDA finding that no single feature achieves AUC > 0.70  the model cannot achieve clean separation because the underlying features do not provide it.

**Score Distribution: Correct vs. Error**

Correctly classified test samples cluster away from the 0.52 threshold (confident predictions in either direction). Error samples concentrate in the 0.3–0.6 range near the threshold. However, the error distribution extends across the full probability range, confirming some high-confidence mistakes exist.

 **FP vs FN Probability Density (KS = 1.000, p = 0.000)**

A KS statistic of 1.000 confirms the predicted probability distributions of FP and FN test cases are perfectly statistically distinguishable. FPs cluster at higher predicted probabilities (0.5–0.7 range the model "sees" these class-0 cases as clearly class 1). FNs cluster at lower probabilities (0.1–0.4 the model assigns low class-1 probability to true positives).



**Uncertainty Score**

Errors on the test set have lower uncertainty scores (|prob − 0.5| closer to 0) than correct predictions, confirming the model is most uncertain near its decision boundary  where most errors occur. The residual high-confidence errors (FPs near 0.97, FNs near 0.014) represent a different failure mode requiring feature-level rather than threshold-level correction.

---

## ROC / PR / Threshold Analysis

### ROC Curve

AUC = 0.7755 on test data. The Youden J optimal threshold from the ROC curve is 0.552. The curve bows moderately above the diagonal — the model adds value over random but leaves significant room for improvement.

### Precision-Recall Curve
![](https://i.imgur.com/FY8jony.png)
![](https://i.imgur.com/OhdCMiL.png)

PR-AUC = 0.9140 vs. baseline 0.761. The best F1 threshold identified on the PR curve is **0.247**  substantially lower than the operating threshold of 0.52. This means the model's operating threshold is configured roughly 2× higher than the optimal F1 point on the PR curve.

### Metrics vs. Threshold Sweep
![](https://i.imgur.com/nPeJRNp.png)

- F1-class-1 peaks near 0.20–0.30 and declines as threshold rises
- F1-class-0 is low throughout but rises with the threshold
- Recall-class-0 collapses steeply as threshold rises above 0.30
- The current threshold of 0.52 sits on the declining slope of the macro-F1 curve



---

## Calibration Analysis
![](https://i.imgur.com/0DXlT3l.png)




try **Platt scaling** (logistic regression calibration) or **isotonic regression** on held-out validation predictions. After recalibration, the threshold should be re-optimised from the PR curve.

---

## 8. Gains & Lift
![](https://i.imgur.com/m4xkQ0J.png)
The modest gains  reflect the fundamental data constraint identified : _"No single feature has AUC > 0.70. The signal is distributed and non-linear."_ Strong top-decile lift requires features that sharply separate a concentrated group of positives but here the signal is diffuse across 131 features with no dominant predictor.
![](https://i.imgur.com/1FgATaN.png)
The model adds predictive value over random selection for approximately the top 70% of the scored test population.






---

## 9. Feature-level Error Rate

![](https://i.imgur.com/MUspxte.png)

**No single feature shows monotonically worsening error**, confirming the EDA's prediction that signal is distributed and non-linear. The U-shaped error pattern for `previous_claims_value` is a textbook confirmation of the non-monotone CDF relationship identified in training data.

---

## Top Worst Errors

**Worst False Positives** 
![](https://i.imgur.com/jO3158A.png)

These class-0 test cases receive near-maximum predicted probabilities (~0.97). The model treats them as almost certainly class 1. Given the EDA finding that `claim_channel = C` produces 85.6% class-1 rate and `priority_level = A` is a 100% class-0 rule, these extreme FPs likely have feature combinations that superficially resemble class 1 (e.g., high `coverage_limit`, high `prior_denial_indicator`, channel C) while actually belonging to class 0 through a combination the model never encountered in training. These cannot be fixed by threshold tuning.

**Worst False Negatives**
![](https://i.imgur.com/ex6tj9y.png)

These true class-1 test cases receive predicted probabilities below 0.05. The model is nearly certain they are class 0. These represent a complete model blind spot. likely tied to `prior_denial_indicator = 0` combined with low `claim_processing_days`, which the EDA showed is the class-0 profile. The model has learned this combination means class 0, but these particular cases are class-1 exceptions .

---

## Error Clustering 

![](https://i.imgur.com/M7sRWq6.png)
![](https://i.imgur.com/4IBw8YO.png)


**FN-dominant clusters (0, 1, 4)** The three clusters differ in PCA position but share the same FN-heavy character, confirming that recall failure is distributed across multiple feature-space regions consistent with the EDA's finding that no single feature cleanly separates classes.

**FP-dominant clusters (2, 3)** Cluster 2 (n=285, 75.8% FP) is compact enough to be targeted with a post-processing rule. Given the EDA's identification of `priority_level = A` as a 100% class-0 rule and `claim_channel = B` as class-0-prone, these FP clusters likely contain class-0 cases with superficially class-1 feature profiles that a simple rule could correct.

PC1 explains 68.7% of variance  a single dominant direction. Given the EDA's finding that `prior_denial_indicator` (F=5,742) overwhelmingly dominates all other features, PC1 likely captures the `prior_denial_indicator` axis. The PCA scatter plot with FP/FN colouring confirms these error types are partially separable in this reduced space.

---

## SHAP Analysis on Errors




![](https://i.imgur.com/lOJZgBn.png)

### SHAP: FP vs FN
![](https://i.imgur.com/0ea5i5U.png)
![](https://i.imgur.com/V9FrlTH.png)

The top-4 SHAP features are identical for both FP and FN test errors: `claim_channel`, `prior_denial_indicator`, `prior_denial_indicator_capped`, `coverage_type`. **the same features drive the model to make opposite mistakes**. The model is not failing on FPs because of different features than FNs, it is the same feature signals being misinterpreted in different directions.

 `denial_x_processingv2` is higher in FNs (rank 6), suggesting the interaction of `prior_denial_indicator` and `claim_processing_days` specifically confuses true positives. `policy_status` and `provider_network_status` are more prominent in FNs.

### EDA vs. SHAP Alignment

| EDA Prediction                                               | SHAP Confirmation                                                                                                                |
| ------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| `claim_detail_code` is #1 categorical predictor (MI=0.115)   | Rank 8 in SHAP. lower than expected, likely due to SHAP value splitting across `coverage_type` and other correlated categoricals |
| `prior_denial_indicator` is #1 numerical predictor (F=5,742) | Rank 2 in SHAP. confirmed, but split with `prior_denial_indicator_capped` (rank 3)                                               |
| `claim_channel` highly significant (Chi²=2,132)              | Rank 1 in SHAP, **outperforms EDA ranking**, suggesting its categorical signal is especially concentrated in error cases         |
|                                                              |                                                                                                                                  |
| `risk_category` — MI=0.001, very low                         |  elevated in errors vs. EDA expectation; warrants investigation                                                                  |

---

## Statistical Separation: FP vs FN (Mann-Whitney U)

Mann-Whitney U test comparing the distributions of each numerical feature between the 2,614 FP and 7,463 FN test cases.
![](https://i.imgur.com/K4LH92D.png)


---

## Sanity Checks

| Check                            | Value                            | Status                                           |
| -------------------------------- | -------------------------------- | ------------------------------------------------ |
| Near-threshold (±0.1) error rate | 0.459 (n=10,088 test cases)      | Near coin-flip in the high-uncertainty zone      |
| Class-0 error rate               | 0.3192, imbalance impact = 1.337 | Confirmed imbalance effect on test set           |
| Feature pairs with r > 0.97      | 3,416 pairs                      | Matches EDA finding; SHAP attribution unreliable |
The model's decision boundary is wide and poorly defined around the threshold on the test set. This is consistent with the EDA's finding of distributed, non-linear signal: no feature sharply resolves class membership in the boundary zone.


---

## EDA vs. Test Error:

This section explicitly cross-references what the EDA predicted and what the test-set error analysis found.

| EDA Prediction                                                          | Test-Set Confirmation                                                               |
| ----------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| Class imbalance will cause systematic class-0 under-prediction          | Class-0 error rate 31.9%, imbalance impact 1.337                                    |
| `prior_denial_indicator` is the dominant predictor                      | Rank 2 SHAP, significant FP/FN separation                                           |
| `claim_processing_days` is 2nd strongest; clearest histogram separation | Significant FP/FN separator (p=0.000); mid-decile error peaks                       |
| `coverage_limit` threshold ~12.5 separates classes                      | FPs have higher coverage limits (median 12.016 vs 11.636)                           |
| `previous_claims_value` has U-shaped relationship                       | U-shaped error rate pattern confirmed on test deciles                               |
| `policy_renewal_months` non-monotone (spike at 7)                       | Non-monotone error pattern confirmed                                                |
| `recovery_prob_is_one` is a useful engineered feature                   | Rank 7 in SHAP on test errors                                                       |
| `denial_x_processingv2` interaction is valuable                         | Rank 6 in SHAP on test errors                                                       |
| 3,416 correlated feature pairs cause SHAP instability                   | Confirmed in sanity checks; `prior_denial_indicator` SHAP split with capped version |
| CatBoost is the right model for `claim_detail_code`                     | Model achieves ROC-AUC 0.7755 competitive given dataset difficulty                  |

---

## Final Summary 

### Error Summary 

| Item                         | Value           |
| ---------------------------- | --------------- |
| Model                        | CatBoost        |
| Data                         | Test set        |
| Threshold                    | 0.52            |
| Total test samples evaluated | 34,297          |
| Total errors                 | 10,077 (29.38%) |
| False Positives              | 2,614           |
| False Negatives              | 7,463           |
| ROC-AUC                      | 0.7755          |
| PR-AUC                       | 0.9140          |
| Brier Score                  | 0.1778          |
| F1 Macro                     | 0.6563          |
| F1 Class-0                   | 0.5253          |
| F1 Class-1                   | 0.7872          |


###  Recommendations

**1. Recalibrate predicted probabilities** Apply Platt scaling or isotonic regression on held-out validation set probabilities. The +0.1491 under-confidence bias directly causes the FN surplus of 7,463 test cases. Recalibration alone without any model changes would shift many FN cases above the threshold.

**2. Re-optimise the decision threshold** After recalibration, sweep the threshold using a business-cost-weighted criterion. The PR curve indicates 0.247 as the F1-optimal point, but the business may prefer a different FN/FP tradeoff. The current threshold of 0.52 is misaligned with the model's probability scale.

**5. Deduplicate correlated features ** The EDA identified and the sanity check confirmed 3,416 feature pairs with r > 0.97. Specifically: drop `api_response_quality` (r=0.998 with `witnesses_count`), drop one of `claim_complexity_score`/`liability_percentage` (r=0.993), review `medical_necessity_score`/`claim_validity_score` pair (r=0.982). This will stabilise SHAP importance and may modestly improve generalisation.

**6. Investigate feature distribution shift** Three features showed unexpected test-set behaviour vs. EDA: `risk_category` (low MI in training, rank 9 in test-error SHAP), `payment_method` (flat in training, rank 13 in test SHAP), `medical_necessity_score` (non-significant in training, p=0.030 in test). Run a KS test or PSI (Population Stability Index) between training and test distributions for these features to detect shift.

**7. Target FP-dominant clusters 2 and 3 with post-processing** Error cluster 2 (n=285, 75.8% FP) is compact enough to be targeted. Identify the feature profile of this cluster and apply a correction rule.

**9. Investigate high-confidence errors** The worst FPs (predicted probability ~0.97, true class 0) and worst FNs (predicted probability ~0.015, true class 1) cannot be fixed by threshold changes or recalibration. Manual inspection of these test cases  especially their `claim_detail_code` and `claim_channel` values  is needed to identify whether they represent data quality issues, label errors, or systematic model blind spots.
  