![](https://i.imgur.com/Yi3Y5WK.png)
# Preprocessing – getting data into shape
- Many machine learning algorithms also require that the selected features are on the same scale for optimal performance.

- Some of the selected features may be highly correlated and therefore redundant to a certain degree. In those cases, dimensionality reduction techniques are useful for compressing the features onto a lower-dimensional subspace. Reducing the dimensionality of our feature space has the advantage that less storage space is required, and the learning algorithm can run much faster. In certain cases, dimensionality reduction can also improve the predictive performance of a model if the dataset contains a large number of irrelevant features (or noise); that is, if the dataset has a low signal-to-noise ratio.
# Training and selecting a predictive model
- before we can compare different models, we first have to decide upon a metric
to measure performance. One commonly used metric is classification accuracy, which is defined as the proportion of correctly classified instances.
- we also cannot expect that the default parameters of the different learning algorithms provided by software libraries are optimal for our specific problem task. We can think of those hyperparameters as parameters that are not learned from the data but represent the knobs of a model that we can turn to improve its performance.
# Evaluating models and predicting unseen data instances
After we have selected a model that has been fitted on the training dataset, we can use the test dataset to estimate how well it performs on this unseen data to estimate the so-called <mark>generalization error</mark>.
. It is important to note that the parameters for the previously mentioned procedures, such as feature scaling and dimensionality reduction, are solely obtained from the training dataset, and the same parameters are later reapplied to transform the test dataset, as well as any new data instances—the performance measured on the test data may be overly optimistic otherwise.