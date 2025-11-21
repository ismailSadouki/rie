# 25.2 Importance of Data Scaling
Some machine learning algorithms perform much better if all of the variables are scaled to the same range, such as scaling all variables to values between 0 and 1, called normalization. This effects algorithms that use a weighted sum of the input, like linear models and neural networks, as well as models that use distance measures such as support vector machines and k-nearest neighbors.

As such, it is a good practice to scale input data, and perhaps even try other data transforms such as making the data more normal (better fit a Gaussian probability distribution) using a power transform. This also applies to output variables, called target variables, such as numerical values that are predicted when modeling regression predictive modeling problems. For regression problems, it is often desirable to scale or transform both the input and the target variables.

# 25.3 How to Scale Target Variables
## 25.3.1 Manual Transform of the Target Variable
Manually managing the scaling of the target variable involves creating and applying the scaling object to the data manually. It involves the following steps:
1. Create the transform object, e.g. a MinMaxScaler.
2. Fit the transform on the training dataset.
3. Apply the transform to the train and test datasets.
4. Invert the transform on any predictions made.

For example, if we wanted to normalize a target variable, we would first define and train a MinMaxScaler object:
```
# create target scaler object
target_scaler = MinMaxScaler()
target_scaler.fit(train_y)
# transform target variables
train_y = target_scaler.transform(train_y)
test_y = target_scaler.transform(test_y)
# invert transform on predictions
yhat = model.predict(test_X)
yhat = target_scaler.inverse_transform(yhat)
# we would fit our model and use the model to make predictions. Before the predictions can be used or evaluated with an error metric, we would have to invert the transform.
```

## 25.3.2 Automatic Transform of the Target Variable 
An alternate approach is to automatically manage the transform and inverse transform. This can be achieved by using the TransformedTargetRegressor object that wraps a given model and a scaling object.
It will prepare the transform of the target variable using the same training data
used to fit the model, then apply that inverse transform on any new data provided when calling fit(), returning predictions in the correct scale.
```
# define the target transform wrapper
wrapped_model = TransformedTargetRegressor(regressor=model, transformer=MinMaxScaler())
# use the target transform wrapper
wrapped_model.fit(train_X, train_y)
yhat = wrapped_model.predict(test_X)
```
This is much easier and allows you to use helpful functions like cross val score() to
evaluate a model.



#  HurberRegressor model
The **`HuberRegressor`** is a **robust linear regression model** from **scikit-learn** that is **less sensitive to outliers** than ordinary least squares (OLS) regression.
Unlike standard linear regression, which minimizes the **squared error** (which can exaggerate the influence of outliers), HuberRegressor uses the **Huber loss function**, which:

- Acts like **squared error** for small errors.
    
- Switches to **absolute error** for large errors (outliers), reducing their impact.
### 🔹 Key Features

- **Robust to outliers**: Downweights large residuals
    
- **Linear model**: Same interpretability as linear regression
    
- **Good when**: You have mostly clean data with **a few bad outliers**
    
- **Can be faster** than RANSAC in some cases
### 🔹 When to Use It

Use `HuberRegressor` if:

- Your data is mostly linear
    
- You suspect or observe a few **outliers**
    
- You want a **balance** between OLS and absolute-error models (like L1 regression)
### 🔹 When _Not_ to Use

Avoid if:

- Your data is highly non-linear (use tree models or non-linear regressors instead)
    
- You have no outliers and prefer classic least squares (use `LinearRegression`)
    
- You need sparsity (use `Lasso` or `ElasticNet`)