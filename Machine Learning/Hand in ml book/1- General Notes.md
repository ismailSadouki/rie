### Anomaly Vs Novelty Detection
the difference is that novelty detection algorithms expect to
see only normal data during training, while anomaly detection algorithms are usually
more tolerant, they can often perform well even with a small percentage of outliers in
the training set.
### Association rule learning
the goal is to dig into large amounts of data and discover interesting relations between
attributes.


### Polynomial Regression model



## Main Challenges of Machine Learning

##### Poor-Quality Data
If some instances are missing a few features (e.g., 5% of your customers did not
specify their age), you must decide whether you want to ignore this attribute alto‐
gether, ignore these instances, fill in the missing values (e.g., with the median
age), or train one model with the feature and one model without it, and so on.
If some instances are clearly outliers, it may help to simply discard them or try to
fix the errors manually.
##### Irrelevant Features
Feature selection: selecting the most useful features to train on among existing
features.
Feature extraction: combining existing features to produce a more useful one (as
we saw earlier, dimensionality reduction algorithms can help).
#### Overfitting the Training Data
To simplify the model by selecting one with fewer parameters
(e.g., a linear model rather than a high-degree polynomial
model), by reducing the number of attributes in the training
data or by constraining the model
To reduce the noise in the training data (e.g., fix data errors
and remove outliers)

Constraining a model to make it simpler and reduce the risk of overfitting is called
**regularization**
<mark>NOTE: Where there is a lot of missing values, try regularized linear models</mark>
- When data has many missing values, imputing them (filling in missing values) might introduce noise or uncertainty. Regularization helps keep the model stable despite this.
- If missing values are imputed with mean/median values or using techniques like KNN imputation, it can introduce bias. 
- A **regularized model** reduces the impact of any imputation errors by shrinking coefficients, leading to a more **robust** and **generalizable** model.
- **Ridge Can Handle Multicollinearity**: If missing values are imputed, they may introduce **correlation between features**.

The amount of regularization to apply during learning can be controlled by a hyper‐
parameter. and Tuning hyperparameters is an important part of building a Machine Learning system
#### Underfitting the Training Data
it occurs when your model is too simple to learn the underlying structure of the data.
- Selecting a more powerful model, with more parameters
• Feeding better features to the learning algorithm (feature engineering)
• Reducing the constraints on the model (e.g., reducing the regularization hyper‐
parameter)



## Testing and Validating
If the training error is low (i.e., your model makes few mistakes on the training set)
but the generalization error is high, it means that your model is overfitting the train‐
ing data

suppose that the linear model generalizes better, but you want to apply some
regularization to avoid overfitting. The question is: how do you choose the value of
the regularization hyperparameter? One option is to train 100 different models using
100 different values for this hyperparameter. Suppose you find the best hyperparame‐
ter value that produces a model with the lowest generalization error, say just 5% error.
So you launch this model into production, but unfortunately it does not perform as
well as expected and produces 15% errors. What just happened?
The problem is that you measured the generalization error multiple times on the test
set, and you adapted the model and hyperparameters to produce the best model for
that particular set. This means that the model is unlikely to perform as well on new
data.
<mark>A common solution to this problem is called holdout validation or for best use cross-validation</mark>


## End-to-end ml project
#### Select a Performance Measure
A typical performance measure for regression problems is the Root Mean Square Error (RMSE).
<mark>NOTE: suppose that there are many outlier districts. In that case, you may consider using the Mean Absolute Error (MAE)</mark>
• The higher the norm index, the more it focuses on large values and neglects small
ones. This is why the RMSE is more sensitive to outliers than the MAE.  ∥ · ∥k => norm.
But when outliers are exponentially rare (like in a bell-shaped curve), the RMSE performs
very well and is generally preferred.


#### Split test data
assume variable x is very important in predicting y, but x is skewed, so when you split data you may lose some of the data. so its better to split data using **stratified sampling based on x**


#### Feature Scaling
Machine Learning algorithms don’t perform well when the input numerical attributes have very different scales.
<mark>There are two common ways to get all attributes to have the same scale: min-max (normalization) scaling and standardization.</mark>
Min-max scaling (many people call this normalization) is quite simple: values are
shifted and rescaled so that they end up ranging from 0 to 1.
Unlike min-max scaling, standardizationdoes not bound values to a specific range, which may be a problem for some algorithms.
<mark>standardization is much less affected by outliers.</mark>



## Model Train
### under-fitting
the main ways to fix underfitting are to select a more powerful model, to feed the training algorithm with better features, or to reduce the constraints on the model.

### Better Evaluation Using Cross-Validation
One way to evaluate the Decision Tree model would be to use the train_test_split
function to split the training set into a smaller training set and a validation set, then train your models against the smaller training set and evaluate them against the vali‐
dation set. It’s a bit of work.
A great alternative is to use Scikit-Learn’s K-fold cross-validation feature

## Fine-Tune Your Model
### Grid Search
For example, the following code searches for the best combi‐
nation of hyperparameter values for the RandomForestRegressor:
```
param_grid = [
{'n_estimators': [3, 10, 30], 'max_features': [2, 4, 6, 8]},
{'bootstrap': [False], 'n_estimators': [3, 10], 'max_features': [2, 3, 4]},
]
forest_reg = RandomForestRegressor()
grid_search = GridSearchCV(forest_reg, param_grid, cv=5,
scoring='neg_mean_squared_error',
return_train_score=True)
grid_search.fit(housing_prepared, housing_labels)
```

Don’t forget that you can treat some of the data preparation steps as
hyperparameters. For example, the grid search will automatically
find out whether or not to add a feature you were not sure about
(e.g., using the add_bedrooms_per_room hyperparameter of your
CombinedAttributesAdder transformer). It may similarly be used
to automatically find the best way to handle outliers, missing fea‐
tures, feature selection, and more.

## Randomized Search
The grid search approach is fine when you are exploring relatively few combinations,
like in the previous example, but when the hyperparameter search space is large, it is
often preferable to use RandomizedSearchCV instead.