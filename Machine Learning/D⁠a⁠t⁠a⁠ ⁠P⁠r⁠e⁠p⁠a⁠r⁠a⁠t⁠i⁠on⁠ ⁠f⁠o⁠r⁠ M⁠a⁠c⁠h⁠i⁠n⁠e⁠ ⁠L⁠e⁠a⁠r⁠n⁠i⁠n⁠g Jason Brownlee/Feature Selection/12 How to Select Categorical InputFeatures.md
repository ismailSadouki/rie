There are two popular feature selection techniques that can be used for categorical input data and a categorical (class) target variable. They are:
 Chi-Squared Statistic.
 Mutual Information Statistic.


# 12.3.1 Chi-Squared Feature Selection
Pearson’s chi-squared statistical hypothesis test is an example of a test for independence between categorical variables.The results of this
test can be used for feature selection, where those features that are independent of the target variable can be removed from the dataset.
```
When there are three or more levels for the predictor, the degree of association between predictor and outcome can be measured with statistics such as χ2
```
For example, we can define the SelectKBest class to use the chi2() function and select all features, then transform the train and test sets.
```
fs = SelectKBest(score_func=chi2, k='all')
fs.fit(X_train, y_train)
X_train_fs = fs.transform(X_train)
X_test_fs = fs.transform(X_test)
```
We can then print the scores for each variable <mark>(largest is better)</mark>, and plot the scores for each variable as a bar graph to get an idea of how many features we should select.
```
# what are scores for the features
for i in range(len(fs.scores_)):
print('Feature %d: %f' % (i, fs.scores_[i]))
# plot the scores
pyplot.bar([i for i in range(len(fs.scores_))], fs.scores_)
pyplot.show()
```


# 12.3.2 Mutual Information Feature Selection 
Mutual information is calculated between two variables and measures the reduction in uncertainty for one variable given a known value of the other variable.
```
# feature selection
def select_features(X_train, y_train, X_test):
	fs = SelectKBest(score_func=mutual_info_classif, k='all')
	fs.fit(X_train, y_train)
	X_train_fs = fs.transform(X_train)
	X_test_fs = fs.transform(X_test)
	return X_train_fs, X_test_fs, fs
```

# 12.4 Modeling With Selected Features
There are many different techniques for scoring features and selecting features based on scores; how do you know which one to use? A robust approach is to evaluate models using different feature selection methods (and numbers of features) and select the method that results in a model with the best performance.