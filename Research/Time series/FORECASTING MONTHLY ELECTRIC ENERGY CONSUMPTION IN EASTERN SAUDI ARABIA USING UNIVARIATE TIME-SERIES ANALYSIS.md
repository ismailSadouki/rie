
# Abstract

Univariate Box-Jenkins time-series analysis has been used for modeling and forecasting monthly domestic electric energy consumption in the Eastern Province of Saudi Arabia. Autoregressive integrated moving average (ARIMA) models were developed using data for 5 yr and evaluated on forecasting new data for the sixth year. The optimum model derived is a multiplicative combination of seasonal and nonseasonal autoregressive parts, each being of the first order, following first differencing at both the seasonal and nonseasonal levels. Compared to regression and abductive network machine-learning models previously developed on the same data, ARIMA models require less data, have fewer coefficients, and are more accurate. The optimum ARIMA model forecasts monthly data for the evaluation year with an average percentage error of 3.8% compared to 8.1% and 5.6% for the best multiple-series regression and abductory induction mechanism (AIM) models, respectively; the mean-square forecasting error is reduced with the ARIMA model by factors of 3.2 and 1.6, respectively. © 1997 Elsevier Science Ltd.




## box and jenkins method

The orders of the AR and MA parts are chosen such that the theoretical autocorrelation function (acjOand partial autocorrelation function (pacf) of the combination approximately match the estimated acf and pacf for the modeled time series.

UBJ-ARIMA modeling is appropriate only for stationary time series (with constant mean, variance, and autocorrelations). A nonstationary series can often be made stationary through simple operations such as differencing and appropriate transforms such as the log transform, prior to modeling.


Box and Jenkins[8] proposed a practical three-stage procedure for finding a good ARIMA model, namely, identification, estimation, and diagnostic checking. During model synthesis, a portion at the end of the available time series is usually reserved for verifying model performance on known future data.

At the **identification stage**, the acf and pacf calculated for the available time-series data are considered as the estimated acf and pacf for the unknown generating process. If these functions indicate nonstationarity, the time series is first converted to a stationary series by appropriate transforms and differencing operations, possibly at both the nonseasonal and seasonal levels.
ARIMA modeling is performed on the resulting stationary series, with its acf andpacf functions used as guides for tentatively choosing one or more ARIMA models with theoretical acf andpacffunctionsthat most closely resemble them.
In general, the ARIMA model is a combination of AR and MA portions; each may be made up of seasonal and nonseasonal parts. A good forecasting model is parsimonious, i.e. it uses the smallest number of coefficients to explain the available data.

At the **estimation stage**, these coefficients are estimated by fitting the model function to time-series data and minimizing a criterion for the residual errors.
For a good model, the coefficients should be of high statistical quality, should not be highly correlated, and must satisfy certain stationarity and invertiblity conditions  [[#Research|[1]]].
If all the important statistical relations in the stationary time series have been embodied in the ARIMA model, the estimation stage residuals should be statistically independent random noise. Tests at the diagnostic checking stage ensure that these residuals are statistically small, that their variance does not change with time, and that their acf function does not have significant autocorrelations, particularly at important short lags. Details of UBJ-ARIMA modeling are described in Refs. [1,2]




## Identification stage
The acf in Fig. 3 dies down at both the nonseasonal and seasonal levels while the pacf cuts off to zero after lag 1 at the nonseasonal level, thus suggesting a first-order nonseasonal autoregressive AR(1) model. However, the obvious seasonal features of Et and the fact that the pacf has a nearly significant coefficient at the near-seasonal lag 11 (It-valuel = 1.82 vs a critical value of 2) suggest the inclusion of a first-order seasonal autoregressive SAR(1) part. A multiplicative combination of the two parts is given by
![](https://i.imgur.com/NWv9els.png)

![](https://i.imgur.com/r5dHTUQ.png)

## Estimation
![](https://i.imgur.com/HceVnhD.png)
![](https://i.imgur.com/oIk1iOX.png)

## Diagnostic checking
At this stage, statistical adequacy of the estimated tentative models is verified. If the statistical properties of the sample population have been adequately modeled using the identified ARIMA processes, the random shock values $a_t$, should be statistically independent; i.e. should be uncorrelated white noise.
In practice, we substitute $a_t$, by their estimates fi, in the form of the residuals calculated as the difference between actual observations in the modeled time-series and corresponding values predicted by the estimated model.
Therefore, the acf function for the residuals resulting from a good ARIMA model would ideally have statistically zero autocorrelation coefficients. However, sampling errors may cause some residual autocorrelations to be nonzero by chance even for a good model. Individual t-values for the acf coefficients indicate statistical significance of each relative to zero, and a collective chi-squared test statistic is often applied for all the residual autocorrelations as a set.

Figure 5 shows plots of the residuals for the two tentative models as well as values for the first 15 autocorrelation coefficients and their t-statistics. The residual plots for the two models show small variations around the zero mean. Performance of the models in explaining the estimation data is reasonably uniform, with little systematic changes in the error variance with time. Such changes are more evident with the MA model where variance tends to increase for the recent past, suggesting poorer future forecasting [9]. This may also indicate the requirement for additional transforms, e.g. a log transform, on the original time-series prior to ARIMA modeling. For the MA model, no residuals are
significant beyond two standard deviations above the zero mean, the largest being only 1.94 standard deviations• For the AR model, only one residual is significant at 2.08 standard deviations. A few residuals may be significantly greater than zero by mere chance, even if the time series is modeled adequately.
![](https://i.imgur.com/nFy4tDD.png)
Plots of the estimated residual acf in Fig. 5 indicate inferior performance by the MA model, with significant autocorrelations at lags 1 and 3 (It-valuel > 1.25) and at lag 11 (It-valuel > 1.6) The AR model gives only one significant autocorrelation at lag 11. The first two correlations in the case of the MA model are more serious since they occur at short lags. However, at around 1.4, their absolute tvalues are not well in excess of the short lag warning value of 1.25. Some significant residual autocorrelations may exist by chance, and the model adopted would be still acceptable [1]. This is particularly so with the AR model, where the troublesome autocorrelation does not occur at the short lags or the first few seasonal lags. The tentative models may be modified by adding coefficients at the lags where serious residual autocorrelations occur, but this was avoided in favor of simpler parsimonious models which have been known to provide better forecasts [1]. However, the results indicate that the MA model is somewhat inferior to the AR model in explaining variations in the time series and therefore in extrapolating it into the future.


The significance of the K-residual autocorrelations combined as a set is tested using the Ljung-Box statistic [20]
![](https://i.imgur.com/whb3bTs.png)
![](https://i.imgur.com/lxPBxPp.png)



## Forecasting





# Research
 1. Pankratz, A., *Forecasting with Univariate Box-Jenkins Models: Concepts and Cases*. Wiley, New York, 1983.
2. Bowerman, B. L. and O'Connell, R. T., Time Series Forecasting: Unified Concepts and Computer lmph,mentation. PWS Publishers, Boston, MA, 1987
3. Vemuri, S., Hill, E. F. and Balasubramanian, R., Ricerche Di Automatica, 1974, 5, 103. 
4. Uri, N. D., U.S. Department of Labor, Bureau of Labor Statistics, Working paper 30, November 1974.