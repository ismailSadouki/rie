## 4.1 Some simple statistics
for the mean:
```
tourism |>
  features(Trips, list(mean = mean)) |>
  arrange(mean)
```
Rather than compute one feature at a time, it is convenient to compute many features at once.
```
tourism |> features(Trips, quantile)
```

## 4.2 ACF features
We can also compute autocorrelations of the changes in the series between periods. That is, we “difference” the data and create a new time series consisting of the differences between consecutive observations. Then we can compute the autocorrelations of this new differenced series. Occasionally it is useful to apply the same differencing operation again, so we compute the differences of the differences. The autocorrelations of this double differenced series may provide useful information.
Another related approach is to compute seasonal differences of a series. If we had monthly data, for example, we would compute the difference between consecutive Januaries, consecutive Februaries, and so on. This enables us to look at how the series is changing between years, rather than between months. Again, the autocorrelations of the seasonally differenced series may provide useful information

The `feat_acf()` function computes a selection of the autocorrelations discussed here. It will return six or seven features:

- the first autocorrelation coefficient from the original data;
- the sum of squares of the first ten autocorrelation coefficients from the original data;
- the first autocorrelation coefficient from the differenced data;
- the sum of squares of the first ten autocorrelation coefficients from the differenced data;
- the first autocorrelation coefficient from the twice differenced data;
- the sum of squares of the first ten autocorrelation coefficients from the twice differenced data;
- For seasonal data, the autocorrelation coefficient at the first seasonal lag is also returned.

```
tourism |> features(Trips, feat_acf)
```
## 4.3 STL Features
For strongly trended data, the seasonally adjusted data should have much more variation than the remainder component. Therefore Var(Rt)/Var(Tt+Rt) should be relatively small. But for data with little or no trend, the two variances should be approximately the same. So we define the strength of trend as:
![](https://i.imgur.com/OeRLkPG.png)
This will give a measure of the strength of the trend between 0 and 1. Because the variance of the remainder might occasionally be even larger than the variance of the seasonally adjusted data, we set the minimal possible value of FT equal to zero.
The strength of seasonality is defined similarly, but with respect to the detrended data rather than the seasonally adjusted data:
![](https://i.imgur.com/ipofOIw.png)
A series with seasonal strength FS close to 0 exhibits almost no seasonality, while a series with strong seasonality will have FS close to 1 because Var(Rt) will be much smaller than Var(St+Rt).

These and other STL-based features are computed using the `feat_stl()` function.
```
tourism |>
  features(Trips, feat_stl) |>
  ggplot(aes(x = trend_strength, y = seasonal_strength_year,
             col = Purpose)) +
  geom_point() +
  facet_wrap(vars(State))
```
![](https://i.imgur.com/zyElp8U.png)

## 4.4 Other features
We’ve already seen how we can plot one feature against another (Section [4.3](https://otexts.com/fpp3/stlfeatures.html#stlfeatures)). We can also do pairwise plots of groups of features. In Figure [4.3](https://otexts.com/fpp3/exploring-australian-tourism-data.html#fig:seasonalfeatures), for example, we show all features that involve seasonality, along with the `Purpose` variable.
```
library(glue)
tourism_features |>
  select_at(vars(contains("season"), Purpose)) |>
  mutate(
    seasonal_peak_year = seasonal_peak_year +
      4*(seasonal_peak_year==0),
    seasonal_trough_year = seasonal_trough_year +
      4*(seasonal_trough_year==0),
    seasonal_peak_year = glue("Q{seasonal_peak_year}"),
    seasonal_trough_year = glue("Q{seasonal_trough_year}"),
  ) |>
  GGally::ggpairs(mapping = aes(colour = Purpose))
```
![](https://i.imgur.com/tmCuGYU.png)
