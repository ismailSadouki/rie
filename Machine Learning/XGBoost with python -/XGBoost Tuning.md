# How to Configure the Gradient Boosting Algorithm
## Configuration Advice from Primary Sources

the trade-off between the number of trees (M ) and the learning rate (v):
	The v-M trade-off is clearly evident; smaller values of v give rise to larger optimal	M -values. They also provide higher accuracy, with a diminishing return for v < 0.125.	The misclassification error rate is very flat for M > 200, so that optimal M-values 	for it are unstable. [...] the qualitative nature of these results is fairly universal.
He suggests to first set a large value for the number of trees, then tune the shrinkage parameter to achieve the best results. Studies in the paper preferred a shrinkage value of 0.1, a number of trees in the range 100 to 500 and the number of terminal nodes in a tree between 2 and 8.
	The “shrinkage” parameter 0 < v < 1 controls the learning rate of the procedure. Empirically [...], it was found that small values (v ≤ 0.1) lead to much better generalization error.
	
		the best value of the sampling fraction [...] is approximately 40% (f = 0.4) [...] However, sampling only 30% or even 20% of the data at each iteration gives considerable improvement over no sampling at all, with a corresponding computational speed-up by factors of 3 and 5 respectively.


He also studied the effect of the number of terminal nodes in trees finding values like 3 and 6 better than larger values like 11, 21 and 41.

	In both cases the optimal tree size as averaged over 100 targets is L = 6. Increasing the capacity of the base learner by using larger trees degrades performance through “over-fitting”.

In his talk titled Gradient Boosting Machine Learning at H2O1 , Trevor Hastie made the comment that in general gradient boosting performs better than random forest, which in turn performs better than individual decision trees.
	Gradient Boosting > Random Forest > Bagging > Single Trees


They comment that a good value the number of nodes in the tree (J) is about 6, with generally good values in the range of 4-to-8.
	Although in many applications J = 2 will be insufficient, it is unlikely that J > 10 will be required. Experience so far indicates that 4 ≤ J ≤ 8 works well in the context of boosting, with results being fairly insensitive to particular choices in this range.

They suggest monitoring the performance on a validation dataset in order to calibrate the number of trees and to use an early stopping procedure once performance on the validation dataset begins to degrade. As in Friedman’s first gradient boosting paper, they comment on the trade-off between the number of trees (M ) and the learning rate (v) and recommend a small value for the learning rate < 0.1.
	Smaller values of v lead to larger values of M for the same training risk, so that there is a tradeoff between them. [...] In fact, the best strategy appears to be to set v to be very small (v < 0.1) and then choose M by early stopping.
Also, as in Friedman’s stochastic gradient boosting paper, they recommend a subsampling percentage (n) without replacement with a value of about 50%.
	A typical value for n can be 12 , although for large N , n can be substantially smallerthan 12 .



## Configuration Advice From scikit-learn
It is useful to review the default configuration for the algorithm in this library. There are many parameters, but below are a few key defaults.

 learning rate = 0.1 (shrinkage).
 n estimators = 100 (number of trees).
 max depth = 3.
 min samples split = 2.
 min samples leaf = 1.
 subsample = 1.0.
In the scikit-learn user guide under the section titled Gradient Tree Boosting 4 the authors comment that setting the maximum leaf nodes has a similar effect to setting the max depth to one minus the maximum leaf nodes, but results in worse performance.
	We found that max leaf nodes=k gives comparable results to max depth=k − 1 but is significantly faster to train at the expense of a slightly higher training error.
## Configuration Advice From XGBoost
The XGBoost library is dedicated to the gradient boosting algorithm. It too specifies default parameters that are interesting to note, firstly the XGBoost Parameters
 eta = 0.3 (a.k.a learning rate).
 max depth = 6.
 subsample = 1.

This shows a higher learning rate and a larger max depth than we see in most studies and
other libraries. Similarly, we can summarize the default parameters for XGBoost in the Python
 max depth = 3.
 learning rate = 0.1.
 n estimators = 100.
 subsample = 1
These defaults are generally more in-line with scikit-learn defaults and recommendations from the papers.

when asked how to configure XGBoost, Tong He suggested the three most
important parameters to tune are: the number of trees, tree depth and learning rate. He also provide a terse configuration strategy for new problems :
1. Run the default configuration (and presumably review learning curves?).
- Start with `XGBClassifier()` or `XGBRegressor()` using **default parameters**.
    
- Train the model and observe **learning curves** (train vs. validation loss).

2. If the system is overlearning, slow the learning down (using shrinkage?).
- **Reduce learning rate** (`learning_rate ↓`) → Slows down updates, preventing overfitting.
    
- **Reduce tree depth** (`max_depth ↓`) → Less complexity, reducing memorization.
    
- **Add regularization** (`alpha`, `lambda`) → Penalizes complexity.
3. If the system is underlearning, speed the learning up to be more aggressive (using shrinkage?).
- **Increase learning rate** (`learning_rate ↑`) → More aggressive updates, faster learning.
    
- **Increase tree depth** (`max_depth ↑`) → Allows capturing more patterns.
    
- **Increase number of trees** (`n_estimators ↑`) → More rounds of boosting.


In Owen Zhang’s talk to the NYC Data Science Academy in 2015 titled Winning Data Science Competitions 9 , he provides some general tips for configuring gradient boost with XGBoost. Owen is a heavy user of gradient boosting.
- My confession: I (over)use GBM. When in doubt, use GBM.
He provides some tips for configuring gradient boosting:
- Target 500-to-1000 trees and then tune the learning rate (n estimators).
- Set the number of samples in the leaf nodes to enough observations needed to make a good mean estimate (min child weight).
- Configure the interaction depth to about 10 or more (max depth).
In an updated slide deck for the same talk , he provides a table of common parameters he uses for XGBoost, summarized as follows:
- Number of Trees (n estimators) set to a fixed value between 100 and 1000, depending on the dataset size.
- Learning Rate (learning rate) simplified to the ratio: number of trees. $\frac{2 to 10}{trees}$ depending on the number of trees.
- Row Sampling (subsample) grid searched values in the range [0.5, 0.75, 1.0].
- Column Sampling (colsample bytree and maybe colsample bylevel) grid searched values in the range [0.4, 0.6, 0.8, 1.0].
- Min Leaf Weight (min child weight) simplified to the ratio $\frac{3}{rare\_events}$ , where rare events is the percentage of rare event observations in the dataset.
- Tree Size (max depth) grid searched values in the rage [4, 6, 8, 10].
- Min Split Gain (gamma) fixed with a value of zero.

What is interesting to note is that this world class practitioner does not fiddle with gamma or the terms for the regularization penalty (reg alpha and reg lambda). In a similar talk by Owen at ODSC Boston 2015 titled Open Source Tools and Data Science Competitions 11 , he again summarized common parameters he uses. We can see some minor differences that may be relevant:
- Number of Trees and Learning Rate: Fix the number of trees at around 100 (rather than 1000) and then tune the learning rate.
- Max Tree Depth: Start with a value of 6 and presumably tune from there.
- Min Leaf Weight: Use a modified ratio of $\frac{1}{sqrt(rare\_events)}$ , where rare events is the percentage of rare event observations in the dataset.
- Column Sampling: Grid search values in the range of 0.3 to 0.5 (more constrained).
- Row Sampling: Fixed at the value 1.0.
- Min Split Gain: Fixed at the value 0.0.


Finally, Abhishek Thakur, in his tutorial titled Approaching (Almost) Any Machine Learning Problem 12 provided a similar table listing out key XGBoost parameters and suggestions for tuning. The tuning ranges for each parameter are much the same with some notable differences. Specifically, he suggests grid searching values for the Min Split Gain (gamma) and the regular- ization penalty terms (reg alpha and reg lambda). He also explore large values for tree size (max depth) values above 10 as well as fixed Min Leaf Weight (min child weight) values in
the range of about 1 to 10.


---
# Tune the Number and Size of Decision Trees with XGBoost 


# Tune the Size of Decision Trees
Deeper trees generally capture too many details of the problem and overfit the training dataset, limiting the ability to make good predictions on new data.
Generally, boosting algorithms are configured with weak learners, decision trees with few layers, sometimes as simple as just a root node, also called a decision stump rather than a decision tree.
```
model = XGBClassifier(max_depth=3)
```

# Tune The Number and Size of Trees
There is a relationship between the number of trees in the model and the depth of each tree.
We would expect that deeper trees would result in fewer trees being required in the model, and the inverse where simpler trees (such as decision stumps) require many more trees to achieve similar results.

---
# Tune Learning Rate and Number of Trees with XGBoost
One effective way to slow down learning in the gradient boosting model is to
use a learning rate, also called shrinkage or eta.
## Slow Learning in Gradient Boosting with a Learning Rate
A technique to slow down the learning in the gradient boosting model is to apply a weighting factor for the corrections by new trees when added to the model. This weighting is called the shrinkage factor or the learning rate.

Naive gradient boosting is the same as gradient boosting with shrinkage where the shrinkage factor is set to 1.0. Setting values less than 1.0 has the effect of making less corrections for each tree added to the model . This in turn results in more trees that must be added to the model.
It is common to have small values in the range of 0.1 to 0.3, as well as values less than 0.1

### Tuning Learning Rate
```
learning_rate = [0.0001, 0.001, 0.01, 0.1, 0.2, 0.3]
```
Smaller learning rates generally require more trees to be added to the model.



###### full code for ploting n_estimators vs learning rate
```

# grid search
model = XGBClassifier()
n_estimators=[800]
learning_rate=[0.0025, 0.005, 0.0075, 0.01, 0.0125, 0.00375]
#max_depth=[3]
param_grid = dict(n_estimators=n_estimators, learning_rate=learning_rate)
#kfold = StratifiedKFold(n_splits=10, shuffle=True, random_state=7)
grid_search = GridSearchCV(model, param_grid, scoring="neg_log_loss", n_jobs=-1, cv=kfold)
result = grid_search.fit(X, label_encoded_y)

# summarize results
print("Best: %f using %s" % (result.best_score_, result.best_params_))
means = result.cv_results_['mean_test_score']
stds = result.cv_results_['std_test_score']
params = result.cv_results_['params']
for mean, stdev, param in zip(means, stds, params):
    print("%f (%f) with: %r" % (mean, stdev, param))
# plot results
scores = numpy.array(means).reshape(len(learning_rate), len(n_estimators))
for i, value in enumerate(learning_rate):
    pyplot.plot(n_estimators, scores[i], label='learning_rate: ' + str(value))
pyplot.legend()
pyplot.xlabel('n_estimators')
pyplot.ylabel('Log Loss')
pyplot.savefig('n_estimators_vs_learning_rate.png')
```

---
# Tuning Stochastic Gradient Boosting with XGBoost

A simple technique for ensembling decision trees involves training trees on subsamples of the training dataset. Subsets of the rows in the training data can be taken to train individual trees called bagging. When subsets of rows of the training data are also taken when calculating each split point, this is called random forest. These techniques can also be used in the gradient tree
boosting model in a technique called stochastic gradient boosting.

## Stochastic Gradient Boosting
Bagging is a technique where a collection of decision trees are created, each from a different random subset of rows from the training data. The effect is that better performance is achieved from the ensemble of trees because the randomness in the sample allows slightly different trees to be created, adding variance to the ensembled predictions.
Random forest takes this one step further, by allowing the features (columns) to be subsampled when choosing split points, adding further variance to the ensemble of trees. These same techniques can be used in the construction of decision trees in gradient boosting in a variation called stochastic gradient boosting. It is common to use aggressive sub-samples of the training data such as 40% to 80%.


### Tuning Row Subsampling
Row subsampling involves selecting a random sample of the training dataset without replacement.

can be specified in the scikit-learn wrapper of the XGBoost class in the subsample parameter. The default is 1.0 which is no sub-sampling. We can use the grid search capability built into scikit-learn to evaluate the effect of different subsample values from 0.1 to 1.0

```
# grid search
model = XGBClassifier()

subsample = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 1.0]
#max_depth=[3]
param_grid = dict(subsample=subsample)
#kfold = StratifiedKFold(n_splits=10, shuffle=True, random_state=7)
grid_search = GridSearchCV(model, param_grid, scoring="neg_log_loss", n_jobs=-1, cv=5)
grid_result = grid_search.fit(X, label_encoded_y)
# summarize results
print("Best: %f using %s" % (grid_result.best_score_, grid_result.best_params_))
means = grid_result.cv_results_['mean_test_score']
stds = grid_result.cv_results_['std_test_score']
params = grid_result.cv_results_['params']
for mean, stdev, param in zip(means, stds, params):
    print("%f (%f) with: %r" % (mean, stdev, param))
# plot
pyplot.errorbar(subsample, means, yerr=stds)
pyplot.title("XGBoost subsample vs Log Loss")
pyplot.xlabel('subsample')
pyplot.ylabel('Log Loss')
pyplot.savefig('subsample.png')
```

# Tuning Column Subsampling By Tree
We can also create a random sample of the features (or columns) to use prior to creating each decision tree in the boosted model.
colsample_bytree parameter. The default value is 1.0 meaning that all columns are
used in each decision tree. We can evaluate values for colsample bytree between 0.1 and 1.0 incrementing by 0.1.
```
# grid search
model = XGBClassifier()

colsample_bytree = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 1.0]
#max_depth=[3]
param_grid = dict(colsample_bytree=colsample_bytree)
#kfold = StratifiedKFold(n_splits=10, shuffle=True, random_state=7)
grid_search = GridSearchCV(model, param_grid, scoring="neg_log_loss", n_jobs=-1, cv=5)
grid_result = grid_search.fit(X, label_encoded_y)
# summarize results
print("Best: %f using %s" % (grid_result.best_score_, grid_result.best_params_))
means = grid_result.cv_results_['mean_test_score']
stds = grid_result.cv_results_['std_test_score']
params = grid_result.cv_results_['params']
for mean, stdev, param in zip(means, stds, params):
    print("%f (%f) with: %r" % (mean, stdev, param))
# plot
pyplot.errorbar(colsample_bytree, means, yerr=stds)
pyplot.title("XGBoost colsample_bytree vs Log Loss")
pyplot.xlabel('colsample_bytree')
pyplot.ylabel('Log Loss')
pyplot.savefig('colsample_bytree.png')
```


# Tuning Column Subsampling By Split
Rather than subsample the columns once for each tree, we can subsample them at each split in the decision tree. In principle, this is the approach used in random forest. using colsample_bylevel param

```
# grid search
model = XGBClassifier()

colsample_bylevel = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 1.0]
param_grid = dict(colsample_bylevel=colsample_bylevel)
grid_search = GridSearchCV(model, param_grid, scoring="neg_log_loss", n_jobs=-1, cv=5)
grid_result = grid_search.fit(X, label_encoded_y)
# summarize results
print("Best: %f using %s" % (grid_result.best_score_, grid_result.best_params_))
means = grid_result.cv_results_['mean_test_score']
stds = grid_result.cv_results_['std_test_score']
params = grid_result.cv_results_['params']
for mean, stdev, param in zip(means, stds, params):
    print("%f (%f) with: %r" % (mean, stdev, param))
# plot
pyplot.errorbar(colsample_bylevel, means, yerr=stds)
pyplot.title("XGBoost colsample_bylevel vs Log Loss")
pyplot.xlabel('colsample_bylevel')
pyplot.ylabel('Log Loss')
pyplot.savefig('colsample_bylevel.png')
```



---
---


