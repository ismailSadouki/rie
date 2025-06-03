
```
Many models, especially those based on regression slopes and intercepts, will estimate parameters for every term in the model. Because of this, the presence of non-informative variables can add uncertainty to the predictions and reduce the overall effectiveness of the model.
```
 Unsupervised Selection: Do not use the target variable (e.g. remove redundant
variables).
 Supervised Selection: Use the target variable (e.g. remove irrelevant variables).

Unsupervised feature selection techniques ignore the target variable, such as methods that remove redundant variables using correlation or features that have few values or low variance (i.e. data cleaning). Supervised feature selection techniques use the target variable, such as methods that remove irrelevant variables.

Supervised feature selection methods may further be classified into three groups:
 Intrinsic: Algorithms that perform automatic feature selection during training.
 Filter: Select subsets of features based on their relationship with the target.
 Wrapper: Search subsets of features that perform according to a predictive model.
```
Wrapper methods evaluate multiple models using procedures that add and/or remove predictors to find the optimal combination that maximizes model performance.
```
```
Filter feature selection methods use s`tatistical techniques to evaluate the relationship between each input variable and the target variable, and these scores are used as the basis to rank and choose those input variables that will be used in the model.
````
```
... some models contain built-in feature selection, meaning that the model will only include predictors that help maximize accuracy. In these cases, the model can pick and choose which representation of the data is best.
```

# 11.3 Statistics for Feature Selection
It is common to use correlation type statistical measures between input and output variables as the basis for filter feature selection.

The statistical measures used in filter-based feature selection are generally calculated one input variable at a time with the target variable. As such, they are referred to as **univariate statistical measures**. This may mean that any interaction between input variables is not considered in the filtering process.

```
Most of these techniques are univariate, meaning that they evaluate each predictor in isolation. In this case, the existence of correlated predictors makes it possible to select important, but redundant, predictors. The obvious consequences of this issue are that too many predictors are chosen and, as a result, collinearity problems arise.
```

![](https://i.imgur.com/HjZ4uc5.png)

### 11.3.1 Numerical Input, Numerical Output
The most common techniques are to use a correlation coefficient, such as Pearson’s for a linear correlation, or rank-based methods for a nonlinear correlation.
 Pearson’s correlation coefficient (linear).
 Spearman’s rank coefficient (nonlinear).
 Mutual Information.
### 11.3.2 Numerical Input, Categorical Output
 ANOVA correlation coefficient (linear).
 Kendall’s rank coefficient (nonlinear).
 Mutual Information.
Kendall does assume that the categorical variable is ordinal.
### 11.3.3 Categorical Input, Categorical Output
The most common correlation measure for categorical data is the chi-squared test. You can also use mutual information (information gain) from the field of information theory.
 Chi-Squared test (contingency tables).
 Mutual Information.
## 11.4 Feature Selection With Any Data Type

One approach to handling different input variable data types is to separately select numerical input variables and categorical input variables using appropriate metrics. This can be achieved using the ColumnTransformer class that will be introduced in Chapter
Another approach is to use a wrapper method that performs a search through different combinations or subsets of input features based on the effect they have on model quality.
Another approach is to use a wrapper method that performs a search through different
combinations or subsets of input features based on the effect they have on model quality. Simple methods might create a tree of all possible combinations of input features and navigate the graph based on the pay-off, e.g. using a best-first tree searching algorithm. Alternately, a stochastic global search algorithm can be used such as a genetic algorithm or simulated annealing.  Although effective, these approaches can be computationally very expensive, specially for large training datasets and sophisticated models.
 Tree-Searching Methods (depth-first, breadth-first, etc.).
 Stochastic Global Search (simulated annealing, genetic algorithm).
Simpler methods involve systematically adding or removing features from the model until no further improvement is seen.
 Step-Wise Models.
 RFE.

# 11.5 Common Questions
## Q. How Do You Filter Input Variables?
 Select the top k variables: SelectKBest.
 Select the top percentile variables: SelectPercentile.
## Q. How Can I Use Statistics for Other Data Types?
Consider transforming the variables in order to access different statistical methods. 














# Resources
- Applied Predictive Modeling