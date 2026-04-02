capable of performing linear or nonlinear classification, regression, and even
outlier detection.
SVMs are particularly well suited for classification of complex but small- or medium-sized datasets.

# Linear SVM Classification
<mark>
SVMs are sensitive to the feature scales,
</mark>


# Soft Margin Classification
If we strictly impose that all instances be off the street and on the right side, this is
called hard margin classification.
There are two main issues with hard margin classification. First, it only works if the data is **linearly separable**, and second it is quite **sensitive to outliers**.

To avoid these issues it is preferable to use a more flexible model. The objective is to
find a good balance between keeping the street as large as possible and limiting the
margin violations (i.e., instances that end up in the middle of the street or even on the
wrong side). This is called soft margin classification.

In Scikit-Learn’s SVM classes, you can control this balance using the C hyperparame‐
ter: 
- a smaller C value leads to a wider street but more margin violations.
![](https://i.imgur.com/S9sP6rc.png)

![](https://i.imgur.com/zOJgaYd.png)


# Nonlinear SVM Classification
Although linear SVM classifiers are efficient and work surprisingly well in many
cases, many datasets are not even close to being linearly separable.
One approach to handling nonlinear datasets is to add more features, such as polynomial features in some cases this can result in a linearly separable dataset.

To implement this idea using Scikit-Learn, you can create a Pipeline containing a
PolynomialFeatures transformer followed by a StandardScaler and a LinearSVC.
```
polynomial_svm_clf = Pipeline([
    ("poly_features", PolynomialFeatures(degree=3)),
    ("scaler", StandardScaler()),
    ("svm_clf", LinearSVC(C=10, loss="hinge"))
])
polynomial_svm_clf.fit(X, y)
```

# Polynomial Kernel
Adding polynomial features is simple to implement and can work great with all sorts
of Machine Learning algorithms (not just SVMs), but at a low polynomial degree it
cannot deal with very complex datasets, and with a high polynomial degree it creates
a huge number of features, making the model too slow.
Fortunately, when using SVMs you can apply an almost miraculous mathematical
technique called the kernel trick (it is explained in a moment). It makes it possible to
get the same result as if you added many polynomial features, even with very high-
degree polynomials, without actually having to add them. So there is no combinato‐
rial explosion of the number of features since you don’t actually add any features. This
trick is implemented by the SVC class. Let’s test it on the moons dataset:
```
from sklearn.svm import SVC
poly_kernel_svm_clf = Pipeline([
	("scaler", StandardScaler()),
	("svm_clf", SVC(kernel="poly", degree=3, coef0=1, C=5))
])
poly_kernel_svm_clf.fit(X, y)
```
This code trains an SVM classifier using a 3rd-degree polynomial kernel.
if your model is overfitting, you might want to reduce the polynomial degree. Conversely, if it is underfitting, you can try increasing
it.
The hyperparameter coef0 controls how much the model is influenced by high-
degree polynomials versus low-degree polynomials.
![](https://i.imgur.com/mEWut6C.png)

# Adding Similarity Features
Another technique to tackle nonlinear problems is to add features computed using a
similarity function that measures how much each instance resembles a particular
landmark.
# Gaussian RBF Kernel

Just like the polynomial features method, the similarity features method can be useful
with any Machine Learning algorithm, but it may be computationally expensive to
compute all the additional features, especially on large training sets. However, once
again the kernel trick does its SVM magic: it makes it possible to obtain a similar
result as if you had added many similarity features, without actually having to add
them. Let’s try the Gaussian RBF kernel using the SVC class:
```
rbf_kernel_svm_clf = Pipeline([
		("scaler", StandardScaler()),
		("svm_clf", SVC(kernel="rbf", gamma=5, C=0.001))
	])
rbf_kernel_svm_clf.fit(X, y)
```
γ acts like a regularization hyperparameter: if your model is overfitting, you should reduce it, and if it is underfitting, you should increase it (similar to the C hyperparameter).

# SVM Regression
The trick is to reverse the objective: instead of trying to fit the largest pos‐
sible street between two classes while limiting margin violations, SVM Regression
tries to fit as many instances as possible on the street while limiting margin violations
(i.e., instances off the street).
The width of the street is controlled by a hyperparameter ϵ.
```
from sklearn.svm import LinearSVR
svm_reg = LinearSVR(epsilon=1.5)
svm_reg.fit(X, y)
```


<mark>To tackle nonlinear regression tasks, you can use a kernelized SVM model.</mark>
The SVR class is the regression equivalent of the SVC class, and the LinearSVR class is the regression equivalent of the LinearSVC class. The LinearSVR class scales linearly with the size of the training set (just like the LinearSVC class), while the SVR class gets much too slow when the training set grows large (just like the SVC class).
```
from sklearn.svm import SVR
svm_poly_reg = SVR(kernel="poly", degree=2, C=100, epsilon=0.1)
svm_poly_reg.fit(X, y)
```