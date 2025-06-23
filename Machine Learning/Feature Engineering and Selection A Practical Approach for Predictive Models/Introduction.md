it is very important to realize that there are a multitude of types of models and that each has its own sensitivities and needs. For example:
• Some models cannot tolerate predictors that measure the same underlying
quantity (i.e., multicollinearity or correlation between predictors).
• Many models cannot use samples with any missing values.
• Some models are severely compromised when irrelevant predictors are in the
data.



## 1.1 Sample example
As a simple example of how feature engineering can affect models
![](https://i.imgur.com/iSrSMoc.png)
In this figure, there is clearly a diagonal separation between the two classes. A simple logistic regression model will be used here to create a prediction equation from these two variables. That model uses the following equation:
![](https://i.imgur.com/Qyj32ub.png)
A standard procedure (maximum likelihood estimation) is used to estimate the three regression parameters from the data. The authors used 1009 data points to estimate the parameters (i.e., a training set) and reserved 1010 samples strictly for estimating performance (a test set).5 Using the training set, the parameters were estimated to be βˆ0 = 1.73, βˆ1 = 0.003, and βˆ2 = −0.064.

Logistic regression naturally produces class probabilities that give an indication of likelihood for each class. While it is common to use a 50% cutoff to make hard class predictions, the performance derived from this default might be misleading. To avoid applying a probability cutoff, a technique called the receiver operating characteristic (ROC) curve is used here.6 The ROC curve evaluates the results on all possible cutoffs and plots the true positive rate versus the false positive rate.
A common summary value for this technique is to use the area under the ROC curve where a value of 1.0 corresponds a perfect model while values near 0.5 are indicative of a model with no predictive ability.


Given these two predictor variables, it would make sense to try different trans-
formations and encodings of these data in an attempt to increase the area under
the ROC curve.Since the predictors are both greater than zero and appear to
have right-skewed distributions, one might be inclined to take the ratio A/B and
enter only this term in the model.
Alternatively, we could also evaluate if simple transformations of each predictor would be helpful. One method is the Box-Cox transformation7 which uses a separate estimation procedure prior to the logistic regression model that can put the predictors on a new scale. Using this methodology, the Box-Cox estimation procedure recommended that both predictors should be used on the inverse scale (i.e., 1/A instead of A). This representation of the data is shown in Figure 1.3a. When these transformed values were entered into the logistic
regression model in lieu of the original values, the area under the ROC curve changed from 0.794 to 0.848, which is a substantial increase.

![](https://i.imgur.com/icRkNk8.png)



ifferent models have different requirements of the data. If the skewness
of the original predictors was the issue affecting the logistic regression model, other models exist that do not have the same sensitivity to this characteristic. For example, a neural network can also be used to fit these data without using the inverse transformation of the predictors.






#### Q1: Should You Use the ROC Curve to Choose the Best Threshold?
**if your goal is to improve classification performance**, especially:

- When **accuracy isn’t telling the full story** (like in imbalanced datasets).
    
- When **false positives and false negatives have different costs** (e.g., in medical diagnosis, fraud detection).

## Q2: Can Choosing a Different Threshold Increase Accuracy?

**Not always.**

### 🎯 If Your Goal Is Higher Accuracy:

- Changing the threshold **can increase accuracy**, **but not guaranteed**.
    
- Sometimes optimizing **F1-score**, **recall**, or **precision** is better than pure accuracy, depending on your use case.


# 1.2 | Important Concepts

...
Often, models that are very flexible (called “low-bias models” in Section 1.2.5) have a higher likelihood of overfitting the data. It is not difficult for these models to do extremely well on the data set used to create the model and, without some preventative mechanism, can easily fail to generalize to new data.

While models can overfit to the data points, such as with the housing data shown
above, feature selection techniques can overfit to the predictors. This occurs when a variable appears relevant in the current data set but shows no real relationship with the outcome once new data are collected. The risk of this type of overfitting is especially dangerous when the number of data points, denoted as n, is small and the number of potential predictors (p) is very large.

# 1.2.2 | Supervised and Unsupervised Procedures
Both supervised and unsupervised analyses are susceptible to overfitting but su-
pervised are particularly inclined to discovering erroneous patterns in the data for
predicting the outcome. In short, we can use these techniques to create a self-fulfilling predictive prophecy. For example, it is not uncommon for an analyst to conduct a supervised analysis of data to detect which predictors are significantly associated with the outcome. These significant predictors are then used in a visualization (such as a heatmap or cluster analysis) on the same data. Not surprisingly, the visual- ization reliably demonstrates clear patterns between the outcomes and predictors and appears to provide evidence of their importance. However, since the same data are shown, the visualization is essentially cherry picking the results that are only true for these data and which are unlikely to generalize to new data.

# 1.2.3 | No Free Lunch
is the idea that, without any specific knowledge of the problem or data at hand, no one predictive model can be said to be the best.

There have been experiments to judge which models tend to do better than others on average, notably Demsar (2006) and Fernandez-Delgado et al. (2014). These analyses show that some models have a tendency to produce the most accurate models but the rate of “winning” is not high enough to enact a strategy of “always use model X.”
# 1.2.4 | The Model versus the Modeling Process
Figure 1.4 shows an illustration of the overall process for creating a model for a typical problem.
![](https://i.imgur.com/5x36GIb.png)
The initial activity begins9 at marker (a) where exploratory data analysis is used to
investigate the data. After initial explorations, marker (b) indicates where early data analysis might take place. This could include evaluating simple summary measures or identifying predictors that have strong correlations with the outcome. The process might iterate between visualization and analysis until the modeler feels confident that the data are well understood. At milestone (c), the first draft for how the predictors will be represented in the models is created based on the previous analysis.
At this point, several different modeling methods might be evaluated with the initial feature set. However, many models can contain hyperparameters that require tuning. This is represented at marker (d). This represents four distinct models that are being evaluated but each one is evaluated multiple times over a set of candidate hyperparameter values.

Once the four models have been tuned, they are numerically
evaluated on the data to understand their performance characteristics (e). Summary measures for each model, such as model accuracy, are used to understand the level of difficulty for the problem and to determine which models appear to best suit the data.

Based on these results, more EDA can be conducted on the model results (f ), such as residual analysis. For the previous example of predicting the sale prices of houses, the properties that are poorly predicted can be examined to understand if there is any systematic issues with the model. As an example, there may be particular ZIP Codes that are difficult to accurately assess. Consequently, another round of feature engineering (g) might be used to compensate for these obstacles.

By this point, it may be apparent which models tend to work best for the problem at hand and another, more extensive, round of model tuning can be conducted on fewer models (h). After more tuning and modification of the predictor representation, the two candidate models (#2 and #4) have been finalized. These models can be evaluated on an external test set as a final “bake off” between the models (i). The final model is then chosen (j) and this fitted model will be used going forward to predict new samples or to make inferences.

# 1.2.5 | Model Bias and Variance
In statistics, bias is generally thought of as the degree in which something deviates from its true underlying value.

A model has high variance if small changes to the underlying data used to estimate the parameters cause a sizable change in those parameters (or in the structure of the model)
A few examples of models with low variance are linear regression, logistic regression, and <mark>partial least squares</mark>.

High-variance models include those that strongly rely on individual data points to define their parameters such as classification or regression trees, nearest neighbor models, and neural networks. To contrast low-variance and high-variance models, consider linear regression and, alternatively, nearest neighbor models. Linear regression uses all of the data to estimate slope parameters and, while it can be sensitive to outliers, it is much less sensitive than a nearest neighbor model.

Model bias reflects the ability of a model to conform to the underlying theoretical
structure of the data. A low-bias model is one that can be highly flexible and has the capacity to fit a variety of different shapes and patterns.
A high-bias model would be unable to estimate values close to their true theoretical counterparts. Linear methods often have high bias since, without modification, they cannot describe nonlinear patterns in the predictor variables. Tree-based models, support vector machines, neural networks, and others can be very adaptable to the data and have low bias.
neural networks, and others can be very adaptable to the data and have low bias.
---
### 🔍 What Does the Sentence Mean?

> _"Model bias reflects the ability of a model to conform to the underlying theoretical structure of the data."_

This means:

- If your model has **high bias**, it **fails to learn** the real relationship between inputs (X) and outputs (Y) — it's too **rigid**.
    
- If your model has **low bias**, it’s **more flexible** and better at **approximating complex patterns** in the data.
### 🎯 So the second sentence means:

> _"A low-bias model is one that can be highly flexible and has the capacity to fit a variety of different shapes and patterns."_

- That is, the model **doesn't assume too simple a structure**, and can adapt to the real data patterns.
    
- Examples: **Decision Trees, Random Forests, Neural Networks** — these are typically low-bias models (they're very flexible).
### ⚠️ But Warning: Flexibility Isn't Always Good!

- **Too much flexibility** (low bias) can lead to **overfitting** → the model fits the **noise** in the training data, not the real pattern.
    
- That’s why we balance **bias and variance**.
---

in order to achieve low bias, models tend to demonstrate high variance (and vice versa). The variance-bias trade-off is a common theme in statistics.

In many cases, models have parameters that control the flexibility of the model and thus affect the variance and bias properties of the results. Consider a simple sequence of data points such as a daily stock price. A moving average model would estimate the stock price on a given day by the average of the data points within a certain window of the day. The size of the window can modulate the variance and bias here. For a small window, the average is much more responsive to the data and has a high potential to match the underlying trend. However, it also inherits a high degree of sensitivity to those data in the window and this increases variance.Widening the window will average more points and will reduce the variance in the model but will also desensitize the model fit potential by risking over-smoothing the data (and thus increasing bias).

![](https://i.imgur.com/zmPOj8c.png)

Consider the example in Figure 1.5a. First, a simple three-point moving average is used (in green). This trend line is bumpy but does a good job of tracking the nonlinear trend in the data. The purple line shows the results of a standard linear regression model that includes a term for the predictor value and a term for the square of the predictor value.

Since the data points start low on the y-axis, reach an apex near a predictor value of 0.3 then decrease, a quadratic regression model would be a reasonable first attempt at modeling these data. This model is very smooth (showing low variance) but does not do a very good job of fitting the nonlinear trend seen in the data (i.e., high bias).

To accentuate this point further, the original data were “jittered” multiple times by
adding small amounts of random noise to their values. This was done twenty times and, for each version of the data, the same two models were fit to the jittered data. The fitted curves are shown in Figure 1.6. The moving average shows a significant degree of noise in the regression predictions but, on average, manages to track the data patterns well. The quadratic model was not confused by the extra noise and generated very similar (although inaccurate) model fits.
![](https://i.imgur.com/Kvh1CGN.png)

In a similar manner, models can have reduced performance due to irrelevant predictors causing excess model variation. Feature selection techniques improve models by reducing the unwanted noise of extra variables.


# 1.3 | A More Complex Example
The case study discussed here involves predicting the ridership on Chicago
“L” trains (i.e., the number of people entering a particular station on a daily basis).
![](https://i.imgur.com/xkjTLOG.png)
With the same feature set, tree-based models had the best performance while linear models had the worst results. Additionally, there is very little variation in RMSE results within a model type (i.e., the linear model results tend to be similar to each other).

In an effort to improve model performance, some time was spent deriving a second set of predictors that might be used to augment the original group of four. From this, 128 numeric predictors were identified that were lagged versions of the ridership at different stations.
For example, to predict the ridership one week in the future,
today’s ridership would be used as a predictor (i.e., a seven-day lag). This second
set of predictors had a beneficial effect overall but were especially helpful to linear models (see the x-axis value of {1, 2} in Figure 1.7). However, the benefit varied between models and model types.

Since the lag variables were important for predicting the outcome, more lag variables were created using lags between 8 and 14 days. Many of these variables show a strong correlation to the other predictors. However, models with predictor sets 1, 2, and 3 did not show much meaningful improvement above and beyond the previous set of models and, for some, the results were worse. One particular linear model suffered since this expanded set had a high degree of between-variable correlation. This situation is generally known as multicollinearity and can be particularly troubling for some models. Because this expanded group of lagged variables didn’t now show much benefit overall, it was not considered further.

When brainstorming which predictors could be added next, it seemed reasonable to think that weather conditions might affect ridership. To evaluate this conjecture, a fourth set of 18 predictors was calculated and used in models with the first two sets (labeled as {1, 2, 4}). Like the third set, the weather did not show any relevance to predicting train ridership.

After conducting exploratory data analysis of residual plots associated with models with sets 1 and 2, a fifth set of 49 binary predictors were developed to address days where the current best models did poorly. These predictors resulted in a substantial drop in model error and were retained

---
### 🧠 Why use these binary variables?

During residual analysis, the researchers noticed that the model consistently made large errors on certain types of days. To fix this, they:

1. **Identified** the types of days where the model's predictions were wrong.
    
2. **Created variables** that "flag" those days with a `1` if the condition is true, and `0` otherwise.
🎯 Examples of such binary "flag" variables:

| Variable Name          | What it Flags                         | Value Example (on a given date)       |
| ---------------------- | ------------------------------------- | ------------------------------------- |
| `is_weekend`           | Saturday or Sunday                    | `1` if weekend, else `0`              |
| `is_holiday`           | National or city holiday              | `1` on holidays, `0` otherwise        |
| `is_event_day`         | Special event (e.g., concert, sports) | `1` if event happens near the station |
| `bad_weather`          | Extreme weather (rain, snow, etc.)    | `1` if bad weather, else `0`          |
| `monday_after_holiday` | Often shows unique patterns           | `1` if Monday after a holiday         |

### 📉 Why did this help reduce model error?

These binary variables helped the model **adjust its prediction** on days when **human behavior is different** (like during holidays or severe weather), which **wasn't captured by other numeric predictors** like lagged ridership.
### 💡 Final insight:

By creating binary “flags” for specific scenarios where the model was struggling, the researchers gave the model **more contextual awareness**, leading to better predictions and lower error.

---
... Note that the improvement affected models differently and that, with feature sets 1, 2, and 5, the simple linear models yielded results that are on par with more complex modeling techniques.


<mark>The overall points that should be understood from this demonstration are:</mark>
1. When modeling data, there is almost never a single model fit or feature set
that will immediately solve the problem. The process is more likely to be a
campaign of trial and error to achieve the best results.
2. The effect of feature sets can be much larger than the effect of different models.
3. The interplay between models and features is complex and somewhat unpre-
dictable.
4. With the right set of predictors, is it common that many different types
of models can achieve the same level of performance. Initially, the linear
models had the worst performance but, in the end, showed some of the best
performance.


# 1.4 | Feature Selection
in the prev example The new predictors were not prospectively filtered for statistical significance prior to adding them to the model. This would be a supervised procedure and care must be taken to make sure that overfitting is not occurring.

In that example, it was demonstrated that some of the predictors have enough
underlying information to adequately predict the outcome (such as sets 1, 2, and
5). However, this collection of predictors might very well contain non-informative
variables and this might impact performance to some extent.
To whittle the predictor set to a smaller set that contains only the informative predictors, a supervised feature selection technique might be used. Additionally, there is the possibility that there are a small number of important predictors in sets 3 and 4 whose utility was not discovered because of all of the non-informative variables in these sets.


There are a number of different strategies for supervised feature selection that can be applied and these are discussed in Chapters 10 through 12. A distinction between search methods are how subsets are derived:
• Wrapper methods use an external search procedure to choose different sub-
sets of the whole predictor set to evaluate in a model. This approach sep-
arates the feature search process from the model fitting process. Examples
of this approach would be backwards or stepwise selection as well as genetic
algorithms.
• Embedded methods are models where the feature selection procedure occurs
naturally in the course of the model fitting process. Here an example would be
a simple decision tree where variables are selected when the model uses them
in a split. If a predictor is never used in a split, the prediction equation is
functionally independent of this variable and it has been selected out.

the main concern during feature selection is overfitting.

unsupervised selection methods can have a very positive effect on model
performance. Recall the Ames housing data. A property’s neighborhood might be a useful predictor in the model. Since most models require predictors to be represented as numbers, it is common to encode such data as dummy or indicator variables.
In this case, the single neighborhood predictor, with 28 possible values, is converted to a set of 27 binary variables that have a value of one when a property is in that neighborhood and zero otherwise. While this is a well-known and common approach, here it leads to cases where 2 of the neighborhoods have only one or two properties in these data, which is less than 1% of the overall set. With such a low frequency, such a predictor might have a detrimental effect on some models (such as linear regression) and removing them prior to building the model might be advisable.
