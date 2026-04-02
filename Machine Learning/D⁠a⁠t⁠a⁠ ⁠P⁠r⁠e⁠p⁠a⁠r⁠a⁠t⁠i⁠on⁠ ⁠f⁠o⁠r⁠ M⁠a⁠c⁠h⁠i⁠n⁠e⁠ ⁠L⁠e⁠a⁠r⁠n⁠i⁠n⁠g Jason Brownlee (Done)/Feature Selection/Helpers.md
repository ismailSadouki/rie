
This code performs feature selection using `SelectKBest` and evaluates how the number of selected features (`k`) impacts the accuracy of a logistic regression model.
```
for k in range(1, X_train.shape[1]+1):
    fs = SelectKBest(score_func=f_classif, k=k)
    X_train_fs = fs.fit_transform(X_train, y_train)
    X_test_fs = fs.transform(X_test)

    model = LogisticRegression(solver='liblinear')
    model.fit(X_train_fs, y_train)
    yhat = model.predict(X_test_fs)
    acc = accuracy_score(y_test, yhat)
    print(f"k={k} -> accuracy: {acc:.3f}")
```

![](https://i.imgur.com/h1aVnyj.png)
