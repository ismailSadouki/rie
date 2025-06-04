Your data may not have a Gaussian distribution and instead may have a Gaussian-like distribution (e.g. nearly Gaussian but with outliers or a skew) or a totally different distribution (e.g. exponential).
Power transforms like the Box-Cox transform and the Yeo-Johnson transform
provide an automatic way of performing these transforms on your data and are provided in the scikit-learn Python machine learning library.
These transforms are most effective when the data distribution is nearly-Gaussian to begin with and is afflicted with a skew or outliers.
```
Another common reason for transformations is to remove distributional skewness. An un-skewed distribution is one that is roughly symmetric. This means that the probability of falling on either side of the distribution’s mean is roughly equal
```

# 20.3 Power Transforms
This is often described as removing a skew in the distribution, although more generally is described as stabilizing the variance of the distribution.
```
The log transform is a specific example of a family of transformations known as power transforms. In statistical terms, these are variance-stabilizing transformations.
```