It can be thought of as a projection method where data with m-columns (features) is projected into a subspace with m or fewer columns, whilst retaining the essence of the original data.
```
PCA can be defined as the orthogonal projection of the data onto a lower dimensional linear space, known as the principal subspace, such that the variance of the projected data is maximized
```

## 29.3 PCA Scikit-Learn API
```
...
data = ...
# define transform
pca = PCA()
# prepare transform on dataset
pca.fit(data)
# apply transform to dataset
transformed = pca.transform(data)

```
Perhaps the best approach
is to use a Pipeline where the first step is the PCA transform and the next step is the learning
algorithm that takes the transformed data as input.
```
...
# define the pipeline
steps = [('pca', PCA()), ('m', LogisticRegression())]
model = Pipeline(steps=steps)
```
It can also be a good idea to normalize data prior to performing the PCA transform if the
input variables have differing units or scales; for example:
```
...
# define the pipeline
steps = [('norm', MinMaxScaler()), ('pca', PCA()), ('m', LogisticRegression())]
model = Pipeline(steps=steps)
```