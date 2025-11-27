First, we illustrate an implementation of regularized regression using the direct
engine glmnet. This will provide you with a strong sense of what is happening
with a regularized model. Realize there are other implementations available
(e.g., h2o, elasticnet, penalized). Then, in Section 6.4, we’ll demonstrate
how to apply a regularized model so we can properly compare it with our
previous predictive models.
The glmnet package is extremely eﬃcient and fast, even on very large data
sets (mostly due to its use of Fortran to solve the lasso problem via coordinate
descent); note, however, that it only accepts the non-formula XY interface
(2.3.1) so prior to modeling we need to separate our feature and target sets.

The following uses model.matrix to dummy encode our feature set (see
Matrix::sparse.model.matrix for increased eﬃciency on larger data sets).
We also log transform the response variable which is not required; however,
parametric models such as regularized regression are sensitive to skewed
response values so transforming can often improve predictive performance.

```r
# Create training feature matrices
# we use model.matrix(...)[, -1] to discard the intercept
X <- model.matrix(Sale_Price ~ ., ames_train)[, -1]
# transform y with log transformation
Y <- log(ames_train$Sale_Price)
```
To apply a regularized model we can use the glmnet::glmnet() function. The
alpha parameter tells glmnet to perform a ridge (alpha = 0), lasso (alpha
= 1), or elastic net (0 < alpha < 1) model. By default, glmnet will do two
things that you should be aware of:

1. Since regularized methods apply a penalty to the coeﬃcients, we
need to ensure our coeﬃcients are on a common scale. If not, then
predictors with naturally larger values (e.g., total square footage)
will be penalized more than predictors with naturally smaller values
(e.g., total number of rooms). By default, glmnet automatically
standardizes your features. If you standardize your predictors prior to
glmnet you can turn this argument oﬀ with standardize = FALSE.
2. glmnet will ﬁt ridge models across a wide range of 𝜆 value
```
# Apply ridge regression to attrition data
ridge <- glmnet(
x = X,
y = Y,
alpha = 0
)
plot(ridge, xvar = ”lambda”)
```
We can see the exact 𝜆 values applied with ridge$lambda. Although you can
specify your own 𝜆 values, by default glmnet applies 100 𝜆 values that are
data derived.
We can also access the coeﬃcients for a particular model using coef(). glmnet
stores all the coeﬃcients for each model in order of largest to smallest 𝜆.
Here we just peak at the two largest coeﬃcients (which correspond to Lati-
- [ ] tude & Overall_QualVery_Excellent) for the largest (289.0010) and smallest
(0.02791035) 𝜆 values. You can see how the largest 𝜆 value has pushed most of
these coeﬃcients to nearly 0.
```
# lambdas applied to penalty parameter
ridge$lambda %>% head()
## [1] 289 263 240 219 199 182
# small lambda results in large coefficients
coef(ridge)[c(”Latitude”, ”Overall_QualVery_Excellent”), 100]
##
##
Latitude Overall_QualVery_Excellent
0.389
0.127
# large lambda results in small coefficients
coef(ridge)[c(”Latitude”, ”Overall_QualVery_Excellent”), 1]
##
Latitude Overall_QualVery_Excellent
##
6.78e-36
9.72e-37
```
At this point, we do not understand how much improvement we are experiencing
in our loss function across various 𝜆 values.

# Tuning
Recall that 𝜆 is a tuning parameter that helps to control our model from
over-ﬁtting to the training data. To identify the optimal 𝜆 value we can use
k-fold cross-validation (CV). glmnet::cv.glmnet() can perform k-fold CV, and
by default, performs 10-fold CV. Below we perform a CV glmnet model with
both a ridge and lasso penalty separately:
```r
# Apply CV ridge regression to Ames data
ridge <- cv.glmnet(
x = X,
y = Y,
alpha = 0
)
# Apply CV lasso regression to Ames data
lasso <- cv.glmnet(
x = X,
y = Y,
alpha = 1
)
# plot results
par(mfrow = c(1, 2))
plot(ridge, main = ”Ridge penalty\n\n”)
plot(lasso, main = ”Lasso penalty\n\n”)
```
![](https://i.imgur.com/KvYdjhG.png)
So far we’ve implemented a pure ridge and pure lasso model. However, we
can implement an elastic net the same way as the ridge and lasso models, by
adjusting the alpha parameter. Any alpha value between 0–1 will perform an
elastic net. When alpha = 0.5 we perform an equal combination of penalties
whereas alpha < 0.5 will have a heavier ridge penalty applied and alpha > 0.5
will have a heavier lasso penalty.


Often, the optimal model contains an alpha somewhere between 0–1, thus we
want to tune both the 𝜆 and the alpha parameters. As in Chapters 4 and 5, we
can use the caret package to automate the tuning process. This ensures that
any feature engineering is appropriately applied within each resample. The
following performs a grid search over 10 values of the alpha parameter between
0–1 and ten values of the lambda parameter from the lowest to highest lambda
values identiﬁed by glmnet.
```
# for reproducibility
set.seed(123)
# grid search across
cv_glmnet <- train(
x = X,
y = Y,
method = ”glmnet”,
preProc = c(”zv”, ”center”, ”scale”),
trControl = trainControl(method = ”cv”, number = 10),
tuneLength = 10
)
# model with lowest RMSE
cv_glmnet$bestTune
##
alpha lambda
## 8
0.1 0.0469
# plot cross-validated RMSE
ggplot(cv_glmnet)
```