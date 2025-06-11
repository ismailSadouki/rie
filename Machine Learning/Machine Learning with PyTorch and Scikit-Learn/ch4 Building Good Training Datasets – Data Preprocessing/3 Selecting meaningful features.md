Common solutions to reduce the generalization error are as follows: (overfitting)
•Collect more training data
•Introduce a penalty for complexity via regularization
•Choose a simpler model with fewer parameters
•Reduce the dimensionality of the data

# L1 and L2 regularization as penalties against model complexity
L2 regularization is one approach to reduce the complexity of a model by penalizing large individual weights. We defined the squared L2 norm of our weight vector, w, as follows:
![](https://i.imgur.com/edOWWCq.png)
Another approach to reduce the model complexity is the related L1 regularization:
![](https://i.imgur.com/6vhpk4f.png)
In contrast to L2 regularization, L1 regularization usually yields sparse feature vectors, and most feature weights will be zero.
Sparsity can be useful in practice if we have a high-dimensional dataset with
many features that are irrelevant, especially in cases where we have more irrelevant dimensions than training examples.
# A geometric interpretation of L2 regularization
L2 regularization adds a penalty term to the loss function that
effectively results in less extreme weight values compared to a model trained with an unregularized loss function.

Here, we will consider the mean squared error (MSE) loss function that we used for Adaline in Chapter 2, which computes the squared distances between the true and predicted class labels, y and 𝑦hat , averaged over all N examples in the training set. Since the MSE is spherical, it is easier to draw than the loss function of logistic regression; however, the same concepts apply. Remember that our goal is to find the combination of weight coefficients that minimize the loss function for the training data, as shown in Figure 4.5 (the point in the center of the ellipses)
![](https://i.imgur.com/d8gLmja.png)
We can think of regularization as adding a penalty term to the loss function to encourage smaller weights; in other words, we penalize large weights. Thus, by increasing the regularization strength via the regularization parameter, 𝜆, we shrink the weights toward zero and decrease the dependence of our model on the training data.
![](https://i.imgur.com/o7nvwfT.png)
The quadratic L2 regularization term is represented by the shaded ball. Here, our weight coefficients cannot exceed our regularization budget—the combination of the weight coefficients cannot fall outside the shaded area.
On the other hand, we still want to minimize the loss function. Under the penalty
constraint, our best effort is to choose the point where the L2 ball intersects with the contours of the unpenalized loss function. The larger the value of the regularization parameter, 𝜆, gets, the faster the penalized loss grows, which leads to a narrower L2 ball. For example, if we increase the regularization parameter toward infinity, the weight coefficients will become effectively zero, denoted by the center of the L2 ball. To summarize the main message of the example, our goal is to minimize the sum of the unpenalized loss plus the penalty term, which can be understood as adding bias and preferring a simpler model to reduce the variance in the absence of sufficient training data to fit the model.
# Sparse solutions with L1 regularization
since the L1 penalty is the sum of the absolute weight coefficients (remember that the L2 term is quadratic), we can represent it as a diamond-shape budget, as shown in Figure 4.7:
![](https://i.imgur.com/pOLHvFd.png)
In the preceding figure, we can see that the contour of the loss function touches the L1 diamond at w1 = 0. Since the contours of an L1 regularized system are sharp, it is more likely that the optimum—that is, the intersection between the ellipses of the loss function and the boundary of the L1 diamond—is located on the axes, which encourages sparsity.
For regularized models in scikit-learn that support L1 regularization, we can simply set the penalty parameter to 'l1' to obtain a sparse solution:
```
>>> LogisticRegression(penalty='l1',
...solver='liblinear',
...multi_class='ovr')
```
Note that we also need to select a different optimization algorithm (for example, solver='liblinear'), since 'lbfgs' currently does not support L1-regularized loss optimization.
As a result of L1 regularization, which, as mentioned, serves as a method for feature selection, we just trained a model that is robust to the potentially irrelevant features in this dataset.
Strictly speaking, though, the weight vectors from the previous example are not necessarily sparse because they contain more non-zero than zero entries. However, we could enforce sparsity (more zero entries) by further increasing the regularization strength—that is, choosing lower values for the C parameter.
![](https://i.imgur.com/6FNNzO7.png)
The resulting plot provides us with further insights into the behavior of L1 regularization. As we can see, all feature weights will be zero if we penalize the model with a strong regularization parameter (C < 0.01); C is the inverse of the regularization parameter, 𝜆.
# Sequential feature selection algorithms
There are two main categories of dimensionality reduction techniques: feature selection and feature extraction.
Via feature selection, we select a subset of the original features, whereas in feature extraction, we derive information from the feature set to construct a new feature subspace.

Sequential feature selection algorithms are a family of greedy search algorithms that are used to reduce an initial d-dimensional feature space to a k-dimensional feature subspace where k<d. The motivation behind feature selection algorithms is to automatically select a subset of features that are  most relevant to the problem, to improve computational efficiency, or to reduce the generalization error of the model by removing irrelevant features or noise, which can be useful for algorithms that don’t support regularization.
**sequential backward selection (SBS)**, , which aims to reduce the dimensionality of the initial feature subspace with a minimum decay in the performance of the classifier to improve upon computational efficiency. In certain cases, SBS can even improve the predictive power of the model if a model suffers from overfitting.
![](https://i.imgur.com/wAwSl9g.png)

The idea behind the SBS algorithm is quite simple: SBS sequentially removes features from the full feature subset until the new feature subspace contains the desired number of features. To determine which feature is to be removed at each stage, we need to define the criterion function, J, that we want to minimize.

The criterion calculated by the criterion function can simply be the difference in the performance of the classifier before and after the removal of a particular feature. Then, the feature to be removed at each stage can simply be defined as the feature that maximizes this criterion; or in more simple terms, at each stage we eliminate the feature that causes the least performance loss after removal. Based on the preceding definition of SBS, we can outline the algorithm in four simple steps:
![](https://i.imgur.com/ENPUBTV.png)

![](https://i.imgur.com/uxYLZhe.png)


<mark>raje3</mark>

# Assessing feature importance with random forests
Using a random forest, we can measure the feature importance as the averaged impurity decrease computed from all decision trees in the forest, without making any assumptions about whether our data is linearly separable or not.

scikit-learn also implements a SelectFromModel object that selects features based on a user-spec-ified threshold after model fitting, which is useful if we want to use the RandomForestClassifier as a feature selector and intermediate step in a scikit-learn Pipeline object, which allows us to connect different preprocessing steps with an estimator