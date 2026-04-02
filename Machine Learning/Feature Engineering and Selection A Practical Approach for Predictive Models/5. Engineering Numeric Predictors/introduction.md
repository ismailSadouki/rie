Some of the problems that are prevalent with continuous
predictors can be mitigated through the type of model that we choose. For example, models that construct relationships between the predictors and the response that are based on the rank of the predictor values rather than the actual value, like trees, are immune to predictor distributions that are skewed or to individual samples that have unusual values (i.e., outliers). Other models such as K -nearest neighbors and support vector machines are much more sensitive to predictors with skewed distributions or outliers.

Continuous predictors that are highly correlated with each other is another
regularly occurring scenario that presents a problem for some models but not for
others. Partial least squares, for instance, is specifically built to directly handle
highly correlated predictors. But models like multiple linear regression or neural
networks are adversely affected in this situation.
> [!note] 🟢 What is Partial Least Squares (PLS)?
> **Partial Least Squares (PLS)** is a **supervised dimension reduction method** that was specifically designed to **handle highly correlated predictors**. It does this by:
> Creating a new set of features (called **latent variables** or **PLS components**) that are **linear combinations of the original predictors**, **but also correlated with the outcome variable**.
> In short:
> - Like **PCA**, it reduces dimensionality.
> - Unlike PCA, it **uses the target variable** to choose the components.

In this chapter we will provide such approaches for and illustrate how to handle
continuous predictors with commonly occurring issues. The predictors may:
• be on vastly different scales.
• follow a skewed distribution where a small proportion of samples are orders of
magnitude larger than the majority of the data (i.e., skewness).
• contain a small number of extreme values.
• be censored on the low and/or high end of the range.
• have a complex relationship with the response and be truly predictive but
cannot be adequately represented with a simple function or extracted by
sophisticated models.
• contain relevant and overly redundant information. That is, the information
collected could be more effectively and efficiently represented with a smaller,
consolidated number of new predictors while still preserving or enhancing the
new predictors’ relationships with the response.