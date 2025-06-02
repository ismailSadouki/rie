
## 14.3.1 Correlation Feature Selection
The scikit-learn machine library provides an implementation of the correlation statistic in the f regression() function. This function can be used in a feature selection strategy
```
# configure to select all features
fs = SelectKBest(score_func=f_regression, k='all')
# learn relationship from training data
fs.fit(X_train, y_train)
# transform train input data
X_train_fs = fs.transform(X_train)
# transform test input data
X_test_fs = fs.transform(X_test)

# what are scores for the features
for i in range(len(fs.scores_)):
print('Feature %d: %f' % (i, fs.scores_[i]))
# plot the scores
pyplot.bar([i for i in range(len(fs.scores_))], fs.scores_)
pyplot.show()
```

## 14.3.2 Mutual Information Feature Selection
same as chapter 13