Loosely speaking, a **time series** {$X_t$,t=0,±1,±2,… }\{$X_t$, t = 0, $\pm1$, $\pm2$, $\dots$\}{$Xt​,t=0,±1,±2,…$} is said to be **stationary** if its statistical properties remain the same over time. This means that if we shift the entire series forward or backward by some time lag $h$, the distribution of the values does not change.
![](https://i.imgur.com/EqV7EOV.png)
![](https://i.imgur.com/AWE3Dd7.png)
in (weakly stationary)  the relationship between $Xt$​ and $X_{t+h}$​ depends only on how far apart they are (the lag), not on when they occur in time.
<mark>Note</mark>:
- If the process is **stationary**, its statistical properties (mean, variance, and covariance) do not change over time.
- Thus, the strength of the relationship between $X_t$​ and $X_{t+h}$​ should be the same no matter which point in time we choose as $t$.
![](https://i.imgur.com/VB7Xgap.png)

![](https://i.imgur.com/4P0xFyf.png)

The **autocorrelation function (ACF)** measures how a time series $X_t$​ is related to its past values at different time lags h. It helps determine if past values influence future values, which is crucial for time series modeling.
##### linearity property of covariances:
$$
Cov(aX + bY + c, Z) = a Cov(X, Z) + b Cov(Y, Z).
$$

### The Sample Autocorrelation Function
The **sample autocorrelation function (sample ACF)** is an estimate of the true autocorrelation function (ACF) from a finite time series dataset. Since we typically don’t know the true statistical properties of the process, we compute the autocorrelation from observed data.
![](https://i.imgur.com/peMpBWX.png)
✅ **Sample ACF estimates the correlation between observations at different time lags.**  
✅ **Helps determine the structure of a time series (AR, MA, or seasonal patterns).**
![](https://i.imgur.com/oTJx3eR.png)
