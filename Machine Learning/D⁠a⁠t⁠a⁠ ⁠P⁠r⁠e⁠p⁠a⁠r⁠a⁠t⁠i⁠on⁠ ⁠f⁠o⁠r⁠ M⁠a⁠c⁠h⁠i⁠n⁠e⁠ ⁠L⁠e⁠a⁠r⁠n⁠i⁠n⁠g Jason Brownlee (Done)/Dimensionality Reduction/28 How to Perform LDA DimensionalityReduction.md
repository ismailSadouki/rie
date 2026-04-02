Linear Discriminant Analysis, or LDA for short, is a predictive modeling algorithm for multiclass classification. It can also be used as a dimensionality reduction technique, providing a projection of a training dataset that best separates the examples by their assigned class.

Linear Discriminant Analysis seeks to best separate (or discriminate) the samples in the training dataset by their class value.
Specifically, the model seeks to find a linear combination of input variables that achieves the maximum separation for samples between classes (class centroids or means) and the minimum separation of samples within each class.

```
... find the linear combination of the predictors such that the between-group variance was maximized relative to the within-group variance. [...] find the combination of the predictors that gave maximum separation between the centers of the data while at the same time minimizing the variation within each group of data.
```
it is good practice to perhaps standardize the data prior to fitting an LDA model.

# 28.3 LDA Scikit-Learn API
We can use LDA to calculate a projection of a dataset and select a number of dimensions or components of the projection to use as input to a model.
```
# prepare dataset
data = ...
# define transform
lda = LinearDiscriminantAnalysis()
# prepare transform on dataset
lda.fit(data)
# apply transform to dataset
transformed = lda.transform(data)
```

The outputs of the LDA can be used as input to train a model. Perhaps the best approach is to use a Pipeline where the first step is the LDA transform and the next step is the learning algorithm that takes the transformed data as input.
```
# define the pipeline
steps = [('lda', LinearDiscriminantAnalysis()), ('m', GaussianNB())]
model = Pipeline(steps=steps)

```
It can also be a good idea to standardize data prior to performing the LDA transform if the input variables have differing units or scales; for example:
```
# define the pipeline
steps = [('s', StandardScaler()), ('lda', LinearDiscriminantAnalysis()), ('m',
GaussianNB())]
model = Pipeline(steps=steps)
```


How do we know that reducing n dimensions of input down to m is good or the best we can do? We don’t; five was an arbitrary choice. A better approach is to evaluate the same transform and model with different numbers of input features and choose the number of features (amount of dimensionality reduction) that results in the best average performance. LDA is limited in the number of components used in the dimensionality reduction to between the number of
classes minus one

```
def get_models():
	models = dict()
	for i in range(1,10):
		steps = [('lda', LinearDiscriminantAnalysis(n_components=i)), ('m', GaussianNB())]
		models[str(i)] = Pipeline(steps=steps)
	return models
# evaluate a given model using cross-validation
def evaluate_model(model, X, y):
	cv = RepeatedStratifiedKFold(n_splits=10, n_repeats=3, random_state=1)
	scores = cross_val_score(model, X, y, scoring='accuracy', cv=cv, n_jobs=-1)
	return scores
# define dataset
X, y = get_dataset()
# get the models to evaluate
models = get_models()
# evaluate the models and store results
results, names = list(), list()
for name, model in models.items():
	scores = evaluate_model(model, X, y)
	results.append(scores)
	names.append(name)
	print('>%s %.3f (%.3f)' % (name, mean(scores), std(scores)))
# plot model performance for comparison
pyplot.boxplot(results, labels=names, showmeans=True)
pyplot.show()
```
![](https://i.imgur.com/Dz42XRs.png)

We can see a general trend of increased performance as the number of dimensions is increased.
On this dataset, the results suggest a trade-off in the number of dimensions vs. the classification
accuracy of the model. The results suggest using the default of nine components achieves the
best performance on this dataset, although with a gentle trade-off as fewer dimensions are used.