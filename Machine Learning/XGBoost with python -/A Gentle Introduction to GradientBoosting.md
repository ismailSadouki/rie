### 2.5 Improvements to Basic Gradient Boosting
Gradient boosting is a greedy algorithm and can overfit a training dataset quickly.It can benefit from regularization methods that penalize various parts of the algorithm and generally improve the performance of the algorithm by reducing overfitting.

**In this this section we will look at 4 enhancements to basic gradient boosting:**
##### 2.5.1 Tree Constraints
It is important that the weak learners have skill but remain weak.Below are some constraints that can be imposed on the construction of decision trees:
- Number of trees, generally adding more trees to the model can be very slow to overfit. The advice is to keep adding trees until no further improvement is observed.
- Tree depth, deeper trees are more complex trees and shorter trees are preferred. Generally, better results are seen with 4-8 levels.
- Number of nodes or number of leaves, like depth, this can constrain the size of the tree, but is not constrained to a symmetrical structure if other constraints are used.
- Number of observations per split imposes a minimum constraint on the amount of training data at a training node before a split can be considered
- Minimum improvement to loss is a constraint on the improvement of any split added to a tree.
##### 2.5.2 Weighted Updates
The predictions of each tree are added together sequentially. The contribution of each tree to this sum can be weighted to slow down the learning by the algorithm. This weighting is called a shrinkage or a learning rate.

Similar to a learning rate in stochastic optimization, shrinkage reduces the influence of each individual tree and leaves space for future trees to improve the model.


##### 2.5.3Stochastic Gradient Boosting
at each iteration a subsample of the training data is drawn at random (without
replacement) from the full training dataset. The randomly selected subsample is
then used, instead of the full sample, to fit the base learner.
A few variants of stochastic boosting that can be used:
 Subsample rows before creating each tree.
 Subsample columns before creating each tree
 Subsample columns before considering each split.
Generally, aggressive sub-sampling such as selecting only 50% of the data has shown to be beneficial.

##### 2.5.4 Penalized Gradient Boosting
the leaf weight values of the trees can be regularized using popular regularization functions, such as:
 L1 regularization of weights.
 L2 regularization of weights.
The additional regularization term helps to smooth the final learnt weights to
avoid over-fitting. Intuitively, the regularized objective will tend to select a model
	employing simple and predictive functions.

---
---

# Data Preparation for Gradient Boosting
XGBoost models represent all problems as a regression predictive modeling problem that only takes numerical values as input.

it requires that the output variables be numeric.



|**Concept**|**Explanation**|
|---|---|
|**Binary Classification in XGBoost**|XGBoost expects class labels as **0 and 1** (not 1 and 2).|
|**Why Convert?**|XGBoost uses a logistic function, which models probabilities between 0 and 1.|
|**How to Convert?**|Use `LabelEncoder()` to map **1 → 0** and **2 → 1** automatically.|

# Working with missing data
##### **XGBoost Treats NaN Smartly Instead of Assuming It Has Meaning**

- When missing values are marked as `0`, XGBoost **treats 0 as real data**, which may lead to incorrect splits.
    
- If they are `NaN`, XGBoost **learns the best way to handle missing values** during training.
###### **Empirical Observation: Higher Accuracy When Using NaN**

✅ In many cases, using `NaN` in XGBoost results in **higher accuracy** because:

- The model finds the best way to handle missing values.
    
- It avoids incorrect assumptions about missing data.
    
- It prevents unnecessary distortions in feature distributions.

<mark>It is a good lesson to try both approaches (automatic handling and imputing) on your data when you have missing values.</mark>



# How to Evaluate XGBoost Models


If you have many classes for a classification type predictive modeling problem or the classes are imbalanced (there are a lot more instances for one class than another), it can be a good idea to create stratified folds when performing cross-validation. This has the effect of enforcing the same distribution of classes in each fold as in the whole training dataset when performing the crossvalidation evaluation. The scikit-learn library provides this capability in the StratifiedKFold
class.

### What Techniques to Use When
- Generally k-fold cross-validation is the gold-standard for evaluating the performance of a machine learning algorithm on unseen data with k set to 3, 5, or 10.
- Use stratified cross-validation to enforce class distributions when there are a large number of classes or an imbalance in instances for each class.
- Using a train/test split is good for speed when using a slow algorithm and produces performance estimates with lower bias when using large datasets.
If in doubt, use 10-fold cross-validation for regression problems and stratified 10-fold cross-validation on classification problems.