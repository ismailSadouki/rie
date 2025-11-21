


## 17.3.2 Data Standardization
Standardization assumes that your observations fit a Gaussian distribution with a well-behaved mean and standard deviation.You can still standardize your data if this expectation is not met, but you may not get reliable results.
```
Another [...] technique is to calculate the statistical mean and standard deviation of
the attribute values, subtract the mean from each value, and divide the result by
the standard deviation. This process is called standardizing a statistical variable
and results in a set of values whose mean is zero and standard deviation is one.
```

```
... it is emphasized that the statistics required for the transformation (e.g., the mean) are estimated from the training set and are applied to all data sets (e.g., the test set or new samples).
```


# Q. Should I Normalize or Standardize?
If the distribution of the quantity is normal, then it should be standardized, otherwise, the data should be normalized. This applies if the range of quantity values is large (10s, 100s, etc.) or small (0.01, 0.0001).
If the quantity values are small (near 0-1) and the distribution is limited (e.g. standard deviation near 1), then perhaps you can get away with no scaling of the data.

If in doubt, normalize the input sequence. If you have the resources, explore modeling with the raw data, standardized data, and normalized data and see if there is a beneficial difference in the performance of the resulting model.

```
If the input variables are combined linearly, as in an MLP [Multilayer Perceptron], then it is rarely strictly necessary to standardize the inputs, at least in theory. [...] However, there are a variety of practical reasons why standardizing the inputs can make training faster and reduce the chances of getting stuck in local optima.

— Should I normalize/standardize/rescale the data? Neural Nets FAQ.
```

# Q. Should I Standardize then Normalize?
Standardization can give values that are both positive and negative centered around zero. It may be desirable to normalize data after it has been standardized. This might be a good idea of you have a mixture of standardized and normalized variables and wish all input variables to have the same minimum and maximum values as input for a given algorithm, such as an algorithm that calculates distance measures.




17.8.1
Books
 Neural Networks for Pattern Recognition, 1995.
https://amzn.to/2S8qdwt
 Feature Engineering and Selection, 2019.
https://amzn.to/2Yvcupn
 Data Mining: Practical Machine Learning Tools and Techniques, 2016.
https://amzn.to/3bbfIAP
 Applied Predictive Modeling, 2