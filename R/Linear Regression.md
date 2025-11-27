```
library(dplyr)
library(ggplot2)
# for data manipulation
# for awesome graphics
# Modeling packages
library(caret)
# for cross-validation, etc.
# Model interpretability packages
library(vip)
# variable importance
```
With the Ames housing data, suppose we wanted to model a linear relationship
between the total above ground living space of a home (Gr_Liv_Area) and sale
price (Sale_Price). To perform an OLS regression model in R we can use the
lm() function:
```
model1 <- lm(sale_price ~ gr_liv_area, data = ames_train)
```
The coef() function extracts the estimated coeﬃcients from the model.
can also use summary()
```
summary(model1)
```
the RMSE of a linear model can be extracted using the sigma() function.

The t-statistics for such a test are nothing more than the estimated
coeﬃcients divided by their corresponding estimated standard errors (i.e., in
the output from summary(), t value = Estimate / Std. Error). The reported
t-statistics measure the number of standard deviations each coeﬃcient is away
from 0. Thus, large t-statistics (greater than two in absolute value, say) roughly
indicate statistical signiﬁcance at the 𝛼 = 0.05 level. The p-values for these
tests are also reported by summary() in the column labeled Pr(>|t|).

In R, multiple linear regression models can be ﬁt by separating all the features
of interest with a +:
```
(model2 <- lm(Sale_Price ~ Gr_Liv_Area + Year_Built,
data = ames_train))
```
Alternatively, we can use update() to update the model formula used in model1.
The new formula can use a . as shorthand for keep everything on either the left
or right hand side of the formula, and a + or - can be used to add or remove
terms from the original model, respectively. In the case of adding Year_Built
to model1, we could’ve used:
```
(model2 <- update(model1, . ~ . + Year_Built))
```
modeling interactions
lm(Sale_Price ~ Gr_Liv_Area + Year_Built + Gr_Liv_Area:Year_Built,
data = ames_train)


The code below creates a third model where
we use all features in our data set as main eﬀects (i.e., no interaction terms)
to predict Sale_Price.

```
model3 <- lm(Sale_Price ~ ., data = ames_train)

# print estimated coefficients in a tidy data frame
broom::tidy(model3)
```

# Assessing model accuaracy
We can use the caret::train() function to
train a linear model (i.e., method = ”lm”) using cross-validation (or a variety of
other validation methods). The beneﬁt of caret is that it provides built-in cross-
validation capabilities, whereas the lm() function does not2 . The following
code chunk uses caret::train() to reﬁt model1 using 10-fold cross-validation:
```r
set.seed(123)
# for reproducibility
(cv_model1 <- train(
form = Sale_Price ~ Gr_Liv_Area,
data = ames_train,
method = ”lm”,
trControl = trainControl(method = ”cv”, number = 10)
))

set.seed(123)
cv_model2 <- train(
Sale_Price ~ Gr_Liv_Area + Year_Built,
data = ames_train,
method = ”lm”,
trControl = trainControl(method = ”cv”, number = 10)
)
# model 3 CV
set.seed(123)

cv_model3 <- train(
Sale_Price ~ .,
data = ames_train,
method = ”lm”,
trControl = trainControl(method = ”cv”, number = 10)
)
# Extract out of sample performance measures
summary(resamples(list(
model1 = cv_model1,
model2 = cv_model2,
model3 = cv_model3
)))


```


# Model concerns
1. Linear relationship: Linear regression assumes a linear relationship be-
tween the predictor and the response variable. However, as discussed in Chapter
3, non-linear relationships can be made linear (or near-linear) by applying
transformations to the response and/or predictors. For example, Figure 4.3
illustrates the relationship between sale price and the year a home was built.
The left plot illustrates the non-linear relationship that exists. However, we
can achieve a near-linear relationship by log transforming sale price, although
some non-linearity still exists for older homes.

```r
# Plot 1: Non-transformed
p1 <- ggplot(ames_train, aes(x = year_built, y = sale_price)) +
  geom_point(size = 1, alpha = 0.4) +
  geom_smooth(se = FALSE) +
  scale_y_continuous(name = "Sale price", labels = scales::dollar) +
  xlab("Year built") +
  ggtitle("Non-transformed variables with a\nnon-linear relationship.")

# Plot 2: Log-transformed
p2 <- ggplot(ames_train, aes(x = year_built, y = sale_price)) +
  geom_point(size = 1, alpha = 0.4) +
  geom_smooth(method = "lm", se = FALSE) +
  scale_y_log10(name = "Sale price", labels = scales::dollar,
                breaks = c(100000, 200000, 300000, 400000)) +
  xlab("Year built") +
  ggtitle("Transforming variables can provide a\nnear-linear relationship.")

# Arrange side by side
gridExtra::grid.arrange(p1, p2, nrow = 1)


```
2. Constant variance among residuals: Linear regression assumes the
variance among error terms (𝜖1 , 𝜖2 , … , 𝜖u� ) are constant (this assumption is
referred to as homoscedasticity). If the error variance is not constant, the
p-values and conﬁdence intervals for the coeﬃcients will be invalid. Similar
to the linear relationship assumption, non-constant variance can often be
resolved with variable transformations or by including additional predictors.
For example, Figure 4.4 shows the residuals vs. predicted values for model1 and
model3. model1 displays a classic violation of constant variance as indicated
by the cone-shaped pattern. However, model3 appears to have near-constant
variance.
The broom::augment function is an easy way to add model results to each
observation (i.e. predicted values, residuals).
```r
df1 <- broom::augment(cv_model1$finalModel, data = ames_train)
p1 <- ggplot(df1, aes(.fitted, .resid)) +
geom_point(size = 1, alpha = .4) +
xlab(”Predicted values”) +
ylab(”Residuals”) +
ggtitle(”Model 1”, subtitle = ”Sale_Price ~ Gr_Liv_Area”)
df2 <- broom::augment(cv_model3$finalModel, data = ames_train)

p2 <- ggplot(df2, aes(.fitted, .resid)) +
geom_point(size = 1, alpha = .4) +
xlab(”Predicted values”) +
ylab(”Residuals”) +
ggtitle(”Model 3”, subtitle = ”Sale_Price ~ .”)
gridExtra::grid.arrange(p1, p2, nrow = 1)
```
![](https://i.imgur.com/b4i1PI1.png)
3. No autocorrelation: Linear regression assumes the errors are independent
and uncorrelated. If in fact, there is correlation among the errors, then the
estimated standard errors of the coeﬃcients will be biased leading to prediction
intervals being narrower than they should be. For example, the left plot in
Figure 4.5 displays the residuals (𝑦-axis) vs. the observation ID (𝑥-axis) for
model1. A clear pattern exists suggesting that information about 𝜖1 provides
information about 𝜖2 .
4. More observations than predictors: Although not an issue with the
Ames housing data, when the number of features exceeds the number of
observations (𝑝 > 𝑛), the OLS estimates are not obtainable. To resolve this
issue an analyst can remove variables one-at-a-time until 𝑝 < 𝑛. Although
pre-processing tools can be used to guide this manual approach (Kuhn and
Johnson, 2013, 43-47), it can be cumbersome and prone to errors. In Chapter
6 we’ll introduce regularized regression which provides an alternative to OLS
that can be used when 𝑝 > 𝑛.
5. No or little multicollinearity: Collinearity refers to the situation in
which two or more predictor variables are closely related to one another.
The presence of collinearity can pose problems in the OLS, since it can
be diﬃcult to separate out the individual eﬀects of collinear variables on
the response. In fact, collinearity can cause predictor variables to appear as
statistically insigniﬁcant when in fact they are signiﬁcant. This obviously leads
to an inaccurate interpretation of coeﬃcients and makes it diﬃcult to identify
inﬂuential predictors.
In ames, for example, Garage_Area and Garage_Cars are two variables that have
a correlation of 0.89 and both variables are strongly related to our response
variable (Sale_Price). Looking at our full model where both of these variables
are included, we see that Garage_Cars is found to be statistically signiﬁcant
but Garage_Area is not:
```
# fit with two strongly correlated variables
summary(cv_model3) %>%
broom::tidy() %>%
filter(term %in% c(”Garage_Area”, ”Garage_Cars”))
#
```
However, if we reﬁt the full model without Garage_Cars, the coeﬃcient estimate
for Garage_Area increases two fold and becomes statistically signiﬁcant.
```
set.seed(123)
mod_wo_Garage_Cars <- train(
Sale_Price ~ .,
data = select(ames_train, -Garage_Cars),
method = ”lm”,
trControl = trainControl(method = ”cv”, number = 10)
)
summary(mod_wo_Garage_Cars) %>%
broom::tidy() %>%
filter(term == ”Garage_Area”)
```

# Principal component regression
Performing PCR with caret is an easy extension from our previous model.
We simply specify method = ”pcr” within train() to perform PCA on all our
numeric predictors prior to ﬁtting the model. Often, we can greatly improve
performance by only using a small subset of all principal components as pre-
dictors. Consequently, you can think of the number of principal components as
a tuning parameter (see Section 2.5.3). The following performs cross-validated
PCR with 1, 2, … , 20 principal components
```
Note in the below example we use preProcess to remove near-zero variance
features and center/scale the numeric features. We then use method = ”pcr”.
This is equivalent to creating a blueprint as illustrated in Section 3.8.3 to
remove near-zero variance features, center/scale the numeric features, perform
PCA on the numeric features, then feeding that blueprint into train() with
method = ”lm”.
```


```r
set.seed(123)
cv_model_pcr <- train(
  sale_price ~ .,
  data = ames_train_prepped,
  method = "pcr",
  trControl = trainControl(method = "cv", number = 10),
  preProcess = c("zv", "center", "scale"),
  tuneLength = 20
)
cv_model_pcr$bestTune
ggplot(cv_model_pcr)
```
By controlling for multicollinearity with PCR, we can experience signiﬁcant
improvement in our predictive accuracy compared to the previously obtained
linear models

# Partial least squares
we can think of PLS as a
supervised dimension reduction procedure that ﬁnds new features that not
only captures most of the information in the original features, but also are
related to the response.
Similar to PCR, we can easily ﬁt a PLS model by changing the method argument
in train(). As with PCR, the number of principal components to use is a tuning

parameter that is determined by the model that maximizes predictive accuracy
(minimizes RMSE in this case). The following performs cross-validated PLS
with 1, 2, … , 20 PCs, and Figure 4.10 shows the cross-validated RMSEs. You
can see a greater drop in prediction error compared to PCR. Using PLS with
𝑚 = 3 principal components corresponded with the lowest cross-validated
RMSE of $29,970.

```
set.seed(123)
cv_model_pls <- train(
Sale_Price ~ .,
data = ames_train,
method = ”pls”,
trControl = trainControl(method = ”cv”, number = 10),
preProcess = c(”zv”, ”center”, ”scale”),
tuneLength = 20
)
# model with lowest RMSE
cv_model_pls$bestTune
##
ncomp
## 3
3
# plot cross-validated RMSE
ggplot(cv_model_pls)

```

# Feature interpretation
We can use vip::vip() to extract and plot the most important variables. The
importance measure is normalized from 100 (most important) to 0 (least
important). Figure 4.11 illustrates that the top 4 most important variables are
Gr_liv_Area, First_Flr_SF, Total_Bsmt_SF, and Garage_Cars respectively.
```
vip(cv_model_pls, num_features = 20, method = ”model”)
```
As stated earlier, linear regression models assume a monotonic linear relation-
ship. To illustrate this, we can construct partial dependence plots (PDPs).
PDPs plot the change in the average predicted value (𝑦)̂ as speciﬁed feature(s)
vary over their marginal distribution. As you will see in later chapters, PDPs
become more useful when non-linear relationships are present (we discuss
PDPs and other ML interpretation techniques in Chapter 16). However, PDPs
of linear models help illustrate how a ﬁxed change in 𝑥u� relates to a ﬁxed
linear change in 𝑦u�̂ while taking into account the average eﬀect of all the other
features in the model (for linear models, the slope of the PDP is equal to the
corresponding features LS coeﬃcient).


