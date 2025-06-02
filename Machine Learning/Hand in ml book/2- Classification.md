

### Stochastic Gradient Descent (SGD)

This classifier has the advantage of being capable of handling very large datasets efficiently. This is in part because SGD deals with training instances independently, one at a time (which also makes SGD well suited for online learning),

# Performance Measures
### Measuring Accuracy Using Cross-Validation
K-fold cross-validation means splitting the training set into K-folds (in this case, three), then mak‐ing predictions and evaluating them on each fold using a model trained on the
remaining folds

You could use  this code
```
from sklearn.model_selection import cross_val_score
cross_val_score(sgd_clf, X_train, y_train_5, cv=3, scoring="accuracy")
```
<mark>Note: accuracy is generally not the preferred performance measure
for classifiers, especially when you are dealing with skewed datasets</mark>
#### Confusion Matrix
A much better way to evaluate the performance of a classifier is to look at the confu‐
sion matrix.The general idea is to count the number of times instances of class A are
classified as class B. For example, to know the number of times the classifier confused
images of 5s with 3s, you would look in the 5th row and 3rd column of the confusion
matrix.
```
from sklearn.metrics import confusion_matrix
confusion_matrix(y_train_5, y_train_pred)
```

but sometimes you may prefer a more concise metric. An interesting one to look at is the accuracy of the positive predictions; this is called the precision of the classifier
$$ precision=\frac{TP}{TP + FP}$$
precision is typically used along with another metric named recall, also called sensitivity or true positive rate $$ recall=\frac{TP}{TP+FN}$$
![](https://i.imgur.com/FCE9vpy.png)

It is often convenient to combine precision and recall into a single metric called the F1
score in particular if you need a simple way to compare two classifiers. The F1 score is
the harmonic mean of precision and recall
the classifier will only get a high F1 score if both recall and precision are
high.
![](https://i.imgur.com/WgcRQPP.png)
The F1 score favors classifiers that have similar precision and recall. This is not always
what you want: in some contexts you mostly care about precision, and in other con‐
texts you really care about recall.
Unfortunately, you can’t have it both ways: increasing precision reduces recall, and
vice versa. This is called the precision/recall tradeoff.

#### Precision/Recall Tradeoff
<mark>lowering the threshold increases recall and reduces precision. and vise versa</mark>
Scikit-Learn does not let you set the threshold directly, but it does give you access to
the decision scores that it uses to make predictions.Instead of calling the classifier’s
predict() method, you can call its decision_function() method, which returns a
score for each instance, and then make predictions based on those scores using any
threshold you want:
```
y_scores = sgd_clf.decision_function([some_digit])
y_scores
threshold = 0
y_some_digit_pred = (y_scores > threshold)
```

###### how do you decide which threshold to use?
For this you will first need to get the scores of all instances in the training set using the cross_val_predict() function again, but this time specifying that you want it to return decision scores instead of predictions:
```
y_scores = cross_val_predict(sgd_clf, X_train, y_train_5, cv=3,
method="decision_function")
```
Now with these scores you can compute precision and recall for all possible thresh‐
olds using the precision_recall_curve() function:
```
from sklearn.metrics import precision_recall_curve
precisions, recalls, thresholds = precision_recall_curve(y_train_5, y_scores)
```
Finally, you can plot precision and recall as functions of the threshold value using
Matplotlib
```
def plot_precision_recall_vs_threshold(precisions, recalls, thresholds):
	plt.plot(thresholds, precisions[:-1], "b--", label="Precision")
	plt.plot(thresholds, recalls[:-1], "g-", label="Recall")
	[...] # highlight the threshold, add the legend, axis label and grid
plot_precision_recall_vs_threshold(precisions, recalls, thresholds)
plt.show()
```

Another way to select a good precision/recall tradeoff is to plot precision directly
against recall,
let’s suppose you decide to aim for 90% precision. You look up the first plot and
find that you need to use a threshold of about 8,000. To be more precise you can
search for the lowest threshold that gives you at least 90% precision (np.argmax()
will give us the first index of the maximum value, which in this case means the first
True value):
#### The ROC Curve
the ROC curve plots the true positive rate (another name for recall) against the false positive rate. The FPR is the ratio of negative instances that are incorrectly classified as positive. It is equal to one minus the true negative rate, which is the ratio of negative instances that are correctly classified as negative. The TNR is also called specificity. Hence the ROC curve plots sensitivity (recall) versus  specificity.

One way to compare classifiers is to measure the area under the curve (AUC). A per‐
fect classifier will have a ROC AUC equal to 1, whereas a purely random classifier will
have a ROC AUC equal to 0.5.
![](https://i.imgur.com/cnes0IJ.png)


### Multiclass Classification
Whereas binary classifiers distinguish between two classes, multiclass classifiers (also
called multinomial classifiers) can distinguish between more than two classes.


<mark>Note: Always remember that scalling the inputs my increases the accuracy</mark>


### Error Analysis
we will assume that you have found a promising model and you want
to find ways to improve it.
1- First, you can look at the confusion matrix.
