What if your data is actually more complex than a simple straight line? Surprisingly,
you can actually use a linear model to fit nonlinear data. A simple way to do this is to
add powers of each feature as new features, then train a linear model on this extended
set of features. This technique is called Polynomial Regression.

Note that when there are multiple features, Polynomial Regression is capable of find‐
ing relationships between features (which is something a plain Linear Regression
model cannot do).
![](https://i.imgur.com/HR6FCzt.png)


### Learning Curves
If you perform high-degree Polynomial Regression, you will likely fit the training
data much better than with plain Linear Regression.

![](https://i.imgur.com/zu0mZDi.png)


so how can you decide how complex your model should be? How can you tell
that your model is overfitting or underfitting the data?



In Chapter 2 you used cross-validation to get an estimate of a model’s generalization
performance. If a model performs well on the training data but generalizes poorly
according to the cross-validation metrics, then your model is overfitting. If it per‐
forms poorly on both, then it is underfitting. This is one way to tell when a model is
too simple or too complex.

Another way is to look at the learning curves: these are plots of the model’s perfor‐
mance on the training set and the validation set as a function of the training set size
(or the training iteration).

**Note**: If your model is underfitting the training data, adding more train‐
ing examples will not help. You need to use a more complex model
or come up with better features.
One way to improve an overfitting model is to feed it more training
data until the validation error reaches the training error.

# Regularized Linear Models
a good way to reduce overfitting is to regularize the
model (i.e., to constrain it): the fewer degrees of freedom it has, the harder it will be
for it to overfit the data. For example, a simple way to regularize a polynomial model
is to reduce the number of polynomial degrees.
## Ridge Regression
The hyperparameter α controls how much you want to regularize the model. If α = 0
then Ridge Regression is just Linear Regression. If α is very large, then all weights end
up very close to zero and the result is a flat line going through the data’s mean.
<mark>Note</mark>
![](https://i.imgur.com/i5WHWIl.png)
## Lasso Regression
An important characteristic of Lasso Regression is that it tends to completely elimi‐
nate the weights of the least important features (i.e., set them to zero).
other words, Lasso Regression automatically performs feature selection and outputs a
sparse model (i.e., with few nonzero feature weights)
## Elastic Net
Elastic Net is a middle ground between Ridge Regression and Lasso Regression.
The regularization term is a simple mix of both Ridge and Lasso’s regularization terms,
and you can control the mix ratio r. When r = 0, Elastic Net is equivalent to Ridge
Regression, and when r = 1, it is equivalent to Lasso Regression

<mark>So when should you use plain Linear Regression (i.e., without any regularization)?</mark>
It is almost always preferable to have at least a little bit of
regularization, so generally you should avoid plain Linear Regression. Ridge is a good
default, but if you suspect that only a few features are actually useful, you should pre‐
fer Lasso or Elastic Net since they tend to reduce the useless features’ weights down to
zero as we have discussed.
In general, Elastic Net is preferred over Lasso since Lasso
may behave erratically when the number of features is greater than the number of
training instances or when several features are strongly correlated.

short example using Scikit-Learn’s
```
>>> from sklearn.linear_model import ElasticNet
>>> elastic_net = ElasticNet(alpha=0.1, l1_ratio=0.5)
>>> elastic_net.fit(X, y)
>>> elastic_net.predict([[1.5]])
```



# Early Stopping

A very different way to regularize iterative learning algorithms such as Gradient
Descent is to stop training as soon as the validation error reaches a minimum.
shows a complex model (in this case a high-degree
Polynomial Regression model) being trained using Batch Gradient Descent. As the
epochs go by, the algorithm learns and its prediction error (RMSE) on the training set
naturally goes down, and so does its prediction error on the validation set. after a while the validation error stops decreasing and actually starts to go back up
<mark>This indicates that the model has started to overfit the training data.</mark>
![](https://i.imgur.com/I0gl2xB.png)
![](https://i.imgur.com/fgGfhEZ.png)
Note that with warm_start=True, when the fit() method is called, it just continues
training where it left off instead of restarting from scratch.
**Note**
### **When NOT to Use Early Stopping**

❌ If your dataset is **very small**, the model may need more epochs to generalize.  
❌ If you don’t have a separate **validation set**, early stopping won’t work properly.  
❌ If your model is **underfitting**, stopping early might make it worse.
### **Use Early Stopping When:**

✅ **You have a validation set**

- If your model trains too long, it may start memorizing noise instead of learning patterns.
- Early stopping monitors performance on a validation set and stops training when the model starts performing worse.
 ✅ **You're using boosting algorithms**

- **Gradient Boosting (XGBoost, LightGBM, CatBoost)** benefits from early stopping to avoid excessive trees that hurt performance.

✅ **You want to reduce training time**

- Training models for too many epochs is computationally expensive.
- Early stopping saves time by stopping training **as soon as performance stops improving**.









# Logistic Regression
is commonly used to estimate the probability that an instance belongs to a particular class

# Softmax Regression
The Logistic Regression model can be generalized to support multiple classes directly,
without having to train and combine multiple binary classifiers (as discussed in
Chapter 3). This is called Softmax Regression, or Multinomial Logistic Regression.