[-3](##7.5 Selecting predictors)

## 7.1 The linear mode

#### PairPlot scatterplot matrix
```
us_change |>
  GGally::ggpairs(columns = 2:6)
```

---

## 7.3 Evaluating the regression model
![](https://i.imgur.com/8kE67I5.png)

After selecting the regression variables and fitting a regression model, it is necessary to plot the residuals to check that the assumptions of the model have been satisfied.
##### ACF plot of residuals[](https://otexts.com/fpp3/regression-evaluation.html#acf-plot-of-residuals)
With time series data, it is highly likely that the value of a variable observed in the current time period will be similar to its value in the previous period, or even the period before that, and so on. Therefore when fitting a regression model to time series data, it is common to find autocorrelation in the residuals. In this case, the estimated model violates the assumption of no autocorrelation in the errors, and our forecasts may be inefficient — there is some information left over which should be accounted for in the model in order to obtain better forecasts. The forecasts from a model with autocorrelated errors are still unbiased, and so they are not “wrong”, but they will usually have larger prediction intervals than they need to. Therefore we should always look at an ACF plot of the residuals.
##### Histogram of residuals
It is always a good idea to check whether the residuals are normally distributed.
this is not essential for forecasting, but it does make the calculation of prediction intervals much easier.

Using the `gg_tsresiduals()` function introduced in Section [5.3](https://otexts.com/fpp3/residuals.html#residuals), we can obtain all the useful residual diagnostics mentioned above.
```
fit_consMR |> gg_tsresiduals()
```
![](https://i.imgur.com/OzrutQ1.png)
```
augment(fit_consMR) |>
  features(.innov, ljung_box, lag = 10)
#> # A tibble: 1 × 3
#>   .model lb_stat lb_pvalue
#>   <chr>    <dbl>     <dbl>
#> 1 tslm      18.9    0.0420
```
The time plot shows some changing variation over time, but is otherwise relatively unremarkable. This heteroscedasticity will potentially make the prediction interval coverage inaccurate.
The autocorrelation plot shows a significant spike at lag 7, and a significant Ljung-Box test at the 5% level. However, the autocorrelation is not particularly large, and at lag 7 it is unlikely to have any noticeable impact on the forecasts or the prediction intervals. In Chapter [10](https://otexts.com/fpp3/dynamic.html#dynamic) we discuss dynamic regression models used for better capturing information left in the residuals.
##### Residual plots against predictors
We would expect the residuals to be randomly scattered without showing any systematic patterns.
If these scatterplots show a pattern, then the relationship may be nonlinear and the model will need to be modified accordingly.
It is also necessary to plot the residuals against any predictors that are _not_ in the model. If any of these show a pattern, then the corresponding predictor may need to be added to the model (possibly in a nonlinear form).

```
us_change |>
  left_join(residuals(fit_consMR), by = "Quarter") |>
  pivot_longer(Income:Unemployment,
               names_to = "regressor", values_to = "x") |>
  ggplot(aes(x = x, y = .resid)) +
  geom_point() +
  facet_wrap(. ~ regressor, scales = "free_x") +
  labs(y = "Residuals", x = "")
```
![](https://i.imgur.com/75dGLD5.png)

##### Residual plots against fitted values
A plot of the residuals against the fitted values should also show no pattern. If a pattern is observed, there may be “heteroscedasticity” in the errors which means that the variance of the residuals may not be constant. If this problem occurs, a transformation of the forecast variable such as a logarithm or square root may be required
```
augment(fit_consMR) |>
  ggplot(aes(x = .fitted, y = .resid)) +
  geom_point() + labs(x = "Fitted", y = "Residuals")
```
![](https://i.imgur.com/kwopDjy.png)

##### Outliers and influential observations[](https://otexts.com/fpp3/regression-evaluation.html#outliers-and-influential-observations)
Observations that have a large influence on the estimated coefficients of a regression model are called **influential observations**. Usually, influential observations are also outliers that are extreme in the x direction.
A scatter plot of y against each x is always a useful starting point in regression analysis, and often helps to identify unusual observations.
##### Spurious regression
For example, consider the two variables plotted in Figure [7.12](https://otexts.com/fpp3/regression-evaluation.html#fig:spurious). These appear to be related simply because they both trend upwards in the same manner. However, air passenger traffic in Australia has nothing to do with rice production in Guinea.
![](https://i.imgur.com/KDpmzTC.png)
Regressing non-stationary time series can lead to spurious regressions. The output of regressing Australian air passengers on rice production in Guinea is shown in Figure [7.13](https://otexts.com/fpp3/regression-evaluation.html#fig:tslmspurious2). High R2 and high residual autocorrelation can be signs of spurious regression
Cases of spurious regression might appear to give reasonable short-term forecasts, but they will generally not continue to work into the future.

```
fit <- aus_airpassengers |>
  filter(Year <= 2011) |>
  left_join(guinea_rice, by = "Year") |>
  model(TSLM(Passengers ~ Production))
report(fit)
#> Series: Passengers 
#> Model: TSLM 
#> 
#> Residuals:
#>    Min     1Q Median     3Q    Max 
#> -5.945 -1.892 -0.327  1.862 10.421 
#> 
#> Coefficients:
#>             Estimate Std. Error t value Pr(>|t|)    
#> (Intercept)    -7.49       1.20   -6.23  2.3e-07 ***
#> Production     40.29       1.34   30.13  < 2e-16 ***
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> Residual standard error: 3.24 on 40 degrees of freedom
#> Multiple R-squared: 0.958,   Adjusted R-squared: 0.957
#> F-statistic:  908 on 1 and 40 DF, p-value: <2e-16
```

```
fit |> gg_tsresiduals()
```
![](https://i.imgur.com/d81rjVV.png)


---

## 7.4 Some useful predictors
##### Trend
It is common for time series data to be trending. A linear trend can be modelled by simply using x1,t=t as a predictor, $$y_t=β_0+β_1t+ε_t$$
where t=1,…,T. A trend variable can be specified in the `TSLM()` function using the `trend()` special.
##### Dummy variables
A dummy variable can also be used to account for an **outlier** in the data. Rather than omit the outlier, a dummy variable removes its effect. In this case, the dummy variable takes value 1 for that observation and 0 everywhere else. An example is the case where a special event has occurred. For example when forecasting tourist arrivals to Brazil, we will need to account for the effect of the Rio de Janeiro summer Olympics in 2016.
`TSLM()` will automatically handle this case if you specify a factor variable as a predictor. There is usually no need to manually create the corresponding dummy variables.
##### Seasonal dummy variables[](https://otexts.com/fpp3/useful-predictors.html#seasonal-dummy-variables)
![](https://i.imgur.com/hCoigIb.png)
for quarterly data, use three dummy variables; for monthly data, use 11 dummy variables; and for daily data, use six dummy variables, and so on.
The interpretation of each of the coefficients associated with the dummy variables is that it is _a measure of the effect of that category relative to the omitted category_.
The `TSLM()` function will automatically handle this situation if you specify the special `season()`.

##### Intervention variables[](https://otexts.com/fpp3/useful-predictors.html#intervention-variables)
It is often necessary to model interventions that may have affected the variable to be forecast.
When the effect lasts only for one period, we use a “spike” variable. This is a dummy variable that takes value one in the period of the intervention and zero elsewhere. A spike variable is equivalent to a dummy variable for handling an outlier.
Other interventions have an immediate and permanent effect. If an intervention causes a level shift (i.e., the value of the series changes suddenly and permanently from the time of intervention), then we use a “step” variable. A step variable takes value zero before the intervention and one from the time of intervention onward.
Another form of permanent effect is a change of slope. Here the intervention is handled using a piecewise linear trend; a trend that bends at the time of intervention and hence is nonlinear.

##### Trading days
The number of trading days in a month can vary considerably and can have a substantial effect on sales data. To allow for this, the number of trading days in each month can be included as a predictor.
![](https://i.imgur.com/6BiJsYs.png)

##### Distributed lags[](https://otexts.com/fpp3/useful-predictors.html#distributed-lags)
It is often useful to include advertising expenditure as a predictor. However, since the effect of advertising can last beyond the actual campaign, we need to include lagged values of advertising expenditure.
![](https://i.imgur.com/IjqnJSo.png)
##### Easter
##### Fourier series
a series of sine and cosine terms of the right frequencies can approximate any periodic function. We can use them for seasonal patterns.

An alternative to using seasonal dummy variables, especially for long seasonal periods, is to use Fourier terms.
![](https://i.imgur.com/h06lefs.png)
With Fourier terms, we often need fewer predictors than with dummy variables, especially when m is large. This makes them useful for weekly data, for example, where m≈52. For short seasonal periods (e.g., quarterly data), there is little advantage in using Fourier terms over seasonal dummy variables.

---

## 7.5 Selecting predictors[](https://otexts.com/fpp3/selecting-predictors.html#selecting-predictors)
A common approach that is _not recommended_ is to plot the forecast variable against a particular predictor and if there is no noticeable relationship, drop that predictor from the model. This is invalid because it is not always possible to see the relationship from a scatterplot, especially when the effects of other predictors have not been accounted for.
Another common approach which is also invalid is to do a multiple linear regression on all the predictors and disregard all variables whose p-values are greater than 0.05. To start with, statistical significance does not always indicate predictive value. Even if forecasting is not the goal, this is not a good strategy because the p-values can be misleading when two or more predictors are correlated with each other
Instead, we will use a measure of predictive accuracy. Five such measures are introduced in this section.
```
glance(fit_consMR) |>
  select(adj_r_squared, CV, AIC, AICc, BIC)
#> # A tibble: 1 × 5
#>   adj_r_squared    CV   AIC  AICc   BIC
#>           <dbl> <dbl> <dbl> <dbl> <dbl>
#> 1         0.763 0.104 -457. -456. -437.
```
We compare these values against the corresponding values from other models. For the CV, AIC, AICc and BIC measures, we want to find the model with the lowest value; for Adjusted R2, we seek the model with the highest value.
##### Adjusted R^2
R2 value is not a good measure of the predictive ability of a model. It measures how well the model fits the historical data, but not how well the model will forecast future data.
In addition, R2 does not allow for “degrees of freedom”. Adding _any_ variable tends to increase the value of R2, even if that variable is irrelevant.
An alternative which is designed to overcome these problems is the adjusted R2
![](https://i.imgur.com/XUSE4tV.png)
where T is the number of observations and k is the number of predictors.
Maximising ¯R2 is equivalent to minimising the standard error $\hat{σ_e}$ given in Equation[](https://otexts.com/fpp3/least-squares.html#eq:Regr-se)
Maximising adjR2 works quite well as a method of selecting predictors, although it does tend to err on the side of selecting too many predictors.
##### Cross-validation
For regression models, it is also possible to use classical leave-one-out cross-validation to select predictors
This is faster and makes more efficient use of the data. The procedure uses the following steps:
![](https://i.imgur.com/Djj5ozY.png)
Although this looks like a time-consuming procedure, there are fast methods of calculating CV, so that it takes no longer than fitting one model to the full data set. The equation for computing CV efficiently is given in Section [7.9](https://otexts.com/fpp3/regression-matrices.html#regression-matrices). Under this criterion, the best model is the one with the smallest value of CV.
##### Akaike’s Information Criterion
![](https://i.imgur.com/DJFaOfS.png)
The idea here is to penalise the fit of the model (SSE) with the number of parameters that need to be estimated.

The model with the minimum value of the AIC is often the best model for forecasting. For large values of T , minimising the AIC is equivalent to minimising the CV value.
##### Corrected Akaike’s Information Criterion[](https://otexts.com/fpp3/selecting-predictors.html#corrected-akaikes-information-criterion)
![](https://i.imgur.com/MI683Pn.png)
##### Schwarz’s Bayesian Information Criterion[](https://otexts.com/fpp3/selecting-predictors.html#schwarzs-bayesian-information-criterion)
![](https://i.imgur.com/2LCT2H7.png)
As with the AIC, minimising the BIC is intended to give the best model. The model chosen by the BIC is either the same as that chosen by the AIC, or one with fewer terms. This is because the BIC penalises the number of parameters more heavily than the AIC. For large values of T, minimising BIC is similar to leave-v-out cross-validation when v=T[1−1/(log(T)−1)].
##### Which measure should we use?
While adjR2 is widely used, and has been around longer than the other measures, its tendency to select too many predictor variables makes it less suitable for forecasting.
Many statisticians like to use the BIC because it has the feature that if there is a true underlying model, the BIC will select that model given enough data. However, in reality, there is rarely, if ever, a true underlying model, and even if there was a true underlying model, selecting that model will not necessarily give the best forecasts (because the parameter estimates may not be accurate).

Consequently, we recommend that one of the AICc, AIC, or CV statistics be used, each of which has forecasting as their objective. If the value of T is large enough, they will all lead to the same model. In most of the examples in this book, we use the AICc value to select the forecasting model.
#### Example
In the multiple regression example for forecasting US consumption we considered four predictors. With four predictors, there are 24=16 possible models. Now we can check if all four predictors are actually useful, or whether we can drop one or more of them. All 16 models were fitted and the results are summarised in Table [7.1](https://otexts.com/fpp3/selecting-predictors.html#tab:tblusMR). A “⬤” indicates that the predictor was included in the model. Hence the first row shows the measures of predictive accuracy for a model including all four predictors.
The results have been sorted according to the AICc. Therefore the best models are given at the top of the table, and the worst at the bottom of the table.
![](https://i.imgur.com/TIX3vyR.png)

##### Stepwise regression
If there are a large number of predictors, it is not possible to fit all possible models.
An approach that works quite well is _backwards stepwise regression_:

- Start with the model containing all potential predictors.
- Remove one predictor at a time. Keep the model if it improves the measure of predictive accuracy.
- Iterate until no further improvement.
If the number of potential predictors is too large, then the backwards stepwise regression will not work and _forward stepwise regression_ can be used instead. This procedure starts with a model that includes only the intercept. Predictors are added one at a time, and the one that most improves the measure of predictive accuracy is retained in the model. The procedure is repeated until no further improvement can be achieved.
Alternatively for either the backward or forward direction, a starting model can be one that includes a subset of potential predictors. In this case, an extra step needs to be included. For the backwards procedure we should also consider adding a predictor with each step, and for the forward procedure we should also consider dropping a predictor with each step. These are referred to as _hybrid_ procedures.

It is important to realise that any stepwise approach is not guaranteed to lead to the best possible model, but it almost always leads to a good model. For further details see James et al. ([2014](https://otexts.com/fpp3/selecting-predictors.html#ref-ISLR)).

##### Beware of inference after selecting predictors[](https://otexts.com/fpp3/selecting-predictors.html#beware-of-inference-after-selecting-predictors)
We do not discuss statistical inference of the predictors in this book (e.g., looking at p-values associated with each predictor). If you do wish to look at the statistical significance of the predictors, beware that _any_ procedure involving selecting predictors first will invalidate the assumptions behind the p-values. The procedures we recommend for selecting predictors are helpful when the model is used for forecasting; they are not helpful if you wish to study the effect of any predictor on the forecast variable.

## 7.6 Forecasting with regression
##### Scenario based forecasting
```
fit_consBest <- us_change |>
  model(
    lm = TSLM(Consumption ~ Income + Savings + Unemployment)
  )
future_scenarios <- scenarios(
  Increase = new_data(us_change, 4) |>
    mutate(Income=1, Savings=0.5, Unemployment=0),
  Decrease = new_data(us_change, 4) |>
    mutate(Income=-1, Savings=-0.5, Unemployment=0),
  names_to = "Scenario")

fc <- forecast(fit_consBest, new_data = future_scenarios)
```

```
us_change |>
  autoplot(Consumption) +
  autolayer(fc) +
  labs(title = "US consumption", y = "% change")
```
![](https://i.imgur.com/hknMTV2.png)

## 7.7 Nonlinear regression
The most commonly used transformation is the (natural) logarithm 
![](https://i.imgur.com/kH0acpV.png)
In this model, the slope β1 can be interpreted as an elasticity: β1 is the average percentage change in y resulting from a 1% increase in x.
The **log-linear** form is specified by only transforming the forecast variable and the **linear-log** form is obtained by transforming the predictor.

There are cases for which simply transforming the data will not be adequate and a more general specification may be required. Then the model we use is $$y=f(x)+ε$$
where f is a nonlinear function.
One of the simplest specifications is to make f **piecewise linear**. That is, we introduce points where the slope of f can change. These points are called **knots**. This can be achieved by letting x1=x and introducing variable x2 such that
![](https://i.imgur.com/aHRaNGv.png)
![](https://i.imgur.com/mwJFGeo.png)
##### Forecasting with a nonlinear trend
The simplest way of fitting a nonlinear trend is using quadratic or higher order trends obtained by specifying
![](https://i.imgur.com/kwK4e6X.png)
However, it is not recommended that quadratic or higher order trends be used in forecasting. When they are extrapolated, the resulting forecasts are often unrealistic.
A better approach is to use the piecewise specification introduced above and fit a piecewise linear trend which bends at some point in time. We can think of this as a nonlinear trend constructed of linear pieces. If the trend bends at time τ, then it can be specified by simply replacing x=t and c=τ above such that we include the predictors,
![](https://i.imgur.com/Cud9z26.png)
in the model. If the associated coefficients of x1,t and x2,t are β1 and β2, then β1 gives the slope of the trend before time τ, while the slope of the line after time τ is given by β1+β2. Additional bends can be included in the relationship by adding further variables of the form (t−τ)+ where τ is the “knot” or point in time at which the line should bend.
##### Example: Boston marathon winning times
We will fit some trend models to the Boston marathon winning times for men.
The top panel of Figure [7.20](https://otexts.com/fpp3/nonlinear-regression.html#fig:marathonLinear) shows the winning times since 1924. The time series shows a general downward trend as the winning times have been improving over the years. The bottom panel shows the residuals from fitting a linear trend to the data. The plot shows an obvious nonlinear pattern which has not been captured by the linear trend.![](https://i.imgur.com/fl5QX93.png)
The fitted exponential trend and forecasts are shown in Figure [7.21](https://otexts.com/fpp3/nonlinear-regression.html#fig:marathonNonlinear). Although the exponential trend does not seem to fit the data much better than the linear trend, it perhaps gives a more sensible projection in that the winning times will decrease in the future but at a decaying rate rather than a fixed linear rate.

The plot of winning times reveals three different periods. There is a lot of volatility in the winning times up to about 1950, with the winning times barely declining. After 1950 there is a clear decrease in times, followed by a flattening out after the 1980s, with the suggestion of an upturn towards the end of the sample. To account for these changes, we specify the years 1950 and 1980 as knots. We should warn here that subjective identification of knots can lead to over-fitting, which can be detrimental to the forecast performance of a model, and should be performed with caution.
```
fit_trends <- boston_men |>
  model(
    linear = TSLM(Minutes ~ trend()),
    exponential = TSLM(log(Minutes) ~ trend()),
    piecewise = TSLM(Minutes ~ trend(knots = c(1950, 1980)))
  )
fc_trends <- fit_trends |> forecast(h = 10)

boston_men |>
  autoplot(Minutes) +
  geom_line(data = fitted(fit_trends),
            aes(y = .fitted, colour = .model)) +
  autolayer(fc_trends, alpha = 0.5, level = 95) +
  labs(y = "Minutes",
       title = "Boston marathon winning times")
```
![](https://i.imgur.com/MhszrrF.png)

---

## 7.8 Correlation, causation and forecasting
---

## 7.9 Matrix formulation


