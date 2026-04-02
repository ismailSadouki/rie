## 2.1 `tsibble` objects
time class functions can be used depending on the frequency of the observations.
![](https://i.imgur.com/4jEEKWB.png)
### Working with `tsibble` objects
We can use `dplyr` functions such as `mutate()`, `filter()`, `select()` and `summarise()` to work with `tsibble` objects.

```
PBS |> filter(ATC2 == "A10") |> select(Month, Type, Cost) |>
  summarise(TotalC = sum(Cost)) |>
  mutate(Cost = TotalC/1e6) -> a10
```

## 2.2 Time plots
```
autoplot(a10, Cost) +
  labs(y = "$ (millions)",
       title = "Australian antidiabetic drug sales") + geom_point()

```

## 2.3 Time series patterns
![](https://i.imgur.com/Yi9d2LO.png)
- Trend
	A _trend_ exists when there is a long-term increase or decrease in the data. It does not have to be linear. Sometimes we will refer to a trend as “changing direction”, when it might go from an increasing trend to a decreasing trend.
- Seasonal
	Seasonality is always of a fixed and known period.
- Cyclic
	A _cycle_ occurs when the data exhibit rises and falls that are not of a fixed frequency. These fluctuations are usually due to economic conditions, and are often related to the “business cycle”. The duration of these fluctuations is usually at least 2 years.
If the fluctuations are not of a fixed frequency then they are cyclic; if the frequency is unchanging and associated with some aspect of the calendar, then the pattern is seasonal.

When choosing a forecasting method, we will first need to identify the time series patterns in the data, and then choose a method that is able to capture the patterns properly.

## 2.4 Seasonal plots
A seasonal plot is similar to a time plot except that the data are plotted against the individual “seasons” in which the data were observed.
```
a10 |>
  gg_season(Cost, labels = "both") +
  labs(y = "$ (millions)",
       title = "Seasonal plot: Antidiabetic drug sales")
```
![](https://i.imgur.com/vJ36a9q.png)
##### Multiple seasonal periods
Where the data has more than one seasonal pattern, the `period` argument can be used to select which seasonal plot is required.
```
vic_elec |> gg_season(Demand, period = "day") +
  theme(legend.position = "none") +
  labs(y="MWh", title="Electricity demand: Victoria")
```

## 2.5 Seasonal subseries plots
An alternative plot that emphasises the seasonal patterns is where the data for each season are collected together in separate mini time plots.

```
gg_subseries(a10, Cost)
```

![](https://i.imgur.com/Iq2mR52.png)

## 2.6 Scatterplots
## 2.7 Lag plots
```
a10 |>
  gg_lag(Cost, geom = "point") +
  labs(x = "lag(Beer, k)")
```

## 2.8 Autocorrelation
Just as correlation measures the extent of a linear relationship between two variables, autocorrelation measures the linear relationship between _lagged values_ of a time series.
![](https://i.imgur.com/z6C3ULm.png)
![](https://i.imgur.com/WSYisFy.png)
## 2.9 White noise
For white noise series, we expect each autocorrelation to be close to zero. Of course, they will not be exactly equal to zero as there is some random variation. For a white noise series, we expect 95% of the spikes in the ACF to lie within ±1.96/√T where T is the length of the time series.



## 2.11 Further reading
- W. S. Cleveland ([1993](https://otexts.com/fpp3/graphics-reading.html#ref-Cleveland1993)) is a classic book on the principles of visualisation for data analysis. While it is more than 20 years old, the ideas are timeless.
- Unwin ([2015](https://otexts.com/fpp3/graphics-reading.html#ref-Unwin2015)) is a modern introduction to graphical data analysis using R. It does not have much information on time series graphics, but plenty of excellent general advice on using graphics for data analysis.
- Cleveland, W. S. (1993). _Visualizing data_. Hobart Press. [[Amazon]](http://buy.geni.us/Proxy.ashx?TSID=140570&GR_URL=http%3A%2F%2Fwww.amazon.com%2Fdp%2F0963488406)

- Unwin, A. (2015). _Graphical data analysis with R_. Chapman; Hall/CRC. [[Amazon]](http://buy.geni.us/Proxy.ashx?TSID=140570&GR_URL=http%3A%2F%2Fwww.amazon.com%2Fdp%2F1498715230)