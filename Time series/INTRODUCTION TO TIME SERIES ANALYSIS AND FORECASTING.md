### STATISTICS BACKGROUND FOR FORECASTING

###### lead − $𝝉$ forecast error
$$
e_t (𝜏) = y_t − ŷ_ t (t − 𝜏).
$$
###### residual
$$ e_t = y_t − ŷ_ t .$$
The reason for this careful distinction between forecast errors and residuals
is that models usually fit historical data better than they forecast. That is,
the residuals from a model-fitting process will almost always be smaller
than the forecast errors that are experienced when that model is used to
forecast future observations.


---
#### 2.2 GRAPHICAL DISPLAYS
##### Plotting Smoothed Data 
Sometimes it is useful to overlay a smoothed version of the original data on the original time series plot to help reveal patterns in the original data. There are several types of data smoothers that can be employed. One of the simplest and most widely used is the ordinary or simple moving average.
### **Why Use Moving Averages?**

1. **Removes Short-Term Noise**
    - Useful for highlighting long-term trends in data.
2. **Reveals Patterns**
    - Helps detect trends and cyclic behaviors.
3. **Useful for Forecasting & Anomaly Detection**
    - Aids in decision-making in finance, weather, and industry.
![](https://i.imgur.com/Wqg1Nof.png)



---
### 2.3 NUMERICAL DESCRIPTION OF TIME SERIES DATA
#### 2.3.1 Stationary Time Series
A time series is said to be strictly stationary if its properties are not affected
by a change in the time origin.
Stationary implies a type of statistical equilibrium or stability in the
data.
#### 2.3.2 Autocovariance and Autocorrelation Functions
If a time series is stationary this means that the joint probability distri-
bution of any two observations, say, yt and yt+k , is the same for any two
time periods t and t + k that are separated by the same interval k.

The covariance between $y_t$ and its value at another time period, say, **$y_t+k$**
is called the **autocovariance** at **lag $k$**, defined by
$$
𝛾_k = Cov(y_t , y_{t+k} ) = E[(y_t − 𝜇)(y_{t+k} − 𝜇)].
$$
The collection of the values of $𝛾_k$ , k = 0, 1, 2, … is called the **autocovariance function**.
Note that the autocovariance at lag k = 0 is just the variance of the time series
$$𝛾_0 = 𝜎_y^2$$
which is constant for a stationary time series.

The **autocorrelation coefficient at lag k** for a stationary time series is
![](https://i.imgur.com/G4MbOSe.png)
The collection of the values of 𝜌k , k = 0, 1, 2, … is called **the autocorrelation function (ACF)**.
**Properties**:
- Note that by definition $𝜌_0 = 1$
- he ACF is independent of the scale of measurement of the time series, so it is a dimensionless quantity
- $𝜌_k = 𝜌_{−k}$ ; that is, the ACF is symmetric around zero
- If a time series has a finite mean and autocovariance function it is said to be second-order stationary (or weakly stationary of order 2).
- If,  the joint probability distribution of the observations at all times is multivariate normal, then that would be sufficient to result in a time series that is strictly stationary.
![](https://i.imgur.com/SbRp3RQ.png)
A good general rule of thumb is that at least 50 observations are required
to give a reliable estimate of the ACF, and the individual sample autocor-
relations should be calculated up to lag K, where K is about T/4.

If we make the assumption that the observations are uncorrelated, that is, 𝜌k = 0 for all k, then the variance of the sample autocorrelation coefficient is
$$ Var(r_k) ≈ \frac{1}{T}$$
#### 2.3.3 The Variogram
The variogram $G_k$ measures variances of the differences between observations
that are k lags apart, relative to the variance of the differences that are one
time unit apart (or at lag 1). The variogram is defined mathematically as
$$
G_k = \frac{Var(y_{t+k}-y_t)}{Var(y_{t+1}-y_t}
$$
and the values of $G_k$ are plotted as a function of the lag k. If the time series
is stationary, it turns out that
$$
	G_k=\frac{1-𝜌_k}{1-𝜌_1}
$$
but for a stationary time series $𝜌_k → 0$ as k increases, so when the variogram
is plotted against lag k, $G+k$ will reach an asymptote $1∕(1 − 𝜌_1 )$. However,
if the time series is **nonstationary**, $G_k$ will **increase monotonically**.
![](https://i.imgur.com/zdCJLeW.png)

EX of variogram:
![](https://i.imgur.com/UjIIWkx.png)
Notice that the sample variogram generally converges to a stable level and then fluctuates around it. This is consistent with a stationary time series, and it provides additional evidence that the chemical process viscosity data are stationary.

---
### 2.4 USE OF DATA TRANSFORMATIONS AND ADJUSTMENTS
#### 2.4.1 Transformations
A very popular type of data transformation to deal with nonconstant
variance is the power family of transformations, given by
![](https://i.imgur.com/5y2c9nQ.png)
Where 
$$
ẏ = e^{(1/T)\sum_{t=1}^T{lny_t}}
$$
is the geometric mean of the observations.
If 𝜆 = 1, there is no transformation. Typical values of 𝜆 used with time
series data are 𝜆 = 0.5 (a square root transformation), 𝜆 = 0 (the log trans-
formation), 𝜆 = −0.5 (reciprocal square root transformation), and 𝜆 = −1
(inverse transformation).The divisor ẏ 𝜆−1 is simply a scale factor that
ensures that when different models are fit to investigate the utility of dif-
ferent transformations (values of 𝜆), the residual sum of squares for these
models can be meaningfully compared.


**The log transformation** (𝜆 = 0) is used frequently in situations where the vari-
ability in the original time series increases with the average level of the
series.
When the standard deviation of the original series increases lin-
early with the mean, the log transformation is in fact an optimal variance-
stabilizing transformation.

#### 2.4.2 Trend and Seasonal Adjustments
A time series that exhibits a trend is a **nonstationary** time series. Modeling and forecasting of such a time series is greatly simplified if we can eliminate the trend.
Modeling and forecasting of such a time series is greatly simplified if we
can eliminate the trend. One way to do this is to fit a regression model
describing the trend component to the data and then subtracting it out of
the original observations, leaving a set of residuals that are free of trend.

Another approach to removing trend is by differencing the data; that is,
applying the difference operator to the original time series to obtain a new
time series, say,
$$
x_t = y_t − y_{t−1} = ∇y_t ,
$$
**Differencing has two advantages:**
- it does not require estimation of any parameters, so it is a more parsimonious (i.e., simpler)
- model fitting assumes that the trend is fixed throughout the time series history and will remain so in the (at least immediate) future.In other words, the trend component, once estimated, is assumed to be deterministic. Differencing can allow the trend component to change through time. The first difference accounts for a trend that impacts the change in the mean of the time series, the second difference accounts for changes in the slope of the time series
**When to Use Differencing?**
- **One difference** is usually enough to remove a simple trend.
- **Two differences** are used when the trend is more complex (e.g., accelerating growth).

Differencing can also be used to eliminate seasonality. Define
a lag—d seasonal difference operator as
$$
∇_d y_t = (1 − B^d ) = y_t − y_{t−d} .
$$
![](https://i.imgur.com/r3J0p9O.png)
![](https://i.imgur.com/ZgB5Kbj.png)
![](https://i.imgur.com/EW2oJIx.png)
There is also a “classical” approach to decomposition of a time series
into trend and seasonal components
The general mathematical model for this decomposition is$$y_t = f (S_t , T_t , 𝜀_t ),$$
where St is the seasonal component, Tt is the trend effect  and 𝜀t is the random error component.
There are usually two forms for the function f ; an additive model
$$y_t = S_t + T_t + 𝜀_t$$
and a multiplicative model
$$y_t = S_t T_t 𝜀_t .$$
The additive model is appropriate if the magnitude (amplitude) of the
seasonal variation does not vary with the level of the series, while the
multiplicative version is more appropriate if the amplitude of the seasonal
fluctuations increases or decreases with the average level of the time series.




---


### 2.5 GENERAL APPROACH TO TIME SERIES MODELING AND FORECASTING
The basic steps in modeling and forecasting a time series are as follows:
1. Plot the time series and determine its basic features, such as whether
trends or seasonal behavior or both are present. Look for possible
outliers or any indication that the time series has changed with respect to its basic features (such as trends or seasonality) over the time period
history.
2. Eliminate any trend or seasonal components. Also consider using data
transformations, particularly if the variability in the time series seems
to be proportional to the average level of the series. The objective of
these operations is to produce a set of stationary residuals.
3. Develop a forecasting model for the residuals.
4. Validate the performance of the model (or models) from the previous
step.
5. To forecast values on the scale of the original time series yt ,
reverse the transformations and any differencing adjustments made
to remove trends or seasonal effects.
6. For forecasts of future values in period T + 𝜏 on the original scale,
if a transformation was used, say, xt = ln yt , then the forecast made
at the end of period T for T + 𝜏 would be obtained by reversing the
transformation
7. If prediction intervals are desired for the forecast (and we recom-
mend doing this), construct prediction intervals for the residuals and
then reverse the transformations made to produce the residuals as
described earlier.
8. Develop and implement a procedure for monitoring the forecast to
ensure that deterioration in performance will be detected reasonably
quickly.





---



### 2.6 EVALUATING AND MONITORING FORECASTING MODEL PERFORMANCE

#### 2.6.1 Forecasting Model Evaluation
It is customary to evaluate forecasting model performance using the
one-step-ahead forecast errors
$$
e_t (1) = y_t − ŷ _t (t − 1)
$$
where $ŷ_ t (t − 1)$ is the forecast of $y_t$ that was made one period prior.

Standard measures of forecast accuracy are the average error or mean error
$$
ME =1/n\sum{e_t} (1) \,\,\,\;\;\;\;\;...(2.32) 
$$
the mean absolute deviation (or mean absolute error)
$$
MAD =
1/n\sum |e_t (1)|,
$$
and the mean squared error
$$
MSE =1/n\sum[{e_t} (1)]^2
$$

The mean forecast error in Eq. (2.32) is an estimate of the expected value
of forecast error, which we would hope to be zero; that is, the forecastingtechnique produces unbiased forecasts. If the mean forecast error differs appreciably from zero, bias in the forecast is indicated. If the mean forecast error drifts away from zero when the forecasting technique is in use, this
can be an indication that the underlying time series has changed in some
fashion, the forecasting technique has not tracked this change, and now
biased forecasts are being generated.


The MSE is a direct estimator of the variance of the one-step-ahead forecast
errors
$$
𝜎̂ _{e(1)}^2
= MSE
$$
The one-step-ahead forecast error and its summary measures, the ME,
MAD, and MSE, are all ==scale-dependent== measures of forecast accuracy;
![](https://i.imgur.com/N3MBUSc.png)


<mark>Note</mark>:
- If a time series consists of uncorrelated observations and has constant
	variance, we say that it is white noise.
- If, in addition, the observations in this time series are normally distributed, the time series is Gaussian white noise.
- If a time series is white noise, the distribution of the sample autocorrelation coefficient at lag k in large samples is approximately normal with mean zero and variance 1/T; That is $$r_k∼N(0,\frac{1}{T}) $$
- Therefore we could test the hypothesis $H_0 : 𝜌_k = 0$ using the test statistic $$ Z_0=\frac{r_k}{\sqrt{1/T}}=r_k\sqrt{T} $$
	==When $ρ_k=0$, it means that there is **no linear correlation** between the time series values at time t and time t−k. This suggests that knowing the value at time t−k gives no information about the value at time t==

	![](https://i.imgur.com/f92i5k6.png)
	![](https://i.imgur.com/7IANTw0.png)
#### 2.6.2 Choosing Between Competing Models
When evaluating the fit of the model to historical data, there are several
criteria that may be of value. The mean squared error of the residuals is
$$
s^2=\frac{\sum_{t=1}^Te^2_t}{T-p}
$$
where T periods of data have been used to fit a model with p parameters
and et is the residual from the model-fitting process in period t,The mean
squared error s2 is just the sample variance of the residuals and it is an
estimator of the variance of the model errors.
Another criterion is the R-squared statistic 
$$
R^2=1-\frac{\sum_{t=1}^Te^2_t}{\sum_{t=1}^T(Y_t-\bar{y})^2}
$$
selecting the model that maximizes R2 is equivalent to selecting the model that minimizes the sum of the squared residuals.
Large values of R2 suggest a good fit to the historical data. Because the residual sum of squares always decreases when parameters are added to a model, relying on R2 to select a forecasting model encourages overfitting or putting in more parameters than are really necessary to obtain good forecasts.
A large value of R2 does not ensure that the out-of-sample one-step-ahead forecast errors will be small.
![](https://i.imgur.com/dxPKXg5.png)


#### 2.6.3 Monitoring a Forecasting Model





---
---
---
---
---
---
---
---
---










---

# REGRESSION ANALYSIS AND FORECASTING

The simple linear regression model
$$
y = 𝛽0 + 𝛽_1 x + 𝜀
$$
The regression model for time series data is written as
$$
y_t = 𝛽_0 + 𝛽_1 x_{t1} + 𝛽_2 x_{t2} + ⋯ + 𝛽_k x_{tk} + 𝜀_t,\,\,\;t = 1, 2, … , T
$$
### 3.2 LEAST SQUARES ESTIMATION IN LINEAR REGRESSION MODELS
**In cross-section data**, We assume that the error term 𝜀 in the model has expected value E(𝜀) = 0 and variance Var (𝜀) = 𝜎 2 , and that the errors 𝜀i , i = 1, 2, … , n are uncorrelated random variables.
![](https://i.imgur.com/wqK6NDw.png)
![](https://i.imgur.com/gwYNab2.png)
These equations are called the least squares normal equations.Note
that there are p = k + 1 normal equations,The solutions to the normal equations will be the least squares estimators of the model regression coefficients.
![](https://i.imgur.com/f00MALg.png)
$X$ is usually called the **model matrix**.
![](https://i.imgur.com/SGiFVOl.png)
Equation (3.12) is just the matrix form of the least squares normal equations. It is identical to Eq. (3.10). To solve the normal equations, multiply both sides of Eq. (3.12) by the inverse of X′ X (we assume that this inverse exists). Thus the least squares estimator of $\hat{𝜷}$ is
$$ \hat{𝜷̂}= (X′ X)^{-1} X′ y$$
The fitted values of the response variable from the regression model are
computed from
$$ \hat{y}=X\hat{𝜷̂}$$
The difference between the actual observation $y_i$ and the corresponding
fitted value is the residual $e_i = y_i − ŷ_ i , i = 1, 2, … , n$. The n residuals can
be written as an (n × 1) vector denoted by
$$ e = y − ŷ = y − X\hat𝜷̂$$
In addition to estimating the regression coefficients 𝛽0 , 𝛽1 , … , 𝛽k , it
is also necessary to estimate the variance of the model errors, 𝜎 2 . The
estimator of this parameter involves the sum of squares of the residuals
$$ SS_E =(y-X\hat𝜷) ̂ ′ (y − X\hat𝜷)$$
We can show that $E(SS_E ) = (n − p)𝜎^2$ , so the estimator of $𝜎 ^2$ is the residual
or mean square error$$𝜎̂ ^2= \frac{SS_E}{n-p} $$
the least squares estimator $\hat𝜷̂$i s an unbiased estimator of the model parameters
𝜷; that is$$E(\hat\beta)=\beta$$
The variances and covariances of the estimators 𝜷̂ are contained in a (p × p)
covariance matrix
$$ Var(\hat\beta)=𝜎 ^2 (X′ X)^{−1} $$
The variances of the regression coefficients are on the main diagonal of
this matrix and the covariances are on the off-diagonals.


### 3.3 STATISTICAL INFERENCE IN LINEAR REGRESSION
we describe several important hypothesis-testing procedures and a confidence interval estimation procedure. These procedures require that the errors $𝜀_i$ in
the model are normally and independently distributed with mean zero and variance $𝜎^2$ , abbreviated $NID(0, 𝜎^2 )$. As a result of this assumption, the observations $y_i$ are normally and independently distributed with mean $𝛽_0 +\sum_{j=1}^k{\beta_jx_{ij}}$ and variance $𝜎^2$ .
#### 3.3.1 Test for Significance of Regression
whether there is a linear relationship between the response variable y and a subset of the predictor or regressor variables x1 , x2 , … , xk . The appropriate hypotheses are
$$
H0 : 𝛽_1 = 𝛽_2 = ⋯ = 𝛽_k = 0 \,\,\,...(3.19)
$$
$$H1 : at \,least\, one\, 𝛽_j ≠ 0$$
Rejection of the null hypothesis implies that at least one of the predictor variables x1 , x2 , … , xk contributes significantly to the model.
The test procedure involves an analysis of variance partitioning of the total
sum of squares
$$
SS_T=\sum_{i=1}^n(y_i-\bar{y})^2
$$
into a sum of squares due to the model (or to regression) and a sum of
squares due to residual (or error), say,
$$SS_T=SS_R+SS_E$$
Now if the null hypothesis in Eq. (3.19) is true and the model errors are
normally and independently distributed with constant variance as assumed,
then the test statistic for significance of regression is
$$
F_0=\frac{SS_R/k}{SS_E/(n-p)}
$$
and one rejects H0 if the test statistic $F_0$ exceeds the upper tail point of the
F distribution with k numerator degrees of freedom and n − p denominator
degrees of freedom, $F_{𝛼,k,n−p}$.
we could use the P-value approach to hypothesis testing and thus reject the null hypothesis if the P-value for the statistic F0 is less than 𝛼.

The test for significance of regression is usually summarized in an
analysis of variance (ANOVA) table such as Table 3.4. Computational
formulas for the sums of squares in the ANOVA are
![](https://i.imgur.com/8ajMjjs.png)
![](https://i.imgur.com/4V41WQw.png)


The statistic R2 is a measure of the amount of reduction in the variability
of y obtained by using the predictor variables x1 , x2 , … , xk in the model.It is
a measure of how well the regression model fits the data sample.
a large value of R2 does not necessarily imply that the regression model is a good one. Adding a variable to the model will never cause a decrease in R2 , even in situations where the additional variable is not statistically significant.
In almost all cases, when a variable is added to the regression model R2 increases. As a result, over reliance on R2 as a measure of model adequacy often results in overfitting;
$$
R^2=\frac{SS_R}{SS_T}=1-\frac{SS_E}{SS_T}
$$
$$
R^2_{Adj}=1-\frac{SS_E/(n-p)}{SS_T/(n-1)}
$$
In general, the **adjusted** $R^2$ statistic will not always increase as variables
are added to the model. In fact, if unnecessary regressors are added, the
value of the adjusted R2 statistic will often decrease.
models with a large value of the adjusted R2 statistic are usually considered good
regression models.
Furthermore, the regression model that maximizes the adjusted R2 statistic is also the model that minimizes the residual mean square