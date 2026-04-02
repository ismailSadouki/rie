# Feature Importance in Gradient Boosting
importance provides a score that indicates how useful or valuable each feature was in the construction of the boosted decision trees within the model. The more an attribute is used to make key decisions with decision trees, the higher its relative importance.
The feature importances are then averaged across all of the decision trees within the model.
```
print(model.feature_importances_)
```

A downside of this plot is that the features are ordered by their input index rather than their importance. We could sort the features before plotting. Thankfully, there is a built in plot function to help us.

```
from xgboost import plot_importance
plot_importance(model)
```

# Feature Selection with XGBoost Feature Importance Scores

This is done using the SelectFromModel class that takes a model and can transform a dataset into a subset with selected features.
It can then use a threshold to decide which features to select. This threshold is
used when you call the transform() function on the SelectFromModel instance to consistently select the same features on the training dataset and the test dataset.
```

thresholds = sorted(model.feature_importances_)
for thresh in thresholds:
    selection = SelectFromModel(model, threshold=thresh, prefit=True)
    select_X_train = selection.transform(X_train)
    # train model
    selection_model = XGBClassifier()
    selection_model.fit(select_X_train, y_train)
    # eval model
    select_X_test = selection.transform(X_test)
    predictions = selection_model.predict(select_X_test)
    accuracy = accuracy_score(y_test, predictions)
    print("Thresh=%.3f, n=%d, Accuracy: %.2f%%" % (thresh, select_X_train.shape[1],     accuracy*100.0))
    OUT:
	    Thresh=0.021, n=4, Accuracy: 90.00%
		Thresh=0.089, n=3, Accuracy: 90.00%
		Thresh=0.384, n=2, Accuracy: 94.00%
		Thresh=0.506, n=1, Accuracy: 90.00%
```


---
# Monitor Training Performance and Early Stopping

Overfitting is a problem with sophisticated nonlinear learning algorithms like gradient boosting.

Early stopping is an approach to training complex machine learning models to avoid overfitting. It works by monitoring the performance of the model that is being trained on a separate test dataset and stopping the training procedure once the performance on the test dataset has not improved after a fixed number of training iterations.

It avoids overfitting by attempting to automatically select the inflection point where performance on the test dataset starts to decrease while performance on the training dataset continues to improve as the model starts to overfit.
The performance measure may be the loss function that is being optimized to train the model (such as logarithmic loss), or an external metric of interest to the problem in general (such as classification accuracy).

## Monitoring Training Performance With XGBoost
The XGBoost model can evaluate and report on the performance on a test set for the model during training. It supports this capability by specifying both an test dataset and an evaluation metric on the call to model.fit() when training the model and specifying verbose output. For example, we can report on the binary classification error rate (error) on a standalone test set (eval set) while training an XGBoost model as follows:

```
es_model = XGBClassifier(eval_metric="mlogloss")
eval_set = [(X_test, y_test)]
es_model.fit(X_train, y_train, eval_set=eval_set, verbose=True)
predictions = es_model.predict(X_test)
```
XGBoost supports a suite of evaluation metrics not limited to:
 rmse for root mean squared error.
 mae for mean absolute error.
 logloss for binary logarithmic loss and mlogloss for multiclass log loss (cross entropy).
 error for classification error.
 auc for area under ROC curve.

## Evaluate XGBoost Models With Learning Curves
We can retrieve the performance of the model on the evaluation dataset and plot it to get insight into how learning unfolded while training.

We provide an array of X and y pairs to the
eval metric argument when fitting our XGBoost model. In addition to a test set, we can also provide the training dataset. This will provide a report on how well the model is performing on both training and test sets during training. For example:

```
from matplotlib import pyplot

es_model = XGBClassifier(eval_metric=["merror", "mlogloss"])
eval_set = [(X_train, y_train), (X_test, y_test)]
es_model.fit(X_train, y_train, eval_set=eval_set, verbose=True)
predictions = es_model.predict(X_test)
# evaluate predictions
accuracy = accuracy_score(y_test, predictions)
print("Accuracy: %.2f%%" % (accuracy * 100.0))
# retrieve performance metrics
results = es_model.evals_result()
epochs = len(results['validation_0']['merror'])
x_axis = range(0, epochs)
# plot log loss
fig, ax = pyplot.subplots()
ax.plot(x_axis, results['validation_0']['mlogloss'], label='Train')
ax.plot(x_axis, results['validation_1']['mlogloss'], label='Test')
ax.legend()
plt.ylabel('Log Loss')
plt.title('XGBoost Log Loss')
plt.show()
# plot classification error
fig, ax = plt.subplots()
ax.plot(x_axis, results['validation_0']['merror'], label='Train')
ax.plot(x_axis, results['validation_1']['merror'], label='Test')
ax.legend()
plt.ylabel('Classification Error')
plt.title('XGBoost Classification Error')
plt.show()
```


## Early Stopping With XGBoost
XGBoost supports early stopping after a fixed number of iterations. In addition to specifying a metric and test dataset for evaluation each epoch, you must specify a window of the number of epochs over which no improvement is observed. This is specified in the early stopping rounds parameter. For example, we can check for no improvement in logarithmic loss over the 10 epochs as follows:


<mark> SEARCH MORE ABOUT IT</mark>


