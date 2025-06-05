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

These power transforms are available in the scikit-learn Python
machine learning library via the PowerTransformer class. The class takes an argument named method that can be set to ‘yeo-johnson’ or ‘box-cox’ for the preferred method.
It will also standardize the data automatically after the transform, meaning each variable will have a zero mean and unit variance. This can be turned off by setting the standardize argument to False.

```
# power transform the raw data
power = PowerTransformer(method='yeo-johnson', standardize=True)
data_trans = power.fit_transform(data)
```

# 20.5 Box-Cox Transform
```
It is important to note that the Box-Cox procedure can only be applied to data that is strictly positive.
```

```
pt = PowerTransformer(method='box-cox')
data = pt.fit_transform(data)
```

if there is negative of zero values use MinMaxScaler
```
scaler = MinMaxScaler(feature_range=(1, 2))
power = PowerTransformer(method='box-cox')
pipeline = Pipeline(steps=[('s', scaler),('p', power)])
data = pipeline.fit_transform(data)
```

# 20.6 Yeo-Johnson Transform
It supports zero values and negative values.
```
# perform a yeo-johnson transform of the dataset
pt = PowerTransformer(method='yeo-johnson')
data = pt.fit_transform(data)
```
Sometimes a lift in performance can be achieved by first standardizing the raw dataset prior to performing a Yeo-Johnson transform.