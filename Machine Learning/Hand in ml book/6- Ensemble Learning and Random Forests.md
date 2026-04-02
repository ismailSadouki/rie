# Voting Classifiers
Suppose you have trained a few classifiers, each one achieving about 80% accuracy.
A very simple way to create an even better classifier is to aggregate the predictions of
each classifier and predict the class that gets the most votes. This majority-vote classi‐
fier is called a hard voting classifier
![](https://i.imgur.com/M143MQN.png)
Somewhat surprisingly, this voting classifier often achieves a higher accuracy than the
best classifier in the ensemble.

![](https://i.imgur.com/tjKHnGa.png)

The following code creates and trains a voting classifier in Scikit-Learn, composed of
three diverse classifiers (the training set is the moons dataset
```
from sklearn.ensemble import RandomForestClassifier
from sklearn.ensemble import VotingClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC
log_clf = LogisticRegression()
rnd_clf = RandomForestClassifier()
svm_clf = SVC()
voting_clf = VotingClassifier(
	estimators=[('lr', log_clf), ('rf', rnd_clf), ('svc', svm_clf)],
	voting='hard')
voting_clf.fit(X_train, y_train)
```
look at each classifier’s accuracy on the test set
```
from sklearn.metrics import accuracy_score
for clf in (log_clf, rnd_clf, svm_clf, voting_clf):
	clf.fit(X_train, y_train)
	y_pred = clf.predict(X_test)
	print(clf.__class__.__name__, accuracy_score(y_test, y_pred))
```
![](https://i.imgur.com/XGR1sLu.png)
If all classifiers are able to estimate class probabilities (i.e., they have a pre
dict_proba() method), then you can tell Scikit-Learn to predict the class with the
highest class probability, averaged over all the individual classifiers. This is called soft
voting. It often achieves higher performance than hard voting because it gives more
weight to highly confident votes.
<mark>All you need to do is replace voting="hard" with voting="soft" and ensure that all classifiers can estimate class probabilities.</mark> This is not the case of the SVC class by default, so you need to set its probability hyperparameter to True (this will make the SVC class use cross-validation to estimate class probabilities, slowing down training, and it will add a predict_proba() method).
modify the preceding code to use soft voting, you will find that the voting classifier
achieves over 91.2% accuracy!
# Bagging and Pasting
One way to get a diverse set of classifiers is to use very different training algorithms,
Another approach is to use the same training algorithm for every predictor, but to train them on different random subsets of the training set.
When sampling is performed with replacement, this method is called bagging1
When sampling is performed without replacement, it is called pasting.3
![](https://i.imgur.com/pZSXle4.png)

Once all predictors are trained, the ensemble can make a prediction for a new
instance by simply aggregating the predictions of all predictors.
aggregation reduces both bias and variance. Generally, the net result is that the
ensemble has a similar bias but a lower variance than a single predictor trained on the
original training set.
The following code trains an
ensemble of 500 Decision Tree classifiers,5 each trained on 100 training instances ran‐
domly sampled from the training set with replacement (this is an example of bagging,
but if you want to use pasting instead, just set bootstrap=False). The n_jobs param‐
eter tells Scikit-Learn the number of CPU cores to use for training and predictions
(–1 tells Scikit-Learn to use all available cores):
```
from sklearn.ensemble import BaggingClassifier
from sklearn.tree import DecisionTreeClassifier
bag_clf = BaggingClassifier(
	DecisionTreeClassifier(), n_estimators=500,
	max_samples=100, bootstrap=True, n_jobs=-1)
bag_clf.fit(X_train, y_train)
y_pred = bag_clf.predict(X_test)
```


<mark>
Overall, bagging often results in better models, which explains why it is gen‐
erally preferred. However, if you have spare time and CPU power you can use cross-
validation to evaluate both bagging and pasting and select the one that works best.
</mark>


# Out-of-Bag Evaluation
With bagging, some instances may be sampled several times for any given predictor,while others may not be sampled at all.
This means that only about 63% of the training instances are sampled on
average for each predictor.6 The remaining 37% of the training instances that are not
sampled are called out-of-bag (oob) instances.
Since a predictor never sees the oob instances during training, it can be evaluated on
these instances, without the need for a separate validation set. You can evaluate the
ensemble itself by averaging out the oob evaluations of each predictor.

```
bag_clf = BaggingClassifier(
	DecisionTreeClassifier(), n_estimators=500,
	bootstrap=True, n_jobs=-1, oob_score=True)
	bag_clf.fit(X_train, y_train)
	bag_clf.oob_score_
	90%......
```
According to this oob evaluation, this BaggingClassifier is likely to achieve about
90.1% accuracy on the test set

# Random Patches and Random Subspaces
The BaggingClassifier class supports sampling the features as well. This is con‐
trolled by two hyperparameters: max_features and bootstrap_features.
They work the same way as max_samples and bootstrap, but for feature sampling instead of
instance sampling. Thus, each predictor will be trained on a random subset of the
input features
<mark>This is particularly useful when you are dealing with high-dimensional inputs</mark>
Sampling both training instances and features is called the Random
Patches method.
Keeping all training instances (i.e., bootstrap=False and max_sam
ples=1.0) but sampling features (i.e., bootstrap_features=True and/or max_fea
tures smaller than 1.0) is called the Random Subspaces method.8

# Random Forests
As we have discussed, a Random Forest9 is an ensemble of Decision Trees, generally
trained via the bagging method (or sometimes pasting), typically with max_samples
set to the size of the training set.
Instead of building a BaggingClassifier and pass‐
ing it a DecisionTreeClassifier, you can instead use the RandomForestClassifier
class,
The following code trains a Random Forest classifier with 500 trees (each limited to maximum 16 nodes), using all available CPU cores:
```
from sklearn.ensemble import RandomForestClassifier
rnd_clf = RandomForestClassifier(n_estimators=500, max_leaf_nodes=16, n_jobs=-1)
rnd_clf.fit(X_train, y_train)
y_pred_rf = rnd_clf.predict(X_test)
```

# ExtraTreesClassifier

![](https://i.imgur.com/iffOJUM.png)

# Boosting
refers to any Ensemble method that can combine several weak learners into a strong learner.The general idea of most boosting methods is to train predictors sequentially, each trying to correct its predecessor. There are many boosting methods available, but by far the most popular are
AdaBoost13 (short for Adaptive Boosting) and Gradient Boosting. Let’s start with Ada‐
Boost.
# AdaBoost
![](https://i.imgur.com/GQI7l7U.png)

# Gradient Boosting
Gradient Boosting works by sequentially adding predictors to an ensemble, each one
correcting its predecessor.
A simpler way to train GBRT ensembles is to use Scikit-Learn’s GradientBoostingRe
gressor class. Much like the RandomForestRegressor class, it has hyperparameters to
control the growth of Decision Trees (e.g., max_depth, min_samples_leaf, and so on),
as well as hyperparameters to control the ensemble training, such as the number of
trees (n_estimators). The following code creates the same ensemble as the previous
one:
```
from sklearn.ensemble import GradientBoostingRegressor
gbrt = GradientBoostingRegressor(max_depth=2, n_estimators=3, learning_rate=1.0)
gbrt.fit(X, y)
```
In order to find the optimal number of trees, you can use early stopping
A simple way to implement this is to use the staged_predict() method: it
returns an iterator over the predictions made by the ensemble at each stage of train‐
ing (with one tree, two trees, etc.). The following code trains a GBRT ensemble with
120 trees, then measures the validation error at each stage of training to find the opti‐
mal number of trees, and finally trains another GBRT ensemble using the optimal
number of trees:
```
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error
X_train, X_val, y_train, y_val = train_test_split(X, y)
gbrt = GradientBoostingRegressor(max_depth=2, n_estimators=120)
gbrt.fit(X_train, y_train)
errors = [mean_squared_error(y_val, y_pred)
	for y_pred in gbrt.staged_predict(X_val)]
bst_n_estimators = np.argmin(errors)
gbrt_best = GradientBoostingRegressor(max_depth=2,n_estimators=bst_n_estimators)
gbrt_best.fit(X_train, y_train)
```

It is also possible to implement early stopping by actually stopping training early
(instead of training a large number of trees first and then looking back to find the
optimal number).
You can do so by setting warm_start=True, which makes Scikit-
Learn keep existing trees when the fit() method is called, allowing incremental
training. The following code stops training when the validation error does not
improve for five iterations in a row:
![](https://i.imgur.com/8mFUWpg.png)

It is worth noting that an optimized implementation of Gradient Boosting is available
in the popular python library XGBoost, which stands for Extreme Gradient Boosting
In fact, XGBoost is often an important component of the winning
entries in ML competitions. XGBoost’s API is quite similar to Scikit-Learn’s:
```
import xgboost
xgb_reg = xgboost.XGBRegressor()
xgb_reg.fit(X_train, y_train)
y_pred = xgb_reg.predict(X_val)
```
XGBoost also offers several nice features, such as automatically taking care of early
stopping:
```
xgb_reg.fit(X_train, y_train,
	eval_set=[(X_val, y_val)], early_stopping_rounds=2)
y_pred = xgb_reg.predict(X_val)
```
<mark>XGBoost model might be better at handling outliers.</mark>
# Stacking
If **no single model** gives the best performance, stacking can learn a better combination.
Works best when base models have **complementary errors** (i.e., their mistakes are different).
- Bagging (`RandomForest`) reduces variance, boosting (`XGBoost`) reduces bias.
- Stacking **learns** how to best combine different models, potentially outperforming both.
- Stacking introduces extra complexity, so it requires **enough training data** to generalize well.
- If data is **too small**, stacking may overfit (use cross-validation to avoid this).