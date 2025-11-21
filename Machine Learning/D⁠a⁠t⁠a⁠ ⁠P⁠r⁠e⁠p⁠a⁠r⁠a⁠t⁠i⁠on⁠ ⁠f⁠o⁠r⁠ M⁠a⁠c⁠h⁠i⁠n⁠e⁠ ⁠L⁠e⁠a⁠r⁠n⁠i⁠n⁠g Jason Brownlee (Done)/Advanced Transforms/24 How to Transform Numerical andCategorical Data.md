Thankfully, the scikit-learn Python machine learning library provides the ColumnTransformer
that allows you to selectively apply data transforms to different columns in your dataset.

# 24.2 Challenge of Transforming Different Data Types
Sequences of different transforms can also be chained together using the Pipeline, such as imputing missing values, then scaling numerical values. For example:
```
# define pipeline
pipeline = Pipeline(steps=[('i', SimpleImputer(strategy='median')), ('s', MinMaxScaler())])
# transform training data
train_X = scaler.fit_transform(train_X)
```
It is very common to want to perform different data preparation techniques on different columns in your input data. For example, you may want to impute missing numerical values with a median value, then scale the values and impute missing categorical values using the most frequent value and one hot encode the categories.
# 24.3 How to use the ColumnTransformer
For example, it allows you to apply a specific transform or sequence of transforms to just the numerical columns, and a separate sequence of transforms to just the categorical columns.
To use the ColumnTransformer, you must specify a list of transformers. Each transformer is a three-element tuple that defines the name of the transformer, the transform to apply, and the column indices to apply it to. For example:
 (Name, Object, Columns)
For example, the ColumnTransformer below applies a OneHotEncoder to columns 0 and 1.
```
#Example of applying ColumnTransformer to encode categorical variables
transformer = ColumnTransformer(transformers=[('cat', OneHotEncoder(), [0, 1])])
```
The example below applies a SimpleImputer with median imputing for numerical columns 0 and 1, and SimpleImputer with most frequent imputing to categorical columns 2 and 3.
```
t = [('num', SimpleImputer(strategy='median'), [0, 1]), ('cat',
SimpleImputer(strategy='most_frequent'), [2, 3])]
transformer = ColumnTransformer(transformers=t)
```
Any columns not specified in the list of transformers are dropped from the dataset by default; this can be changed by setting the remainder argument. Setting remainder=‘passthrough’ will mean that all columns not specified in the list of transformers will be passed through without transformation, instead of being dropped.
```
# Example of using the ColumnTransformer to only encode categorical variables and pass through the rest.
transformer = ColumnTransformer(transformers=[('cat', OneHotEncoder(), [2, 3])], remainder='passthrough')
```

Once the transformer is defined, it can be used to transform a dataset. For example:
```
transformer = ColumnTransformer(transformers=[('cat', OneHotEncoder(), [0, 1])])
# transform training data
train_X = transformer.fit_transform(train_X)
```

A ColumnTransformer can also be used in a Pipeline to selectively prepare the columns of your dataset before fitting a model on the transformed data. This is the most likely use case as it ensures that the transforms are performed automatically on the raw data when fitting the model and when making predictions, such as when evaluating the model on a test dataset via cross-validation or making predictions on new data in the future. For example:
```
# define model
model = LogisticRegression()
# define transform
transformer = ColumnTransformer(transformers=[('cat', OneHotEncoder(), [0, 1])])
# define pipeline
pipeline = Pipeline(steps=[('t', transformer), ('m',model)])
# fit the model on the transformed data
model.fit(train_X, train_y)
# make predictions
yhat = model.predict(test_X)
```