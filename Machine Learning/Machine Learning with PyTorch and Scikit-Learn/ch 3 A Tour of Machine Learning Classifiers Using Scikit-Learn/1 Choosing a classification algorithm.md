In practice, it is always recommended that you compare the performance of at least a handful of different learning algorithms to select the best model for the particular problem; these may differ in the number of features or examples, the amount of noise in a dataset, and whether the classes are linearly separable.

The five main steps that are involved in training a supervised machine learning algorithm can be summarized as follows:
1.Selecting features and collecting labeled training examples
2.Choosing a performance metric
3.Choosing a learning algorithm and training a model
4.Evaluating the performance of the model
5.Changing the settings of the algorithm and tuning the model.


# First steps with scikit-learn – training a perceptron
```
from sklearn import datasets
import numpy as np
from sklearn.model_selection import train_test_split
iris = datasets.load_iris()
X = iris.data[:, [2,3]]
y = iris.target
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=1, stratify=y)
```
Note that the train_test_split function already shuffles the training datasets internally before splitting; otherwise, all examples from class 0 and class 1 would have ended up in the training datasets, and the test dataset would consist of 45 examples from class 2.

stratify=y. In this context, stratification means that the train_test_split method returns training and test subsets that have the same proportions of class labels as the input dataset. We can use NumPy’s bincount function, which counts the number of occurrences of each value in an array, to verify that this is indeed the case:
![](https://i.imgur.com/SrVHh04.png)
standarization:
```
sc = StandardScaler()
sc.fit(X_train)
X_train_std = sc.transform(X_train)
X_test_std = sc.transform(X_test)
```

train a perceptron model 
```
ppn = Perceptron(eta0=0.1, random_state=1)
ppn.fit(X_train_std, y_train)
```

![](https://i.imgur.com/hCxgRA5.png)
Note that scikit-learn also implements a large variety of different performance metrics that are available via the metrics module.
```
y_pred = ppn.predict(X_test_std)
accuracy_score(y_test,y_pred)
```
Alternatively, each classifier in scikit-learn has a score method, which computes a classifier’s prediction accuracy by combining the predict call with accuracy_score, as shown here:
```
ppn.score(X_test_std, y_test)
```
![](https://i.imgur.com/LNupk71.png)
the perceptron algorithm never converges on datasets that aren’t perfectly linearly separable, which is why the use of the perceptron algorithm is typically not recommended in practice.