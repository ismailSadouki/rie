Standardization can become skewed or biased if the input variable contains outlier values.
To overcome this, the median and interquartile range can be used when standardizing numerical input variables, generally referred to as robust scaling.
# 18.2 Robust Scaling Data
If there are input variables that have very large values relative to the other input variables, these large values can dominate or skew some machine learning algorithms. The result is that the algorithms pay most of their attention to the large values and ignore the variables with smaller values.
This includes algorithms that use a weighted sum of inputs like linear regression, logistic regression, and artificial neural networks, as well as algorithms that use distance measures between examples, such as k-nearest neighbors and support vector machines. As such, it is normal to scale input variables to a common range as a data preparation technique prior to fitting a model.

One approach to standardizing input variables in the presence of outliers is to
ignore the outliers from the calculation of the mean and standard deviation, then use the calculated values to scale the variable. This is called robust standardization or robust data scaling. This can be achieved by calculating the median (50th percentile) and the 25th and 75th percentiles. The values of each variable then have their median subtracted and are divided by the interquartile range (IQR) which is the difference between the 75th and 25th percentiles
![](https://i.imgur.com/wWcMAxf.png)
The resulting variable has a zero mean and median and a standard deviation of 1, although not skewed by outliers and the outliers are still present with the same relative relationships to other values.

# 18.3 Robust Scaler Transforms 
The robust scaler transform is available in the scikit-learn Python machine learning library via the RobustScaler class. The with centering argument controls whether the value is centered to zero (median is subtracted) and defaults to True. The with scaling argument controls whether the value is scaled to the IQR (standard deviation set to one) or not and defaults to True.
```
# perform a robust scaler transform of the dataset
trans = RobustScaler()
data = trans.fit_transform(data)
```
# 18.6 Explore Robust Scaler Range
The range used to scale each variable is chosen by default as the IQR is bounded by the 25th and 75th percentiles. This is specified by the quantile range argument as a tuple. Other values can be specified and might improve the performance of the model, such as a wider range, allowing fewer values to be considered outliers, or a more narrow range, allowing more values to be considered outliers.