To illustrate logistic regression concepts we’ll use the employee attrition data,
where our intent is to predict the Attrition response variable (coded as
”Yes”/”No”). As in the previous chapter, we’ll set aside 30% of our data as a
test set to assess our generalizability error.
```r
df <- attrition %>% mutate_if(is.ordered, factor, ordered = FALSE)
# Create training (70%) and test (30%) sets for the
# rsample::attrition data.
set.seed(123) # for reproducibility
churn_split <- initial_split(df, prop = .7, strata = ”Attrition”)
churn_train <- training(churn_split)
churn_test <- testing(churn_split)
```
We will ﬁt two logistic regression models in order to predict the probability of an
employee attriting. The ﬁrst predicts the probability of attrition based on their
monthly income (MonthlyIncome) and the second is based on whether or not
the employee works overtime (OverTime). The glm() function ﬁts generalized
linear models, a class of models that includes both logistic regression and
simple linear regression as special cases. The syntax of the glm() function
is similar to that of lm(), except that we must pass the argument family =
”binomial” in order to tell R to run a logistic regression rather than some other
type of generalized linear model (the default is family = ”gaussian”, which is
equivalent to ordinary linear regression assuming normally distributed errors).
```

model1 <- glm(Attrition ~ MonthlyIncome, family = "binomial",
              data = churn_train)
model2 <- glm(Attrition ~ OverTime, family = "binomial",
              data = churn_train)

tidy(model1)
```
As discussed earlier, it is easier to interpret the coeﬃcients using an exp()
transformation:
```
exp(coef(model1))
```
Thus, the odds of an employee attriting in model1 increase multiplicatively by
1 for every one dollar increase in MonthlyIncome, whereas the odds of attriting
in model2 increase multiplicatively by 4.081 for employees that work OverTime
compared to those that do not.
Many aspects of the logistic regression output are similar to those discussed
for linear regression. For example, we can use the estimated standard errors to
get conﬁdence intervals as we did for linear regression in Chapter 4:
```
confint(model1)
```
# Multiple logistic regression
```
model3 <- glm(
Attrition ~ MonthlyIncome + OverTime,
family = ”binomial”,
data = churn_train
)
tidy(model3)
```


# Assessing model accuracy
we’ll use caret::train() and ﬁt three 10-fold cross
validated logistic regression models. Extracting the accuracy measures (in this
case, classiﬁcation accuracy), we see that both cv_model1 and cv_model2 had
an average accuracy of 83.88%. However, cv_model3 which used all predictor
variables in our data achieved an average accuracy rate of 87.58%.

```
set.seed(123)
cv_model1 <- train(
Attrition ~ MonthlyIncome,
data = churn_train,
method = ”glm”,
family = ”binomial”,
trControl = trainControl(method = ”cv”, number = 10)
)
set.seed(123)
cv_model2 <- train(
Attrition ~ MonthlyIncome + OverTime,
data = churn_train,
method = ”glm”,
family = ”binomial”,
trControl = trainControl(method = ”cv”, number = 10)
)
set.seed(123)
cv_model3 <- train(
Attrition ~ .,
data = churn_train,
method = ”glm”,
family = ”binomial”,
trControl = trainControl(method = ”cv”, number = 10)
)
# extract out of sample performance measures
summary(
resamples(
list(
model1 = cv_model1,
model2 = cv_model2,
model3 = cv_model3
)
)
)$statistics$Accuracy
```

We can get a better understanding of our model’s performance by assessing
the confusion matrix (see Section 2.6). We can use caret::confusionMatrix()
to compute a confusion matrix. We need to supply our model’s predicted class
and the actuals from our training data. The confusion matrix provides a wealth
of information. Particularly, we can see that although we do well predicting
cases of non-attrition (note the high speciﬁcity), our model does particularly
poor predicting actual cases of attrition (note the low sensitivity).
```
By default the predict() function predicts the response class for a caret
model; however, you can change the type argument to predict the probabilities
(see ?caret::predict.train).
```


```
# predict class
pred_class <- predict(cv_model3, churn_train)
# create confusion matrix
confusionMatrix(
data = relevel(pred_class, ref = ”Yes”),
reference = relevel(churn_train$Attrition, ref = ”Yes”)
)
```

Similar to linear regression, we can perform a PLS logistic regression to
assess if reducing the dimension of our numeric predictors helps to improve
accuracy. There are 16 numeric features in our data set so the following code
performs a 10-fold cross-validated PLS model while tuning the number of
principal components to use from 1–16. The optimal model uses 14 principal
components, which is not reducing the dimension by much. However, the mean
accuracy of 0.876 is no better than the average CV accuracy of cv_model3
(0.876).
```
# Perform 10-fold CV on a PLS model tuning the number of PCs to
# use as predictors
set.seed(123)
cv_model_pls <- train(
Attrition ~ .,
data = churn_train,
method = ”pls”,
family = ”binomial”,
trControl = trainControl(method = ”cv”, number = 10),
preProcess = c(”zv”, ”center”, ”scale”),
tuneLength = 16
)
Model with lowest RMSE
cv_model_pls$bestTune
##
ncomp
## 14
14
# Plot cross-validated RMSE
ggplot(cv_model_pls)
```

# Model concerns
