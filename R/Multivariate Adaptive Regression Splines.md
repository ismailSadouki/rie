This chapter discusses multivariate adaptive regression splines (MARS) (Fried-
man, 1991), an algorithm that automatically creates a piecewise linear model
which provides an intuitive stepping block into nonlinearity after grasping
the concept of multiple linear regression.


# Multivariate adaptive regression splines
Multivariate adaptive regression splines (MARS) provide a convenient approach
to capture the nonlinearity relationships in the data by assessing cutpoints
(knots) similar to step functions. The procedure assesses each data point
for each predictor as a knot and creates a linear regression model with the
candidate feature(s). For example, consider our non-linear, non-monotonic
data above where 𝑌 = 𝑓 (𝑋). The MARS procedure will ﬁrst look for the
single point across the range of X values where two diﬀerent linear relationships
between Y and X achieve the smallest error (e.g., smallest SSE). What results
is known as a hinge function ℎ (𝑥 − 𝑎), where 𝑎 is the cutpoint value.
We can ﬁt a direct engine MARS model with the earth package (from
mda:mars by Trevor Hastie and utilities with Thomas Lumley’s leaps wrapper.,
2019). By default, earth::earth() will assess all potential knots across all
supplied features and then will prune to the optimal number of knots based
on an expected change in 𝑅2 (for the training data) of less than 0.001. This
calculation is performed by the Generalized cross-validation (GCV) proce-
dure, which is a computational shortcut for linear models that produces an
approximate leave-one-out cross-validation error metric (Golub et al., 1979).

The following applies a basic MARS model to our ames example. The results
show us the ﬁnal models GCV statistic, generalized 𝑅2 (GRSq), and more.'
```r
# Fit a basic MARS model
mars1 <- earth(
Sale_Price ~ .,
data = ames_train
)
# Print model summary
print(mars1)
```
It also shows us that 36 of 41 terms were used from 24 of the 307 original
predictors. But what does this mean? If we were to look at all the coeﬃcients,
we would see that there are 36 terms in our model (including the intercept).
These terms include hinge functions produced from the original 307 predictors
(307 predictors because the model automatically dummy encodes categorical
features). Looking at the ﬁrst 10 terms in our model, we see that Gr_Liv_Area
is included with a knot at 2790 (the coeﬃcient for ℎ (2790 − Gr_Liv_Area) is
-55.26), Year_Built is included with a knot at 2002, etc.

You can check out all the coeﬃcients with summary(mars1) or coef(mars1).

```
summary(mars1) %>% .$coefficients %>% head(10)
```
In addition to pruning the number of knots, earth::earth() allows us to also
assess potential interactions between diﬀerent hinge functions. The following
illustrates this by including a degree = 2 argument. You can see that now our
model includes interaction terms between a maximum of two hinge functions
(e.g., h(Year_Built-2002)*h(2362-Gr_Liv_Area) represents an interaction eﬀect
for those houses built prior to 2002 and had less than 2,362 square feet of
living space above ground).
```r
# Fit a basic MARS model
mars2 <- earth(
Sale_Price ~ .,
data = ames_train,
degree = 2
)
# check out the first 10 coefficient terms
summary(mars2) %>% .$coefficients %>% head(10)
```
There are two important tuning parameters associated with our MARS model:
the maximum degree of interactions and the number of terms retained in
the ﬁnal model. We need to perform a grid search to identify the optimal
combination of these hyperparameters that minimize prediction error (the
above pruning process was based only on an approximation of CV model
performance on the training data rather than an exact k-fold CV process). As
in previous chapters, we’ll perform a CV grid search to identify the optimal
hyperparameter mix. Below, we set up a grid that assesses 30 diﬀerent combi-
nations of interaction complexity (degree) and the number of terms to retain
in the ﬁnal model (nprune).
```
Rarely is there any beneﬁt in assessing greater than 3-rd degree interactions
and we suggest starting out with 10 evenly spaced values for nprune and then
you can always zoom in to a region once you ﬁnd an approximate optimal
solution.
```

```r
# create a tuning grid
hyper_grid <- expand.grid(
degree = 1:3,
nprune = seq(2, 100, length.out = 10) %>% floor()
)
head(hyper_grid)
```
As in the previous chapters, we can use caret to perform a grid search using
10-fold CV. The model that provides the optimal combination includes third
degree interaction eﬀects and retains 45 terms. The cross-validated RMSE for
these models is displayed in Figure 7.4; the optimal model’s cross-validated
RMSE was $22,888.
```r
# Cross-validated model
set.seed(123) # for reproducibility
cv_mars <- train(
x = subset(ames_train, select = -Sale_Price),
y = ames_train$Sale_Price,
method = ”earth”,
metric = ”RMSE”,
trControl = trainControl(method = ”cv”, number = 10),
tuneGrid = hyper_grid
)
# View results
cv_mars$bestTune
##
nprune degree
## 25
45
3
ggplot(cv_mars)
```
The above grid search helps to focus where we can further reﬁne our model
tuning. As a next step, we could perform a grid search that focuses in on a
reﬁned grid space for nprune (e.g., comparing 35–45 terms retained). However,
for brevity we’ll leave this as an exercise for the reader.

# Feature interpretation
