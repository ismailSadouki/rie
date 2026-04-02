## simple methods
```
library(fpp3)
# Re-index based on trading days
google_stock <- gafa_stock |>
  filter(Symbol == "GOOG", year(Date) >= 2015) |>
  mutate(day = row_number()) |>
  update_tsibble(index = day, regular = TRUE)
# Filter the year of interest
google_2015 <- google_stock |> filter(year(Date) == 2015)
# Fit the models
google_fit <- google_2015 |>
  model(
    Mean = MEAN(Close),
    `Naïve` = NAIVE(Close),
    Drift = NAIVE(Close ~ drift())
  )
# Produce forecasts for the trading days in January 2016
google_jan_2016 <- google_stock |>
  filter(yearmonth(Date) == yearmonth("2016 Jan"))
google_fc <- google_fit |>
  forecast(new_data = google_jan_2016)
# Plot the forecasts
google_fc |>
  autoplot(google_2015, level = NULL) +
  autolayer(google_jan_2016, Close, colour = "black") +
  labs(y = "$US",
       title = "Google daily closing stock prices",
       subtitle = "(Jan 2015 - Jan 2016)") +
  guides(colour = guide_legend(title = "Forecast"))

```
![](https://i.imgur.com/vAS1D2O.png)


## Fitted values and residuals
![](https://i.imgur.com/gywVvCy.png)
![](https://i.imgur.com/w57tWD1.png)
If a transformation has been used in the model, then it is often useful to look at residuals on the transformed scale. We call these “**innovation residuals**”.
For example, suppose we modelled the logarithms of the data, wt=log(yt). Then the innovation residuals are given by wt−^wt whereas the regular residuals are given by yt−^yt.

For example, suppose we modelled the logarithms of the data, wt=log(yt). Then the innovation residuals are given by wt−^wt whereas the regular residuals are given by yt−^yt.

```
augment(google_fit)
```
Residuals are useful in checking whether a model has adequately captured the information in the data. For this purpose, we use innovation residuals.
Residuals are useful in checking whether a model has adequately captured the information in the data. For this purpose, we use innovation residuals.


## 5.4 Residual diagnostics



![](https://i.imgur.com/4CXx602.png)


acf
```
augment(google_fit) |> filter(.model=="Naïve") |>
  ACF(.resid) |>
  autoplot()
```
In addition to looking at the ACF plot, we can also do a more formal test for autocorrelation by considering a whole set of rk values as a group, rather than treating each one separately.
![](https://i.imgur.com/Wise03Z.png)
![](https://i.imgur.com/YO8cjPK.png)
reject the null hyp if p is less then 0.05 (H0 white noise)

If either of these properties is not satisfied, then the forecasting method can be modified to give better forecasts. Adjusting for bias is easy: if the residuals have mean m, then simply add m to all forecasts and the bias problem is solved. Fixing the correlation problem is harder, and we will not address it until Chapter [10](https://otexts.com/fpp3/dynamic.html#dynamic).
In addition to these essential properties, it is useful (but not necessary) for the residuals to also have the following two properties.

3. The innovation residuals have constant variance. This is known as “homoscedasticity”.
```
augment(google_fit) |>
  autoplot(.resid) 
```
4. The innovation residuals are normally distributed.
```
augment(google_fit) |> filter(.model=="Naïve") |>
  ggplot(aes(x=.resid)) + geom_histogram(bins = 150)
```
However, a forecasting method that does not satisfy these properties cannot necessarily be improved. Sometimes applying a Box-Cox transformation may assist with these properties, but otherwise there is usually little that you can do to ensure that your innovation residuals have constant variance and a normal distribution. Instead, an alternative approach to obtaining prediction intervals is necessary. We will show how to deal with non-normal innovation residuals in Section [5.5](https://otexts.com/fpp3/prediction-intervals.html#prediction-intervals).

A convenient shortcut for producing these residual diagnostic graphs is the `gg_tsresiduals()` function, which will produce a time plot, ACF plot and histogram of the residuals.
```
google_2015 |>
  model(NAIVE(Close)) |>
  gg_tsresiduals()
```
![](https://i.imgur.com/uRqjk6c.png)


## 5.5 Distributional forecasts and prediction intervals
we express the uncertainty in our forecasts using a probability distribution. It describes the probability of observing possible future values using the fitted model. The point forecast is the mean of this distribution. Most time series models produce normally distributed forecasts — that is, we assume that the distribution of possible future values follows a normal distribution.
![](https://i.imgur.com/pdtLmts.png)
![](https://i.imgur.com/u82b3c4.png)
![](https://i.imgur.com/S7VBfKL.png)
##### One-step prediction intervals
![](https://i.imgur.com/2Qg20F5.png)

##### Multi-step prediction intervals
A common feature of prediction intervals is that they usually increase in length as the forecast horizon increases. The further ahead we forecast, the more uncertainty is associated with the forecast, and thus the wider the prediction intervals. That is, σh usually increases with h (although there are some non-linear forecasting methods which do not have this property)

Prediction intervals can easily be computed for you when using the `fable` package. For example, here is the output when using the naïve method for the Google stock price.
```
google_2015 |>
  model(NAIVE(Close)) |>
  forecast(h = 10) |>
  hilo()
```
The `hilo()` function converts the forecast distributions into intervals. By default, 80% and 95% prediction intervals are returned, although other options are possible via the `level` argument.
plotting
```
google_2015 |>
  model(NAIVE(Close)) |>
  forecast(h = 10) |>
  autoplot(google_2015) +
  labs(title="Google daily closing stock price", y="$US" )

```
![](https://i.imgur.com/qXnw29X.png)

##### Prediction intervals from bootstrapped residuals
When a normal distribution for the residuals is an unreasonable assumption, one alternative is to use bootstrapping, which only assumes that the residuals are uncorrelated with constant variance.
```
fit <- google_2015 |>
  model(NAIVE(Close))
sim <- fit |> generate(h = 30, times = 5, bootstrap = TRUE)
sim
```
```
google_2015 |>
  ggplot(aes(x = day)) +
  geom_line(aes(y = Close)) +
  geom_line(aes(y = .sim, colour = as.factor(.rep)),
    data = sim) +
  labs(title="Google daily closing stock price", y="$US" ) +
  guides(colour = "none")
```
![](https://i.imgur.com/fO3Cv8z.png)
Then we can compute prediction intervals by calculating percentiles of the future sample paths for each forecast horizon. The result is called a **bootstrapped** prediction interval. The name “bootstrap” is a reference to pulling ourselves up by our bootstraps, because the process allows us to measure future uncertainty by only using the historical data.
This is all built into the `forecast()` function so you do not need to call `generate()` directly.
```
fc <- fit |> forecast(h = 30, bootstrap = TRUE)
```
Notice that the forecast distribution is now represented as a simulation with 5000 sample paths. Because there is no normality assumption, the prediction intervals are not symmetric. The `.mean` column is the mean of the bootstrap samples, so it may be slightly different from the results obtained without a bootstrap.
```
autoplot(fc, google_2015) +
  labs(title="Google daily closing stock price", y="$US" )
```
![](https://i.imgur.com/ZadXV1q.png)
The number of samples can be controlled using the `times` argument for `forecast()`.

## 5.6 Forecasting using transformations
![](https://i.imgur.com/0inxf9s.png)
![](https://i.imgur.com/dCIThaz.png)
![](https://i.imgur.com/YMqXgcp.png)
![](https://i.imgur.com/s8fte51.png)
![](https://i.imgur.com/dzIIcGa.png)
![](https://i.imgur.com/KwLN6E8.png)
![](https://i.imgur.com/WoGdypa.png)
##### Prediction intervals with transformations[](https://otexts.com/fpp3/ftransformations.html#prediction-intervals-with-transformations)
If a transformation has been used, then the prediction interval is first computed on the transformed scale, and the end points are back-transformed to give a prediction interval on the original scale. This approach preserves the probability coverage of the prediction interval, although it will no longer be symmetric around the point forecast.

Transformations sometimes make little difference to the point forecasts but have a large effect on prediction intervals.

##### Bias adjustments
One issue with using mathematical transformations such as Box-Cox transformations is that the back-transformed point forecast will not be the mean of the forecast distribution. In fact, it will usually be the median of the forecast distribution
![](https://i.imgur.com/JDlbwKc.png)
The difference between the simple back-transformed forecast given by [(5.2)](https://otexts.com/fpp3/ftransformations.html#eq:backtransform) and the mean given by [(5.3)](https://otexts.com/fpp3/ftransformations.html#eq:backtransformmean) is called the **bias**. When we use the mean, rather than the median, we say the point forecasts have been **bias-adjusted**.
Bias-adjusted forecast means are automatically computed in the fable package. The forecast median (the point forecast prior to bias adjustment) can be obtained using the median() function on the distribution column.

## 5.7 Forecasting with decomposition
![](https://i.imgur.com/bGQQgDp.png)
![](https://i.imgur.com/AP0RMtt.png)
To forecast a decomposed time series, we forecast the seasonal component, ^St, and the seasonally adjusted component ^At, separately. It is usually assumed that the seasonal component is unchanging, or changing extremely slowly, so it is forecast by simply taking the last year of the estimated component. In other words, a seasonal naïve method is used for the seasonal component.

To forecast the seasonally adjusted component, any non-seasonal forecasting method may be used. For example, the drift method, or Holt’s method (discussed in Chapter [8](https://otexts.com/fpp3/expsmooth.html#expsmooth)), or a non-seasonal ARIMA model (discussed in Chapter [9](https://otexts.com/fpp3/arima.html#arima)), may be used.

This is made easy with the `decomposition_model()` function, which allows you to compute forecasts via any additive decomposition, using other model functions to forecast each of the decomposition’s components. Seasonal components of the model will be forecast automatically using `SNAIVE()` if a different model isn’t specified. The function will also do the reseasonalising for you, ensuring that the resulting forecasts of the original data are obtained. These are shown in Figure [5.19](https://otexts.com/fpp3/forecasting-decomposition.html#fig:print-media5).
```
us_retail_employment <- us_employment |>
  filter(year(Month) >= 1990, Title == "Retail Trade")
fit_dcmp <- us_retail_employment |>
  model(stlf = decomposition_model(
    STL(Employed ~ trend(window = 7), robust = TRUE),
    NAIVE(season_adjust)
  ))
fit_dcmp |>
  forecast() |>
  autoplot(us_retail_employment)+
  labs(y = "Number of people",
       title = "US retail employment")
```
![](https://i.imgur.com/DSTSnj8.png)

The prediction intervals shown in this graph are constructed in the same way as the point forecasts. That is, the upper and lower limits of the prediction intervals on the seasonally adjusted data are “reseasonalised” by adding in the forecasts of the seasonal component.
The ACF of the residuals, shown in Figure [5.20](https://otexts.com/fpp3/forecasting-decomposition.html#fig:print-media5-resids), displays significant autocorrelations. These are due to the naïve method not capturing the changing trend in the seasonally adjusted series.
![](https://i.imgur.com/w612vVb.png)
## 5.8 Evaluating point forecast accuracy
![](https://i.imgur.com/nRrT3YU.png)
![](https://i.imgur.com/hHHK1Vy.png)
![](https://i.imgur.com/2HEyDHr.png)


##### Functions to subset a time series
```
aus_production |> filter(year(Quarter) >= 1995)
```
```
aus_production |> filter_index("1995 Q1" ~ .)
```
Another useful function is `slice()`, which allows the use of indices to choose a subset from each group.
```
aus_production |>
  slice(n()-19:0)
```
Slice also works with groups, making it possible to subset observations from each key. For example,
```
aus_retail |>
  group_by(State, Industry) |>
  slice(1:12)
```
will subset the first year of data from each time series in the data.


##### Forecast errors
Note that forecast errors are different from residuals in two ways. First, residuals are calculated on the _training_ set while forecast errors are calculated on the _test_ set. Second, residuals are based on _one-step_ forecasts while forecast errors can involve _multi-step_ forecasts.
##### Scale-dependent errors
The two most commonly used scale-dependent measures are based on the absolute errors or squared errors
When comparing forecast methods applied to a single time series, or to several time series with the same units, the MAE is popular as it is easy to both understand and compute. A forecast method that minimises the MAE will lead to forecasts of the median, while minimising the RMSE will lead to forecasts of the mean. Consequently, the RMSE is also widely used, despite being more difficult to interpret.
##### Percentage errors
The percentage error is given by pt=100et/yt. Percentage errors have the advantage of being unit-free, and so are frequently used to compare forecast performances between data sets. The most commonly used measure is: Mean absolute percentage error: MAPE=mean(|pt|).
Measures based on percentage errors have the disadvantage of being infinite or undefined if yt=0 for any t in the period of interest, and having extreme values if any yt is close to zero.
Another problem with percentage errors that is often overlooked is that they assume the unit of measurement has a meaningful zero;
They also have the disadvantage that they put a heavier penalty on negative errors than on positive errors.This observation led to the use of the so-called “symmetric” MAPE (sMAPE) proposed by Armstrong ([1978, p. 348](https://otexts.com/fpp3/accuracy.html#ref-Armstrong85)), which was used in the M3 forecasting competition. It is defined by
![](https://i.imgur.com/c7qc80P.png)
However, if yt is close to zero, ^yt is also likely to be close to zero. Thus, the measure still involves division by a number close to zero, making the calculation unstable. Also, the value of sMAPE can be negative, so it is not really a measure of “absolute percentage errors” at all.
Hyndman & Koehler ([2006](https://otexts.com/fpp3/accuracy.html#ref-HK06)) recommend that the sMAPE not be used. It is included here only because it is widely used,
##### Scaled errors
Scaled errors were proposed by Hyndman & Koehler ([2006](https://otexts.com/fpp3/accuracy.html#ref-HK06)) as an alternative to using percentage errors when comparing forecast accuracy across series with different units. They proposed scaling the errors based on the _training_ MAE from a simple forecast method.
For a non-seasonal time series, a useful way to define a scaled error uses naïve forecasts:
![](https://i.imgur.com/4NIECZa.png)
A scaled error is less than one if it arises from a better forecast than the average one-step naïve forecast computed on the training data. Conversely, it is greater than one if the forecast is worse than the average one-step naïve forecast computed on the training data.
For seasonal time series, a scaled error can be defined using seasonal naïve forecasts:
![](https://i.imgur.com/NMggWBn.png)

Sometimes, different accuracy measures will lead to different results as to which forecast method is best. However, in this case, all of the results point to the seasonal naïve method as the best of these four methods for this data set.



## 5.9 Evaluating distributional forecast accuracy
![](https://i.imgur.com/V2KxlBU.png)
![](https://i.imgur.com/2uvRnhQ.png)
![](https://i.imgur.com/RJIC3r7.png)
<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20250423230842.png]]
![](https://i.imgur.com/pvEMyjk.png)

The preceding measures all measure point forecast accuracy. When evaluating distributional forecasts, we need to use some other measures.

##### Quantile scores
```
google_fc |>
  filter(.model == "Naïve") |>
  autoplot(bind_rows(google_2015, google_jan_2016), level=80)+
  labs(y = "$US",
       title = "Google closing stock prices")
```
![](https://i.imgur.com/i3gMiL1.png)
The lower limit of this prediction interval gives the 10th percentile (or 0.1 quantile) of the forecast distribution, so we would expect the actual value to lie below the lower limit about 10% of the time, and to lie above the lower limit about 90% of the time. When we compare the actual value to this percentile, we need to allow for the fact that it is more likely to be above than below.

The quantile score can be interpreted like an absolute error. In fact, when p=0.5, the quantile score Q0.5,t is the same as the absolute error. For other values of p, the “error” (yt−fp,t) is weighted to take account of how likely it is to be positive or negative. If p>0.5, Qp,t gives a heavier penalty when the observation is greater than the estimated quantile than when the observation is less than the estimated quantile. The reverse is true for p<0.5.
##### Continuous Ranked Probability Score[](https://otexts.com/fpp3/distaccuracy.html#continuous-ranked-probability-score)
Often we are interested in the whole forecast distribution, rather than particular quantiles or prediction intervals. In that case, we can average the quantile scores over all values of p to obtain the **Continuous Ranked Probability Score** or CRPS (
```
google_fc |>
  accuracy(google_stock, list(crps = CRPS))
#> # A tibble: 3 × 4
#>   .model Symbol .type  crps
#>   <chr>  <chr>  <chr> <dbl>
#> 1 Drift  GOOG   Test   33.5
#> 2 Mean   GOOG   Test   76.7
#> 3 Naïve  GOOG   Test   26.5
```
##### Scale-free comparisons using skill scores
As with point forecasts, it is useful to be able to compare the distributional forecast accuracy of several methods across series on different scales. For point forecasts, we used scaled errors for that purpose. Another approach is to use skill scores. These can be used for both point forecast accuracy and distributional forecast accuracy

## 5.10 Time series cross-validation
![](https://i.imgur.com/MLiPePO.png)
The forecast accuracy is computed by averaging over the test sets. This procedure is sometimes known as “evaluation on a rolling forecasting origin” because the “origin” at which the forecast is based rolls forward in time.
With time series forecasting, one-step forecasts may not be as relevant as multi-step forecasts. In this case, the cross-validation procedure based on a rolling forecasting origin can be modified to allow multi-step errors to be used. Suppose that we are interested in models that produce good 4-step-ahead forecasts. Then the corresponding diagram is shown below.
![](https://i.imgur.com/Q9WApSn.png)
In the following example, we compare the forecast accuracy obtained via time series cross-validation with the residual accuracy. The `stretch_tsibble()` function is used to create many training sets.
```
# Time series cross-validation accuracy
google_2015_tr <- google_2015 |>
  stretch_tsibble(.init = 3, .step = 1) |>
  relocate(Date, Symbol, .id)
google_2015_tr
```
The `.id` column provides a new key indicating the various training sets. The `accuracy()` function can be used to evaluate the forecast accuracy across the training sets.
```
# TSCV accuracy
google_2015_tr |>
  model(RW(Close ~ drift())) |>
  forecast(h = 1) |>
  accuracy(google_2015)
# Training set accuracy
google_2015 |>
  model(RW(Close ~ drift())) |>
  accuracy()
```

<mark>A good way to choose the best forecasting model is to find the model with the smallest RMSE computed using time series cross-validation.</mark>
