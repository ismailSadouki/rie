This is a technique that comes from the field of linear algebra and can be used as a data preparation technique to create a projection of a sparse dataset prior to fitting a model.

Sparse data refers to rows of data where many of the values are zero. This is often the case in some problem domains like recommender systems where a user has a rating for very few movies or songs in the database and zero ratings for all other cases. Another common example is a bag-of-words model of a text document, where the document has a count or frequency for some words and most words have a 0 value. Examples of sparse data appropriate for applying
SVD for dimensionality reduction:
 Recommender Systems
 Customer-Product purchases
 User-Song Listen Counts
 User-Movie Ratings
 Text Classification
 One Hot Encoding
 Bag-of-Words Counts
 TF/IDF

SVD can be thought of as a projection method where data with m-columns (features) is projected into a subspace with m or fewer columns, whilst retaining the essence of the original data. The SVD is used widely both in the calculation of other matrix operations, such as matrix inverse, but also as a data reduction method in machine learning.
# 30.3 SVD Scikit-Learn API

```
...
data = ...
# define transform
svd = TruncatedSVD()
# prepare transform on dataset
svd.fit(data)
# apply transform to dataset
transformed = svd.transform(data)
```
Perhaps the best approach
is to use a Pipeline where the first step is the SVD transform and the next step is the learning
algorithm that takes the transformed data as input.
```
...
# define the pipeline
steps = [('svd', TruncatedSVD()), ('m', LogisticRegression())]
model = Pipeline(steps=steps)
```
It can also be a good idea to normalize data prior to performing the SVD transform if the
input variables have differing units or scales; for example:
```
...
# define the pipeline
steps = [('norm', MinMaxScaler()), ('svd', TruncatedSVD()), ('m', LogisticRegression())]
model = Pipeline(steps=steps)
```