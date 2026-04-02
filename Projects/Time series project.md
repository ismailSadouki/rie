
# BOX-JENKINS FORECASTING PROCEDURE

## Identification of model
## Sample size.
Building an ARlMA model requires an adequate sample size. Box and Jenkins, suggest that about 50 observations is the minimum required number. A large sample size is especially desirable when working with seasonal data.
```
nrow(data)
[1] 228
```
our data contains **228 observations** (monthly data over 19 years) is **definitely suitable** for applying the **Box and Jenkins methodology**
## Stationary series.
The UBJ-ARIMA method applies only to srarionary data series.

```
plot(data$value, type = "l", main = "Time Series Plot")

acf(data$value, lag.max = 40)
```
![](https://i.imgur.com/swIJXHq.png)







The rising trend suggests that the mean is nonstationary, and the variance also seems to get larger as the overall level rises.

When preparing the data and testing for nonstationarity If the autocorrelation starts high and decline slowly, then the series is non-stationary, and the Box-Jenkins methodology recommend differencing one or more times to get stationarity
![](https://i.imgur.com/0EUVppD.png)

as in our case the estimated acf falls slowly to zero, the mean of the data is probably not **stationary**. 



## Differencing
Differencing is used when the mean of a series is changng over time.
Applying the first difference to our data:
```
data_diff <- data %>%
  mutate(diff_value = difference(value))
data_diff %>%
  autoplot(diff_value)
```
![](https://i.imgur.com/nbjA0KD.png)

The differencing procedure seems to have been successful: the differenced series
appears to have a constant mean.
a series whch has been made stationary by appropriate differencing frequently has a mean of virtually zero. 
```
# before differencing
mean(data$value, na.rm= TRUE)
[1] 90.67149
# after differencing
mean(data_diff$diff_value, na.rm= TRUE)
[1] 0.1705314
```

However the variance of the differenced data still seems to be increasing over time.
### stationarity
to assess whether the data come from a stationary process we can perform the unit root test: Dickey Fuller test for stationarity. 
H0 : The series has a unit root.
H1 : The series does not have a unit root. The series is
stationary.

```
Augmented Dickey-Fuller Test

data:  data_diff$diff_value
Dickey-Fuller = -8.5811, Lag order = 5, p-value = 0.01
alternative hypothesis: stationary
```
since **p-value = 0.01** < 0.05 we **reject the null hypothesis** of a unit root.
from a **mean stationarity** perspective, the series is **stationary now**


### Nonstationary variance
Some realizations have a variance that changes through time. This occurs
most frequently with business and economic data covering a long time span,
especially when there is a seasonal element in the data.

##### **Variance Ratio Test**
```
library(car)
data_diff <- na.omit(data_diff)  # Remove rows with NA values

n <- length(data_diff$diff_value)
first_half <- na.omit(data_diff$diff_value)[1:(n/2)]
second_half <- data_diff$diff_value[(n/2 + 1):n]


var_first_half <- var(first_half)
var_second_half <- var(second_half)


var_ratio <- var_first_half / var_second_half
var_ratio
```

```
[1] 0.7352831
```
The **variance ratio** of 0.7352831 indicates that the variance of the first half of the series is about 74% of the variance of the second half. This suggests that the variance is **constant** across the time series, which is an indication of **variance stationarity**.
##### **Levene's Test or Bartlett’s Test** (for equal variances):
```

n <- nrow(data_diff)
first_half <- data_diff$diff_value[1:(n/2)]
second_half <- data_diff$diff_value[((n/2)+1):n]


group <- factor(c(rep(1, length(first_half)), rep(2, length(second_half))))
values <- c(first_half, second_half)


leveneTest(values ~ group)
```

```
Levene's Test for Homogeneity of Variance (center = median)
       Df F value Pr(>F)
group   1  1.8254 0.1782
      204
```

Since **p = 0.1782 > 0.05**, we again **fail to reject the null hypothesis**.($H_0$ the variances are equal between the groups.)


<mark>we conclude that the series has a stationary variance</mark>


2. An Empirical Study on Stock Price Forecasting Based on ARIMA Model. ISSN 2616-7433 Vol. 4, Issue 6: 30-37, DOI: 10.25236/FSST.2022.040605
3. 



**Logarithmic transformation.** Often a series with a nonstationary vari-
ance will be stationary in the natural logarithms. This transformation is
appropriate if the standard deviation of the original series is proportional to
the mean, so that the percenr fluctuations are constant through time.
<mark>Important findings</mark>

<mark> try Box-Cox transformation</mark>



### Draw Autocorrelation Diagram and Partial Autocorrelation Diagram
After the stationarity processing of the original sequence, we need to use ACF and PACF diagrams to identify the model form, and use the information criterion to determine the lag order p and q.
```
par(mfrow = c(2, 1))  # 1 row, 2 columns
acf(na.omit(data_diff$diff_value), lag.max = 30)
pacf(na.omit(data_diff$diff_value), lag.max = 30)
```
![](https://i.imgur.com/ors5ppD.png)
![](https://i.imgur.com/cn7TnZB.png)
<mark>Important Note:in the plot of ACF in R programming lang</mark>: 
- **Lag 0** corresponds to the autocorrelation of the series with itself — it is always 1.  **Lag 1** is the autocorrelation between $x_t$​ and $x_t−1$​, which is usually what we refer to as the **first lag**.

![](https://i.imgur.com/GXhSnKV.png)
by comparing the estimated ACF/PACF to the theoretical ACF/PACF 
we notice that the ACF dies off quickly which has a **strong negative autocorrelation** (below -0.5) at **lag 1**, which is **statistically significant**. Beyond Lag 1, the autocorrelations are within the confidence bounds, meaning they’re **not statistically significant**.
This is a classic sign of a **MA(1)** (Moving Average order 1) process.
![](https://i.imgur.com/FdE6bWA.png)

![](https://i.imgur.com/2So5CR4.png)
![](https://i.imgur.com/nt3iMAB.png)


the **PACF** at **Lag 1 to 3** show **significant negative spikes**, especially lag 1. After lag 4, partial autocorrelations become insignificant (within the bounds). This suggests an **AR(3)** or possibly **AR(1)** to **AR(3)** process.

So, a good candidate ARIMA model might be:  
**ARIMA(3,1,1)**, **ARIMA(2,1,1)**, **ARIMA(1,1,1)** (— depending on fit criteria (like AIC/BIC).




The p and q values determined by observing the Autocorrelograms and Partial Autocorrelograms after the first difference of the original sequence are only a rough estimate, and the exact values need to be compared with the nearby values.
## Estimation Stage
fitting **ARIMA(3,1,1)**, **ARIMA(2,1,1)**, **ARIMA(1,1,1)**
```
# Convert to ts object
ts_data <- ts(data_diff$diff_value, start = c(1999, 2), frequency = 12)

# ---- Fit Model: ARMA ----
fit_arma31 <- Arima(ts_data, order = c(3, 0, 1), include.mean = TRUE)
fit_arma21 <- Arima(ts_data, order = c(2, 0, 1), include.mean = TRUE)
fit_arma11 <- Arima(ts_data, order = c(1, 0, 1), include.mean = TRUE)
fit_arma31
fit_arma21
fit_arma11
```
**ARMA(3,1)**
![](https://i.imgur.com/rOgo9el.png)
**ARMA(2,1)**
![](https://i.imgur.com/YYHb4wu.png)
**ARMA(1,1)**
![](https://i.imgur.com/DQppJqm.png)

![](https://i.imgur.com/8dWr9Ig.png)

##### Coefficient quality: statistical significance.

```
# Repeat this code for each model

# Extract coefficients and standard errors
coefs <- coef(fit_arma31)
se <- sqrt(diag(fit_arma31$var.coef))

# Compute z-values and p-values
z_values <- coefs / se
p_values <- 2 * (1 - pnorm(abs(z_values)))

# Combine into a data frame
coef_summary <- data.frame(
  Coefficient = names(coefs),
  Estimate = coefs,
  StdError = se,
  Z = z_values,
  PValue = p_values
)

print(coef_summary)
```
For **ARMA(3,1)**
![](https://i.imgur.com/guUvjah.png)
**`ar3` is not statistically significant** (p ≈ 0.12), this term might be unnecessary, and we could consider the other simpler models like **ARMA(2,1)** without losing much predictive power.
We may Consider **comparing the ARMA(3,1) vs ARMA(2,1)** using AIC/BIC **and** this statistical significance. If removing `ar3` simplifies the model and doesn’t degrade performance.
For **ARMA(2,1)**
![](https://i.imgur.com/TuEGfRG.png)
Nearly all coefficients are statistically significant.

Comparison with ARMA(3,1):

- Both models have similar **log-likelihoods** and **AIC/BIC** values.
- **ARMA(3,1)** has an extra parameter (`ar3`) that is **not significant (p = 0.1168)**.
- **ARMA(2,1)** has fewer parameters and **all except ar2 are significant**, with **ar2 only marginally insignificant (p ≈ 0.089)**.

For **ARMA(1,1)**
![](https://i.imgur.com/YlPYUem.png)
All parameters significant.

######  Conclusion

| Model     | # Params | All Sig.? | AIC    | BIC    | Notes                                                            |
| --------- | -------- | --------- | ------ | ------ | ---------------------------------------------------------------- |
| ARMA(3,1) | 5        | NO        | 956.63 | 976.63 | `ar3` is **not significant**                                     |
| ARMA(2,1) | 4        | ~Yes      | 956.81 | 973.48 | `ar2` is **marginal** (p ≈ 0.089), others are significant        |
| ARMA(1,1) | 3        | YES       | 957.50 | 970.83 | All coefficients **significant**, but **slightly worse** AIC/BIC |


- **ARMA(1,1)**: Smallest, simplest model. All parameters significant. Slightly worse AIC/BIC. 
- **ARMA(2,1)**: Balanced—good AIC/BIC and most coefficients are significant.
- **ARMA(3,1)**: Adds complexity **without statistical justification** (non-significant `ar3`).

**ARMA(2,1)** offers the best trade-off between model fit and parameter significance. If interpretability and parsimony are critical, **ARMA(1,1)** is also acceptable.

##### Coefficient quality: correlation matrix.
We cannot avoid getting estimates that are correlated, but very high correlations
between estimated coefficients suggest that the estimates may be of poor
quality.

```
vcov_mat <- vcov(fit_arma11)
cor_mat <- cov2cor(vcov_mat)
print(round(cor_mat, 3))
```
![](https://i.imgur.com/sQ07Vfc.png)
![](https://i.imgur.com/uv1TFXr.png)
As a practical rule, one should suspect that the estimates are somewhat
unstable when the absolute correlation coefficient between any two esti-
mated ARIMA coefficients is 0.9 or larger. When this happens we should
consider whether some alternative models are justified by the estimated acf
and pacf. One of these alternatives might provide an adequate fit with more
stable parameter estimates [1]. Therefore, in our case the estimated models are satisfactory in this regard.

<mark>check</mark>
##### Coefficient quality: coefficient near-redundancy




##### Checking coefficients for stationarity and invertibility.
The invertibility requirement applies only to the moving-average part of
the models. The requirement for an $ARMA(1,1), ARMA(2,1), ARMA(3,1)$ is the same as that for an MA(1): $|\theta| <1$, the estimated coefficient for our models $\theta=-0.6767,-0.5816, -0.4194$ clearly satisfies this requirement. 

**Checking for Stationarity of the coefficients** 
![](https://i.imgur.com/I8rji60.png)

For the ARMA(1,1) we have $|\theta=-0.1679|< 1$ hence the coefficient is stationary.
and for the ARMA(2,1) we have $|\theta_2=-0.1531|<1$, $\theta_2+\theta_1=-0.4299$ and $\theta_2-\theta_1=0.1237$. hence the stationarity checked.













##### Closeness of fit root-mean-squared error.
the model with the smaller RMSE tends to have a smaller forecast-error
Variance.Two or more models could give essentially the
same results in most respects. That is, they could be equally parsimonious,
equally justifiable based on the estimated acf s and pacf s, and so forth. But
if one model has a noticeably lower RMSE, we prefer that one because it
fits the available data more closely.
##### Closeness of f i t mean absolute percent error.
Using the example shown in Figure 8.2, we could report that this model
fits the available data with an average error of +0.71%. The MAPE
suggests, very roughly, the kind of accuracy we could expect from forecasts
produced by rhls model. However, the preferred way of conveying forecast
accuracy is to derive confidence intervals for the forecasts. This latter topic
is discussed in Chapter 10.


### 8 3 &timation-stage results have we found a good model?
1. it is parsimonious;
2. it is stationary;
3. it is invertible;
4. it has estimated coefficients of high quality;
5. it has statistically independent residuals;
6. it fits the available data satisfactorily; and
7. it produces sufficiently accurate forecasts.




# DIAGNOSTIC CHECKING STAGE
In this stage we determine if a model is statistically adequate. In particular, we test if the random shocks are independent. If this assumption is not satisfied, there is an autocorrelation pattern in the original series that has not been explained by the ARIMA model. Our goal, however, is to build a model that fully explains any autocorrelation in the original series [1].
#### Are the random shocks independent?
![](https://i.imgur.com/dGJ27h8.png)

Inspection does not suggest that the variance is changing systematically over time.
also there may be unusual event between the year 2015-2016 that may need further investigations. we must expect some residuals to be large just by chance. But they might also represent data that were incorrectly recorded, or perturbations to the data caused by identifiable exogenous events.

To test the hypothesis that the random shocks are independent we
construct a residual acf. This acf is like any estimated acf except we
construct it using the estimation residuals $\hat{a}_t$, instead of the realization $z_t$,
![](https://i.imgur.com/E8g9bz5.png)
from the acf and pacf of the residuals we conclude that the random shocks are independent. (ALL the p-values are larger than 0.05)

###### Ljung-Box test
Another way to deal with potentially underestimated residual acf
t-values is to test the residual autocorrelations as a set rather than individu-
ally. An approximate chi-squared statistic (the Ljung-Box statistic) is
available for this test.
```
Box.test(residuals(fit_arma21), lag = 20, type = "Ljung-Box")
Box.test(residuals(fit_arma11), lag = 20, type = "Ljung-Box")
```
![](https://i.imgur.com/w3qz3RL.png)
![](https://i.imgur.com/h4aoILH.png)
**p-value > 0.05** For both the models ARMA(2,1) and ARMA(1,1). we Fail to reject the null hypothesis that residuals are uncorrelated (white noise).
This suggests the models capture the time dependence in the data sufficiently well, at least up to lag 20.

**Ljung-Box test on squared residuals** (**ARCH effects** (non-constant variance)):
**Null Hypothesis**: No autocorrelation in the **squared residuals**, i.e., no ARCH effects.
```
Box.test(residuals(fit_arma21)^2, lag = 20, type = "Ljung-Box")
Box.test(residuals(fit_arma11)^2, lag = 20, type = "Ljung-Box")
```
![](https://i.imgur.com/5R9lo9z.png)
Since the **p-value is extremely high**, we **fail to reject** the null hypothesis.







# FORECASTING STAGE
![](https://i.imgur.com/tbU54m3.png)











#### Notes
- The goal of UBJ analysis is to find an ARIMA model with the
smallest number of estimated parameters needed to fit adequately the
patterns in the available data.
- At the identification stage we tentatively select one or more
ARIMA models by looking at two graphs derived from the available data.
These graphs are called an estimated autocorrelation function (acf) and an
estimated partial autocorrelation function (pacf). We choose a model whose
associated theoretical acf and pacf look like the estimated acf and pacf
calculated from the data.
- To focus on the stochastic (nondeterministic) components in a sta-
tionary time series, we subtract out the sample mean I, which is an estimate
of the parameter p. We then analyze these data expressed in deviations from
the mean: zt = zt-zbar
A series expressed in deviations from the mean has the same statisti-
cal properties as the original series (e.g., it has the same variance and
estimated acf) except the mean of the differenced series is identically zero.
- The estimated acf for a series whose mean is stationary drops off
rapidly to zero. If the mean is nonstationary the estimated acf falls slowly
toward zero.
- The number of useful estimated autocorrelations is about n/4, that
is. about one-fourth of the number of observations.
- Some realizations have a variance that changes over time. Such
realizations must be transformed to induce a constant variance before the
UBJ method may be used. It is possible that no suitable transformation will
be found.
- If the mean ( p , ) of a differenced series ( w , ) is assumed to be zero.
the resulting ARIMA model for both w, and the integrated series z, has a
constant term of zero. Any trend element in forecasts from such a model is
stochastic, not deterministic.s
- (iii) fit the chosen model to
subsets of the available realmtion to see if the estimated coefficients change
significantly.

![](https://i.imgur.com/GXhSnKV.png)
![](https://i.imgur.com/sKJUGpu.png)
![](https://i.imgur.com/AvOTCDL.png)

![](https://i.imgur.com/x8M8Mfl.png)
![](https://i.imgur.com/e8lvbvo.png)
![](https://i.imgur.com/xw1YZir.png)
![](https://i.imgur.com/S9l17aM.png)


#### How do we decide if a model is a good one
(1) A good model is parsimonious.
(2) A good AR model is stationnary.
(3) A good MA model is invertible
(4) A good model has high-quuliw esrimated coeflicients at the estimation
stage. We want to avoid a forecasting model
which represents only a chance relationship, so we want each 6 or 6
coefficient to have an absolute t-statistic of about 2.0 or larger. This means
each estimated 6 or 6 coefficient should be about two or more standard
errors away from zero. If this condition is met, each 6 or 8 is statistically
different from zero at about the 5% level.
In addition, estimated phi and theta coefficients should not be too hghly
correlated with each other. If they are they tend to be somewhat unstable
even if they are statistically significant.
(5) A good model has statistaclly indep residuals
If the residuals are statistically independent, this is important evidence that we
cannot improve the model further by adding more AR or MA terms.


### ARMA processes.
Mixed processes have theoretical acfs with both AR and MA characteristics. The acf tails off toward zero after the first q - p lags with either exponential decay or a damped sine wave. The theoretical pacf tails off to zero after the first p - q lags. In practice. p and q are usually not larger than two in a mixed model for nonseasonal data.
Because q = 1 and p = 1 for these examples, q - p = 0, and each acf in
Figure 6.5 tails off toward zero starting from lag 1. Likewise, p - q = 0 in
these examples, so each pacf in Figure 6.5 also tails off toward zero starting
from lag 1.

##### stationarity check
![](https://i.imgur.com/I8rji60.png)
If p = 0, we have either a pure MA model or a whtte-noise series. All
pure MA models and white noise are stationary, so there are no stationarity
conditions to check.
The stationarity conditions become complicated when p > 2. For-
tunately, ARIMA models with p > 2 do not occur often in practice. When p
exceeds 2 we can at least check this necessary (but not sufficient) stationar-
ity condition:![](https://i.imgur.com/rxAMOAt.png)

Checking for stationarity in practice. Suppose we have a realization in
hand and we want to develop an ARIMA model to forecast future values of
this variable. We have three ways to determine if the stationarity require-
ment is met:
(i) Examine the realization visually to see if either the mean or the
variance appears to be changing over time.
(ii) Examine the estimated acf to see if the autocorrelations move rapidly
toward zero. In practice, “rapidly” means that the absolute t-values of the
estimated autocorrelations should fall below roughly 1.6 by about lag 5 or 6.
These numbers are only guidelines, not absolute rules. If the acf does not
fall rapidly to zero, we should suspect a nonstationary mean and consider
differencing the data.
(iii) Examine any estimated AR coefficients to see if they satisfy the
stationarity conditions (6.7), (6.8), and (6.9).

##### 6.3 Invertibility
Conditions on the MA coefficients.
![](https://i.imgur.com/P8zGTZt.png)
A reason for invertibility. There is a common-sense reason for the
invertibility condition: a noninvertible ARIMA model implies that the
weights placed on past t observations do not decline as we move further
into the past; but common sense says that larger weights should be attached
to more recent observations. Invertibility ensures that this result holds.*

### 7.3 Differencing and deterministic trends
![](https://i.imgur.com/XdI5Zyg.png)
Estimate two models, one with a nonzero mean (and constant) and
one without, and check the forecasting accuracy of both models.
Accord ing to Box and Jenluns,
In many applications. where no physical reason for a deterministic
component exists, the mean of w can be assumed to be zero unless
such an assumption proves contrary to facts presented by the data. I t
is clear that, for many applications, the assumption of a stochastic
trend is often more realistic than the assumption of a deterministic
trend. This is of special importance in forecasting a time series, since a
stochastic trend does not necessitate the series to follow the identical
pattern which it has developed in the past. [l, pp- 92-93. Quoted by
permission.]


