# Mapping ordinal features
To make sure that the learning algorithm interprets the ordinal features correctly, we need to convert the categorical string values into integers. Unfortunately, there is no convenient function that can automatically derive the correct order of the labels of our size feature, so we have to define the mapping manually. In the following simple example, let’s assume that we know the numerical difference 
between features, for example, XL = L + 1 = M + 2:
```
>>> size_mapping = {'XL': 3,
...'L': 2,
...'M': 1}
>>> df['size'] = df['size'].map(size_mapping)
```

![](https://i.imgur.com/tTWu4SX.png)





Other, more advanced methods for feature scaling are available from scikit-learn, such as RobustScaler. RobustScaler is especially helpful and recommended if we are working with small datasets that contain many outliers. Similarly, if the machine learning algorithm applied to this dataset is prone to overfitting, RobustScaler can be a good choice.