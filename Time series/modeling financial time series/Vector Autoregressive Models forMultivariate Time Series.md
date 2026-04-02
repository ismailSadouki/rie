# The Stationary Vector Autoregression Model
![](https://i.imgur.com/GJHIdHS.png)

![](https://i.imgur.com/wh1Wdy3.png)
![](https://i.imgur.com/gYJ2ivH.png)
![](https://i.imgur.com/ZlHG8mO.png)
![](https://i.imgur.com/2oCx1E2.png)
![](https://i.imgur.com/EgGR2Rk.png)
example for chacking stationarity
![](https://i.imgur.com/DIbo9mF.png)


![](https://i.imgur.com/QbJslWY.png)
The basic VAR(p) model may be too restrictive to represent suﬃciently
the main characteristics of the data. In particular, other deterministic terms
such as a linear time trend or seasonal dummy variables may be required
to represent the data properly. Additionally, stochastic exogenous variables
may be required as well. The general form of the VAR(p) model with de-
terministic terms and exogenous variables is given by
![](https://i.imgur.com/IOBF2wv.png)
##### Explanation
![](https://i.imgur.com/HLKmKgC.png)
![](https://i.imgur.com/Yjr9gvi.png)
![](https://i.imgur.com/8tk18EH.png)
![](https://i.imgur.com/9kUG8eX.png)


# 2.1 Estimation
![](https://i.imgur.com/DXbSoUY.png)
![](https://i.imgur.com/HKbXCQG.png)
explanation
![](https://i.imgur.com/FA1c5eZ.png)
![](https://i.imgur.com/rmhVRlM.png)
![](https://i.imgur.com/a98cZrP.png)
![](https://i.imgur.com/4IbZo4C.png)
![](https://i.imgur.com/MtEYhQd.png)
![](https://i.imgur.com/GXt3byK.png)
![](https://i.imgur.com/adGg5ey.png)

![](https://i.imgur.com/AMGFG4R.png)
![](https://i.imgur.com/QTGz1UV.png)
![](https://i.imgur.com/8rvSMRQ.png)


# 2.2 Inference on Coeﬃcients
![](https://i.imgur.com/iL21gAu.png)
![](https://i.imgur.com/eVghai1.png)
![](https://i.imgur.com/ATLb7ge.png)


# 2.3 Lag Length Selection

![](https://i.imgur.com/DiLFyM8.png)
![](https://i.imgur.com/luwvdS5.png)
![](https://i.imgur.com/eb4Pcik.png)
![](https://i.imgur.com/AIr3rH5.png)
![](https://i.imgur.com/3fmIJcY.png)
![](https://i.imgur.com/krEdzD6.png)
<mark> how do we find Residual covariance matrix</mark>
![](https://i.imgur.com/pNQTiFR.png)
![](https://i.imgur.com/XUb5X9G.png)


# Forecasting
<mark>Skipped</mark>


# 4. Structural Analysis
## 4.1 Granger Causality
If a variable, or
group of variables, y1 is found to be helpful for predicting another variable,
or group of variables, y2 then y1 is said to Granger-cause y2 ;otherwise it
is said to fail to Granger-cause y2 .Formally, y1 fails to Granger-cause y2
if for all s > 0 the MSE of a forecast of y2,t+s based on (y2,t , y2,t−1 , . . .) is
the same as the MSE of a forecast of y2,t+s based on (y2,t , y2,t−1 , . . .) and
(y1,t , y1,t−1 , . . .). Clearly, the notion of Granger causality does not imply
true causality. It only implies forecasting ability.
![](https://i.imgur.com/HdIGXNB.png)
![](https://i.imgur.com/23FPi5m.png)

### Bivariate VAR Models
![](https://i.imgur.com/jYeASj2.png)
![](https://i.imgur.com/DxSBxLw.png)
![](https://i.imgur.com/a3pq2xk.png)
More on testing
![](https://i.imgur.com/7CXzDD8.png)
![](https://i.imgur.com/9f0CywC.png)
![](https://i.imgur.com/pL98SZE.png)
![](https://i.imgur.com/3zhcjyX.png)

### General VAR Models
![](https://i.imgur.com/sRTpHph.png)


## 4.2 Impulse Response Functions
