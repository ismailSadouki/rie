

linear regression gives us a way to predict the value of Y given X by modeling the conditional mean of Y given X as a linear function of X. As long as the observation for
which Y is to be predicted is drawn from the same population as the data used to estimate
the linear regression, the regression line provides a way to predict Y given X.


### The Linear Regression Model
The easiest starting point for mod-eling a function of X, when X can take on multiple values, is to suppose that it is linear. In the case of test scores and class size, this linear function can be written
$$
E(testScore|classSize) = \beta_0 + \beta_{classSize} * classSize \,\, ...(4.1)

$$
Equation (4.1) tells you what the test score will be, on average, for districts with
class sizes of that value.
to make a prediction for a given district, we know that prediction will
not be exactly right: The prediction will have an error. Stated mathematically, for any
given district the imperfect relationship between class size and test score can be
written
$$
testScore = \beta_0 + \beta_{classSize} * classSize+error \,\, ...(4.2)

$$

second component that represents the error made using the prediction in Equation (4.1).


##### In general
Suppose you have a sample of $n$ districts. Let $Y_i$ be the average test
score in the $i$th district, and let $X_i$ be the average class size in the $i$th district, so that
Equation $(4.1)$ becomes$$E(Y_i|X_i)=\beta_0+\beta_1X_i$$
Let $u_i$ denote the error made by predicting $Y_i$ using its conditional mean. Then Equation (4.2) can be written more generally a$$Y_i=\beta_0+\beta_1X_i+u_i\,\,...(4.3)$$
Equation (4.3) is **the linear regression model with a single regressor**, where $X$ is the **independent** variable or the **regressor**.
The term $u_i$ in Equation $(4.3)$ is the error term. In the context of the prediction
problem, $u_i$ is the difference between $Y_i$ and its predicted value using the population
regression line.

![](https://i.imgur.com/lpTtXkI.png)


## The Ordinary Least Squares Estimator
The OLS estimator chooses the regression coefficients so that the estimated regres-
sion line is as close as possible to the observed data. where closeness is measured by
the sum of the squared mistakes made in predicting Y given X.

Let $b_0$ and $b_1$ be some estimators of $b_0$ and $b_1$. The regression line based on these estimators is $b_0 + b_1X$, Thus the mistake made in predicting the $i$th observation is $Y_i - (b_0 + b_1X_i) = Y_i - b_0 - b_1X_i$.
The sum of these squared prediction mistakes over all n observations is 
$$\sum(Y_i - b_0 - b_1X_i)^2\,\,....(4.4)$$
The estimators of the intercept and slope that minimize the sum of squared mis-takes in Expression (4.4) are called the **ordinary least squares (OLS)** estimators of $b_0$ and $b_1$.

The **OLS regression line**, also called the **sample regression line** or **sample regression function**, is the straight line constructed using the OLS estimators: $\hat{\beta_0} + \hat{\beta_1}X$.

The **residual** for the $i$th observation is the difference between $Y_i$ and its **predicted** value: $\hat{u_i}=Y_i-\hat{Y_i}$


## Measures of Fit and Prediction Accuracy

### The $R^2$
The **regression $R^2$** is the fraction of the sample variance of Y explained by (or predicted
by) $X$
Mathematically, the R can be written as the ratio of the explained sum of squares
to the total sum of squares.
$$
ESS = \sum(\hat{Y_i}-\bar{Y})^2
$$
$$
TSS = \sum(Y_i-\bar{Y})^2
$$
$$
R^2= \frac{ESS}{TSS}\,\,\,....(4.14)
$$
Alternatively, the R2 can be written in terms of the fraction of the variance of $Y_i$ not
explained by $X_i$.

The **sum of squared residuals (SSR)** is the sum of the squared OLS residuals:
$$
SRR = \sum{\hat{u_i}^2}\,\,\,...(4.15)
$$
NOTE: <mark>TSS = ESS + SRR</mark>
Thus the R2 also can be expressed as 1 minus the ratio of the sum of squared residuals to the total sum of squares:
$$
R^2= 1- \frac{SRR}{TSS} \,\,\,....(4.16)
$$
<mark>Finally</mark>, the $R^2$ of the regression of $Y$ on the single regressor $X$ is the square of the
correlation coefficient between $Y$ and $X$

![](https://i.imgur.com/fEYySEf.png)


### The Standard Error of the Regression
The **standard error of the regression (SER)** is an estimator of the standard deviation
of the regression error $u_i$.
the SER is a measure of the spread of the observations around the regression line, measured in the units of the dependent variable.
$$
SER = s_{\hat{u}}=\sqrt{s_{\hat{u}}^2}, where\,s_{\hat{u}}^2=\frac{1}{n-2}\sum\hat{u_i}^2=\frac{SSR}{n-2}\,\,\,....(4.17)
$$
The reason for using the divisor $n - 2$: It corrects for a slight downward bias introduced because two regression coefficients were estimated. This is called a “degrees of freedom” correction because when two coefficients were estimated ($\beta_0$ and $\beta_1$), two “degrees of freedom” of the data were lost, so the divisor in this factor is $n - 2$. When n is large, the difference among dividing by n, by n - 1, or by n - 2 is negligible.

### Prediction Using OLS
A common way to report a prediction and its accuracy is as the prediction + or - The SER that is:
$$ \hat{Y}+- s_{\hat{u}}$$














that they equal speciﬁed values. She then samples the
population and compares her observations with the hypothesis. If the observations
disagree with the hypothesis, the scientist rejects it. If not, the scientist concludes
either that the hypothesis is true or that the sample did not detect the difference
between the real and hypothesized values of the population parameters.