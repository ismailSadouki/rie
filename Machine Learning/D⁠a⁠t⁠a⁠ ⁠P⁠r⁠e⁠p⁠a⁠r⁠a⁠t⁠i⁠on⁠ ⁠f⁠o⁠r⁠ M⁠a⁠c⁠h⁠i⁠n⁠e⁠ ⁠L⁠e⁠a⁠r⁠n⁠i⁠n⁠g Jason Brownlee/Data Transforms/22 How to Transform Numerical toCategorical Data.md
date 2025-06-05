The discretization transform provides an automatic way to change a numeric input variable to have a different data distribution, which in turn can be used as input to a predictive model.
 Many machine learning algorithms prefer or perform better when numerical features with non-standard probability distributions are made discrete.


**Discretizing** means converting continuous numerical values into **categorical bins or intervals**. For example, turning age (a number) into categories like:

- 0–18 → "Child"
    
- 19–35 → "Young Adult"
    
- 36–60 → "Adult"
    
- 61+ → "Senior"

### 🔹 Why discretization can help

1. **Simplifies the structure for models**:
    
    - Tree-based models (like Decision Trees, Random Forests, and XGBoost) often benefit because they naturally split features into intervals.
        
    - Discretized features reduce the number of possible splits and can make the model more interpretable and faster to train.
        
2. **Reduces the impact of outliers**:
    
    - Outliers in numeric features can distort the learning process for algorithms like logistic regression, k-NN, or neural networks. Binning removes this sensitivity.
        
3. **Handles non-linearities better**:
    
    - Some algorithms assume linear relationships between features and the target. Discretization lets the model capture non-linear patterns more easily (e.g., by one-hot encoding the bins).
        
4. **Improves performance with sparse or irregular data**:
    
    - Features with clumped or uneven distributions can mislead the model. Discretizing into balanced or meaningful bins (like quantiles) can regularize the learning process.

 Discretization transforms are a technique for transforming numerical input or output variables to have discrete ordinal labels.
 How to use the KBinsDiscretizer to change the structure and distribution of numeric variables to improve the performance of predictive models.


# 22.2 Change Data Distribution
Some machine learning algorithms may prefer or require categorical or ordinal input variables, such as some decision tree and rule-based algorithms.
```
Some classification and clustering algorithms deal with nominal attributes only and cannot handle ones measured on a numeric scale.
```
Further, the performance of many machine learning algorithms degrades for variables that have non-standard probability distributions.
Some input variables may have a highly skewed distribution, such as an
exponential distribution where the most common observations are bunched together. Some input variables may have outliers that cause the distribution to be highly spread.

These concerns and others, like non-standard distributions and  multi-modal distributions, can make a dataset challenging to model with a range of machine learning models
> [!word] **multi-modal distributions**
> A **multi-modal distribution** is a probability distribution with **more than one "peak" or mode**..

As such, it is often desirable to transform each input variable to have a standard probability distribution.
One approach is to use a transform of the numerical variable to have a discrete probability distribution where each numerical value is assigned a label and the labels have an ordered (ordinal) relationship.
This is called a binning or a discretization transform and can improve the
performance of some machine learning models for datasets by making the probability distribution of numerical input variables discrete.

# 22.3 Discretization Transforms
```
Binning, also known as categorization or discretization, is the process of translating a quantitative variable into a set of two or more qualitative buckets (i.e., categories)
```
The use of bins is often referred to as binning or k-bins, where k refers to the number of groups to which a numeric variable is mapped.
The transformation can be applied to each numeric input variable in the training dataset and then provided as input to a machine learning model to learn a predictive modeling task.
```
The determination of the bins must be included inside of the resampling process.
```
Different methods for grouping the values into k discrete bins can be used; common techniques include:
 Uniform: Each bin has the same width in the span of possible values for the variable.
 Quantile: Each bin has the same number of values, split based on percentiles.
 Clustered: Clusters are identified and examples are assigned to each group.

The discretization transform is available in the scikit-learn Python machine learning library via the `KBinsDiscretizer` class.
The `strategy` argument controls the manner in which the input variable is divided, as either `‘uniform’`, `‘quantile’`, or `‘kmeans’`. The `n_bins` argument
controls the number of bins that will be created and must be set based on the choice of strategy, e.g. `‘uniform’` is flexible, `‘quantile’` must have a `n_bins` less than the number of observations or sensible percentiles, and `‘kmeans’` must use a value for the number of clusters that can be reasonably found.
The `encode` argument controls whether the transform will map each value to
an integer value by setting `‘ordinal’` or a one hot encoding `‘onehot’`.
An ordinal encoding is almost always preferred, although a one hot encoding may allow a model to learn non-ordinal relationships between the groups, such as in the case of k-means clustering strategy.

```
# discretization transform the raw data
kbins = KBinsDiscretizer(n_bins=10, encode='ordinal', strategy='uniform')
data_trans = kbins.fit_transform(data)
```


# 22.6 k-Means Discretization Transform
A k-means discretization transform will attempt to fit k clusters for each input variable and then assign each observation to a cluster. Unless the empirical distribution of the variable is complex, the number of clusters is likely to be small, such as 3-to-5.
```
# perform a kmeans discretization transform of the dataset
trans = KBinsDiscretizer(n_bins=3, encode='ordinal', strategy='kmeans')
data = trans.fit_transform(data)
```

# 22.7 Quantile Discretization Transform
A quantile discretization transform will attempt to split the observations for each input variable into k groups, where the number of observations assigned to each group is approximately equal. Unless there are a large number of observations or a complex empirical distribution, the number of bins must be kept small, such as 5-10.











We chose the number of bins as an arbitrary number; in this case, 10. This hyperparameter
can be tuned to explore the effect of the resolution of the transform on the resulting skill of the
model.


![](https://i.imgur.com/OgEptRx.png)
![](https://i.imgur.com/RJwuGC1.png)
