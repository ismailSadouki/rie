### 5.3 Identify Columns That Contain a Single Value
### 5.5 Consider Columns That Have Very Few Values
We can refer to these columns or predictors as near-zero variance predictors, as
their variance is not zero, but a very small number close to zero.
Depending on the choice of data preparation and modeling algorithms, variables with very few numerical values can also cause errors or unexpected results. For example, I have seen them cause errors when using power transforms for data preparation and when fitting linear models that assume a sensible data probability distribution.

### 5.6 Remove Columns That Have A Low Variance
A column that has a single value has a variance of 0.0, and a column that has very few unique values may have a small variance
The VarianceThreshold class from the scikit-learn library supports this as a type of feature selection.

### 5.7 Identify Rows That Contain Duplicate Data
if you are using a train/test split or k-fold cross-validation, then it is possible for a duplicate row or rows to appear in both train and test datasets and any evaluation of the model on these rows will be (or should be) correct. This will result in an optimistically biased estimate of performance on unseen data.