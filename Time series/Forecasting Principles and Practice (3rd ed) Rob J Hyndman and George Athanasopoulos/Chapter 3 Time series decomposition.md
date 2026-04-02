Time series data can exhibit a variety of patterns, and it is often helpful to split a time series into several components, each representing an underlying pattern category.
When we decompose a time series into components, we usually combine the trend and cycle into a single **trend-cycle** component (often just called the **trend** for simplicity). Thus we can think of a time series as comprising three components: a trend-cycle component, a seasonal component, and a remainder component (containing anything else in the time series).

## 3.1 Transformations and adjustments
The purpose of these adjustments and transformations is to simplify the patterns in the historical data by removing known sources of variation, or by making the pattern more consistent across the whole data set. Simpler patterns are usually easier to model and lead to more accurate forecasts.
##### Calendar adjustments
Some of the variation seen in seasonal data may be due to simple calendar effects. In such cases, it is usually much easier to remove the variation before doing any further analysis.
##### Population adjustments
Any data that are affected by population changes can be adjusted to give per-capita data.
##### Inflation adjustments
Data which are affected by the value of money are best adjusted before modelling.
To make these adjustments, a price index is used. If zt denotes the price index and yt denotes the original house price in year t, then xt=yt/zt∗z2000 gives the adjusted house price at year 2000 dollar values. Price indexes are often constructed by government agencies. For consumer goods, a common price index is the Consumer Price Index (or CPI).

##### Mathematical transformations
If the data shows variation that increases or decreases with the level of the series, then a transformation can be useful.
![](https://i.imgur.com/EvN9gmC.png)
A good value of λ is one which makes the size of the seasonal variation about the same across the whole series, as that makes the forecasting model simpler.
The `guerrero` feature ([Guerrero, 1993](https://otexts.com/fpp3/transformations.html#ref-Guerrero93)) can be used to choose a value of lambda for you
```
lambda <- aus_production |>
  features(Gas, features = guerrero) |>
  pull(lambda_guerrero)
```

## 3.2 Time series components
If we assume an additive decomposition, then we can write $$y_t=S_t+T_t+R_t$$
where yt is the data, St is the seasonal component, Tt is the trend-cycle component, and Rt is the remainder component, all at period t. Alternatively, a multiplicative decomposition would be written as
$$y_t=S_t*T_t*R_t$$
The additive decomposition is the most appropriate if the magnitude of the seasonal fluctuations, or the variation around the trend-cycle, does not vary with the level of the time series. When the variation in the seasonal pattern, or the variation around the trend-cycle, appears to be proportional to the level of the time series, then a multiplicative decomposition is more appropriate. Multiplicative decompositions are common with economic time series.
![](https://i.imgur.com/66rKmio.png)

##### Seasonally adjusted data
If the seasonal component is removed from the original data, the resulting values are the “seasonally adjusted” data.For an additive decomposition, the seasonally adjusted data are given by yt−St, and for multiplicative data, the seasonally adjusted values are obtained using yt/St.

## 3.3 Moving averages
![](https://i.imgur.com/GmHajAC.png)
That is, the estimate of the trend-cycle at time t is obtained by averaging values of the time series within k periods of t. Observations that are nearby in time are also likely to be close in value. Therefore, the average eliminates some of the randomness in the data, leaving a smooth trend-cycle component. We call this an **m-MA**, meaning a moving average of order m.
This is easily computed using `slide_dbl()` from the `slider` package
```
a10_trend <- a10 |>
  mutate(
    '5-MA' = slider::slide_dbl(Cost, mean, .before=2, .after=2, .complete = TRUE)
  )
a10_trend |> 
  autoplot(Cost) +
  geom_line(aes(y = `5-MA`), colour = "#D55E00") 
```
The order of the moving average determines the smoothness of the trend-cycle estimate. In general, a larger order means a smoother curve.
![](https://i.imgur.com/2dPO3UD.png)

##### Moving averages of moving averages
It is possible to apply a moving average to a moving average. One reason for doing this is to make an even-order moving average symmetric.
```
  `4-MA` = slider::slide_dbl(Beer, mean,
                .before = 1, .after = 2, .complete = TRUE),
    `2x4-MA` = slider::slide_dbl(`4-MA`, mean,
                .before = 1, .after = 0, .complete = TRUE)
```
The notation “2×4-MA” in the last column means a 4-MA followed by a 2-MA.
When a 2-MA follows a moving average of an even order (such as 4), it is called a “centred moving average of order 4”. This is because the results are now symmetric.
In general, an even order MA should be followed by an even order MA to make it symmetric. Similarly, an odd order MA should be followed by an odd order MA.
##### Estimating the trend-cycle with seasonal data
In general, a 2×m-MA is equivalent to a weighted moving average of order m+1 where all observations take the weight 1/m, except for the first and last terms which take weights 1/(2m). So, if the seasonal period is even and of order m, we use a 2×m-MA to estimate the trend-cycle. If the seasonal period is odd and of order m, we use a m-MA to estimate the trend-cycle. For example, a 2×12-MA can be used to estimate the trend-cycle of monthly data with annual seasonality and a 7-MA can be used to estimate the trend-cycle of daily data with a weekly seasonality.
##### Weighted moving averages
![](https://i.imgur.com/Eb7Bb2L.png)
A major advantage of weighted moving averages is that they yield a smoother estimate of the trend-cycle. Instead of observations entering and leaving the calculation at full weight, their weights slowly increase and then slowly decrease, resulting in a smoother curve.

---
## 3.4 Classical decomposition
![](https://i.imgur.com/saGp7iV.png)
![](https://i.imgur.com/mGkzczo.png)
![](https://i.imgur.com/j4uB4Wo.png)
![](https://i.imgur.com/31Kcyy3.png)

In classical decomposition, we assume that the seasonal component is constant from year to year. For multiplicative seasonality, the m values that form the seasonal component are sometimes called the “seasonal indices”.
```

a10 |>
  model(
    classical_decomposition(Cost, type="additive")
  ) |>
  components() |>
  autoplot()


```
##### Comments on classical decomposition[](https://otexts.com/fpp3/classical-decomposition.html#comments-on-classical-decomposition)
While classical decomposition is still widely used, it is not recommended, as there are now several much better methods. Some of the problems with classical decomposition are summarised below.
- The estimate of the trend-cycle is unavailable for the first few and last few observations.
- The trend-cycle estimate tends to over-smooth rapid rises and falls in the data.
- Classical decomposition methods assume that the seasonal component repeats from year to year. For many series, this is a reasonable assumption, but for some longer series it is not.
- Occasionally, the values of the time series in a small number of periods may be particularly unusual.
---
## 3.5 Methods used by official statistics agencies
![](https://i.imgur.com/OM77m6s.png)
![](https://i.imgur.com/PiUAxBp.png)
<mark>NOT COMPLETED</mark>


---

## 3.6 STL decomposition
![](https://i.imgur.com/sxxBies.png)

```
a10 |> model(STL(Cost ~ season(window="periodic"))) |> components() |> autoplot()
```
![](https://i.imgur.com/8aMHrnt.png)
STL is a versatile and robust method for decomposing time series. STL is an acronym for “Seasonal and Trend decomposition using Loess”, while loess is a method for estimating nonlinear relationships.
STL has several advantages over classical decomposition, and the SEATS and X-11 methods:

- Unlike SEATS and X-11, STL will handle any type of seasonality, not only monthly and quarterly data.
- The seasonal component is allowed to change over time, and the rate of change can be controlled by the user.
- The smoothness of the trend-cycle can also be controlled by the user.
- It can be robust to outliers (i.e., the user can specify a robust decomposition), so that occasional unusual observations will not affect the estimates of the trend-cycle and seasonal components. They will, however, affect the remainder component.
On the other hand, STL has some disadvantages. In particular, it does not handle trading day or calendar variation automatically, and it only provides facilities for additive decompositions.

A multiplicative decomposition can be obtained by first taking logs of the data, then back-transforming the components. Decompositions that are between additive and multiplicative can be obtained using a Box-Cox transformation of the data with 0<λ<1. A value of λ=0 gives a multiplicative decomposition while λ=1 gives an additive decomposition.