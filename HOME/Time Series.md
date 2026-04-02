	# P-value
#### Resources
Book: Statistical Inference by George Casella


![](https://i.imgur.com/WrfQ0he.png)
p-value is a measure used in hypothesis testing to indicate the probability of observing a test statistic as extreme as the one computed given that the null hypothesis is true.
- if the p-val is less the $\alpha$ reject the null-hyp 
$$ P_\theta=< \alpha$$
#### Other statisticians use:
- if p-val =< 0.01 reject => the difference is highly significant
- if p-val > 0.01 but =< 0.05 reject => the difference is significant
- if p-val > 0.05 but =< 0.1 consider the consequence of type 1 error before rejecting the null hyp
- if p-val > 0.1 do not reject => the difference is not significat

---


# Box and Jenkins
#### **Resources**
Article - A Survey on Data Mining Techniques Applied to Electricity-Related Time Series Forecasting

Note:
Some information in this section may not be relevant to the class; it was included out of personal interest.

![](https://i.imgur.com/Oa8JUIl.png)
#### 1. Identification of the model.
The first task to be fulfilled is to determine wether the time series is stationary or not, that is, to determine if the mean and variance of a stochastic process do not vary along with time. If the time series does not satisfy this constraint, a transformation has to be applied and the time series has to be differentiated until reaching stationarity. The number of times that the series has to be differentiated is denoted by d and is one of the parameters to be determined in ARIMA models.
### 2. Estimation of the parameters.
Once d is determined, the process is reduced to an ARMA model with parameters p and q. These parameters can be estimated by following non-linear strategies. From all of them, three stand out: the evolutionary algorithms, the least squares (LS) minimization and the maximum likelihood (ML). Evolutionary algorithms and LS consist in minimizing the square error of forecasting for a training set while the ML consists in maximizing the likehood function, which is proportional to the probability of obtaining the data given the model.

Comparisons between different Box-Jenkins time series models can be easily found in the literature [1–4], but there are very few works comparing the results of different parameter estimation methods. ML and LS were compared in [5] to obtain an ARIMA model to predict the gold price. The results reported an error of 0.81% and 2.86% when using a LS and a ML, respectively. A comparative analysis between autocorrelation function, conditional likelihood, unconditional likelihood and genetic algorithms in the context of streamflow forecasting was made in [6]. Although similar results were obtained by the four methods, the autocorrelation function and the methods based on ML were the most computationally cost, especially when increased the order of the model. For that, the authors finally recommended the use of evolutionary algorithms.

The good performance of several metaheuristics to solve optimization problems along with the limitations of the classical methods, such as the low precision and poor convergence, has motivated the appearance of recent works comparing evolutionary algorithms and traditional methods for parameter estimation in time series models [7,8]. In general, evolutionary algorithms obtain better results due to the likelihood function is highly nonlinear, and therefore, conventional methods usually converge to a local maxima contrarily to genetic algorithms, which tend to find the global maxima [9].
### 3. Validation of the model.
Once the ARIMA model has been estimated several hypotheses have to be validated. Thus, the fitness of the model, the residual values or the significance of the coefficients forming the model are forced to agree with some requirements. In cases in which this step is not fulfilled, the process begins again and the parameters are recalculated.

In particular, an ARIMA model is validated if estimated residuals behave as white noise, that is, if they exhibit normal distribution as well as constant variance and null mean and covariance. To determine if they are white noise, autocorrelation and partial autocorrelation functions are calculated. These values must be significatively small.

Additionally, to assess different models’ performance, Akaike information criterion (AIC) and Bayesian information criterion (BIC) measures are typically used (instead of classical error measures, such as MAE or RMSE) given their ability to avoid the overfitting that overparameterization causes.
A problem with the AIC is that it tends to overestimate the number of parameters in the model and this effect can be important in small samples. If AIC and BIC are compared, it can be seen that the BIC penalizes the introduction of new parameters more than the AIC does, hence it tends to choose more parsimonious models.
### 4. Forecasts.
if the parameters have been properly determined and validated, the system is ready to perform forecasts.


##### References For this section

1. Valipour, M.; Banihabib, M.E.; Behbahani, S.M.R. Parameters Estimate of Autoregressive Moving Average and Autoregressive Integrated Moving Average Models and Compare Their Ability for Inflow Forecasting. J. Math. Stat. 2012, 8, 330–338.
2. Dashora, I.; Singal, S.; Srivastav, D. Streamflow prediction for estimation of hydropower potential. Water Energy Int. 2015, 57, 54–60.
3. Pfeffermann, D.; Sverchkov, M. Estimation of Mean Squared Error of X-11-ARIMA and Other Estimators of Time Series Components. J. Off. Stat. 2014, 30, 811–838.
4. Suhartono, S. Time series forecasting by using seasonal autoregressive integrated moving average: Subset, multiplicative or additive model. J. Math. Stat. 2011, 7, 20–27.
5. Miswan, N.H.; Ping, P.Y.; Ahmad, M.H. On parameter estimation for Malaysian gold prices modelling and forecasting. Int. J. Math. Anal. 2013, 7, 1059–1068.
6. Wu, B.; Chang, C.L. Using genetic algorithms to parameters (d; r) estimation for threshold autoregressive models. Comput. Stat. Data Anal. 2002, 38, 315–330.
7. Wei, S.; Lei, L.; Qun, H. Research on weighted iterative stage parameter estimation algorithm of time series model. Appl. Mech. Mater. 2014, 687–691, 3968–3971.
8. Hassan, S.; Jaafar, J.; Belhaouari, B.; Khosravi, A. A new genetic fuzzy system approach for parameter estimation of ARIMA model. In Proceedings of the International Conference on Fundamental and Applied Sciences, Kuala Lumpur, Malaysia, 12–14 June 2012; Volume 1482, pp. 455–459.
9. Chen, B.S.; Lee, B.K.; Peng, S.C. Maximum Likelihood Parameter Estimation of F-ARIMA Processes Using the Genetic Algorithm in the Frequency Domain. IEEE Trans. Signal Process. 2002, 50, 2208–2220.



---

# Unit root tests
#### **Resources**
Article: Research on the Augmented Dickey-Fuller Test for Predicting Stock Prices and Returns
book: Forecasting: Principles and Practice (3rd ed), Rob J Hyndman and George Athanasopoulos



![](https://i.imgur.com/GzkKo8a.png)
One way to determine more objectively whether differencing is required is to use a _unit root test_. These are statistical hypothesis tests of stationarity that are designed for determining whether differencing is required.
A number of unit root tests are available, which are based on different assumptions and may lead to conflicting answers. 

### Kwiatkowski-Phillips-Schmidt-Shin (KPSS) test
In this test, the null hypothesis is that the data are stationary, and we look for evidence that the null hypothesis is false. Consequently, small p-values (e.g., less than 0.05) suggest that differencing is required. The test can be computed in R using the `unitroot_kpss()` function `(Make sure that you have imported the fpp3 package)`.
For example, let us apply it to the Google stock price data.
![](https://i.imgur.com/JqUcoyS.png)
```
google_2015 |>
  features(Close, unitroot_kpss)
#> # A tibble: 1 × 3
#>   Symbol kpss_stat kpss_pvalue
#>   <chr>      <dbl>       <dbl>
#> 1 GOOG        3.56        0.01
```
The KPSS test p-value is reported as a number between 0.01 and 0.1. If the actual p-value is less than 0.01, it is reported as 0.01; and if the actual p-value is greater than 0.1, it is reported as 0.1. In this case, the p-value is shown as 0.01 (and therefore it may be smaller than that), indicating that the null hypothesis is rejected. That is, the data are not stationary. We can difference the data, and apply the test again.
```
google_2015 |>
  mutate(diff_close = difference(Close)) |>
  features(diff_close, unitroot_kpss)
#> # A tibble: 1 × 3
#>   Symbol kpss_stat kpss_pvalue
#>   <chr>      <dbl>       <dbl>
#> 1 GOOG      0.0989         0.1
```
This time, the p-value is reported as 0.1 (and so it could be larger than that). We can conclude that the differenced data appear stationary.
A similar feature for determining whether seasonal differencing is required is `unitroot_nsdiffs()`, which uses the measure of seasonal strength to determine the appropriate number of seasonal differences required. No seasonal differences are suggested if FS<0.64, otherwise one seasonal difference is suggested.
We can apply `unitroot_nsdiffs()` to the monthly total Australian retail turnover.
![](https://i.imgur.com/RQOJeHk.png)
![](https://i.imgur.com/Jk7lHYS.png)
```
aus_total_retail <- aus_retail |>
  summarise(Turnover = sum(Turnover))
aus_total_retail |>
  mutate(log_turnover = log(Turnover)) |>
  features(log_turnover, unitroot_nsdiffs)
#> # A tibble: 1 × 1
#>   nsdiffs
#>     <int>
#> 1       1

aus_total_retail |>
  mutate(log_turnover = difference(log(Turnover), 12)) |>
  features(log_turnover, unitroot_ndiffs)
#> # A tibble: 1 × 1
#>   ndiffs
#>    <int>
#> 1      1
```
Because `unitroot_nsdiffs()` returns 1 (indicating one seasonal difference is required), we apply the `unitroot_ndiffs()` function to the seasonally differenced data. These functions suggest we should do both a seasonal difference and a first difference.



## Augmented Dickey-Fuller

The Dickey-Fuller test (DF test) was originally developed by Dickey and Fuller to demonstrate the presence of a unit root. A unit root characteristic of a “non-stationary time series” character theoretically means that the mean and variance of the series are not constant over time. Nyarko mentioned the stationarity of time series data that non-stationary time series data exhibit variant properties with respect to the mean and variance. For time series data that is stationary, the mean and variance will not change over time. This test is a mathematical model that represents the relationship between a time series and its lagged values.
The formula for the DF test as formula 1:
$$ Y_t=pY_t-1+\epsilon_t\;\;\;\;(1)$$
- p is the coefficient on the lagged value of the time series.
The null hypothesis is that $Y_t$ is not stationary and $p = 1$, the error term in the Dickey-Fuller test,is identical and independently distributed
The null hypothesis for the DF test is that p = 1, which means the time series contains a unit root.However, the alternative hypothesis does not contain a unit root, and p should be smaller than 1.

**The augmented Dickey-Fuller test** is an extension of the simple DF test to determine whether financial time series data is stationary or not. This is widespread for statistical tests in econometrics.
The formula for the ADF test as formula 2:
![](https://i.imgur.com/X8T9fhi.png)
where ∆ denotes the first difference, $y_t$ is the time series being tested, t is the time   trend variable, and k is the number of lags which are added to the model to ensure that  the residuals, Compared to the DF test, the ADF test includes additional terms to account for potential trends and serial correlations in the time series. The constant term and linear trend term are helpful to capture any deterministic trends in the time series. the lagged value of y,  $Δy_t − 1, Δy_t − 2,......, Δy_t − p$ can improve the accuracy of the test by reducing type II error when the time series is nonstationary.
Similar to the DF test, the null hypothesis is that the time series contains a unit root, which means the series is non-stationary.
The series contains a unit root once the series is non-stationary and the first difference is stationary.
The ADF test is commonly used to determine the presence of unit roots [1]. For time series data, the ADF test is more flexible and accurate since the financial time series data contains trends or serial correlation.
### Example
![](https://i.imgur.com/U9RQiFJ.png)
![](https://i.imgur.com/rC7oNhP.png)
![](https://i.imgur.com/9sN39Bo.png)
![](https://i.imgur.com/pJFTJkh.png)

![](https://i.imgur.com/kVqNmt2.png)

Based on the Figure 1, the p-values for stock prices are all significantly larger than 0.05, which means it fails to reject the hypothesis. (non-stationary)
the p-values for returns are equal to 0, which is smaller than 0.05 (Table 1 and Table 2). In other words, it rejects the null hypothesis. The returns of these two companies are stationary.

###### Note:
In python code the number of lags is 30, the test may not fully capture the auto-correlation present in the data. A few lags can lead to a higher probability of a false negative result, which means it fails to reject the null when the series is stationary. Too many lags included may over-correct the auto-correlation, and it rejects the null when the series is non-stationary. Based on the autocorrelation structures and sample sizes, an appropriate number of lags can be picked for the ADF test, and the Akaike Information Criterion (AIC) is one of the various methods to determining the optimal number of lags.

##### References For this section
1. Glynn, J., Perera, N., & Verma, R. (2007). Unit root tests and structural breaks: A survey with applications.