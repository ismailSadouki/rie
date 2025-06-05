Numerical input variables may have a highly skewed or non-standard distribution. This could be caused by outliers in the data, multi-modal distributions, highly exponential distributions, and more.
Many machine learning algorithms prefer or perform better when numerical input variables and even output variables in the case of regression have a standard probability distribution, such as a Gaussian (normal) or a uniform distribution.

The quantile transform provides an automatic way to transform a numeric input variable to have a different data distribution, which in turn, can be used as input to a predictive model.


Some algorithms, like linear regression and logistic regression, explicitly assume the real-valued variables have a Gaussian distribution. Other nonlinear algorithms may not have this assumption, yet often perform better when variables have a Gaussian distribution.

# 21.3 Quantile Transforms

A quantile transform will map a variable’s probability distribution to another probability distribution. Recall that a quantile function, also called a percent-point function (PPF), is the inverse of the cumulative probability distribution (CDF). The PPF is the inverse of this function and returns the value at or below a given probability.

rvations and can be mapped onto other distributions, such as the uniform or normal distribution. The transformation can be applied to each numeric input variable in the training dataset and then provided as input to a machine learning model to learn a predictive modeling task.

```
# quantile transform the raw data
# perform a normal quantile transform of the dataset
trans = QuantileTransformer(n_quantiles=100, output_distribution='normal')
data = trans.fit_transform(data)
```
We must also set the n quantiles argument to a value less than the number of observations in the training dataset.

# 21.6 Uniform Quantile Transform
Sometimes it can be beneficial to transform a highly exponential or multi-modal distribution to have a uniform distribution. This is especially useful for data with a large and sparse range of values, e.g. outliers that are common rather than rare.

<mark>You will never know sometimes uniform transform results in much higher accuracy than normal transform</mark>





We chose the number of quantiles as an arbitrary number, in this case, 100. This hyperparameter can be tuned to explore the effect of the resolution of the transform on the resulting skill of the model.

```
def get_models():
models = dict()
for i in range(1,100):
	# define the pipeline
	trans = QuantileTransformer(n_quantiles=i, output_distribution='uniform')
	model = KNeighborsClassifier()
	models[str(i)] = Pipeline(steps=[('t', trans), ('m', model)])
	return models
# evaluate a given model using cross-validation
def evaluate_model(model, X, y):
	cv = RepeatedStratifiedKFold(n_splits=10, n_repeats=3, random_state=1)
	scores = cross_val_score(model, X, y, scoring='accuracy', cv=cv, n_jobs=-1)
	return scores
# get the models to evaluate
models = get_models()
# evaluate the models and store results
results = list()
for name, model in models.items():
	scores = evaluate_model(model, X, y)
	results.append(mean(scores))
	print('>%s %.3f (%.3f)' % (name, mean(scores), std(scores)))
# plot model performance for comparison
pyplot.plot(results)
pyplot.show()
```
![](https://i.imgur.com/DY6AOlj.png)
![](https://i.imgur.com/t839bib.png)
<mark>The results highlight that there is likely some benefit in
exploring different distributions and number of quantiles to see if better performance can be achieved.</mark>
