we discuss in this chapter some ways in which the simple linear model can be improved, by replacing plain least squares fitting with some alternative fitting procedures. Why might we want to use another fitting procedure instead of least ? As we will see, alternative fitting procedures can yield better prediction accuracy and model interpretability. 
- rediction Accuracy: Provided that the true relationship between the
response and the predictors is approximately linear, the least squares
estimates will have low bias. If n + p—that is, if n, the number of
observations, is much larger than p, the number of variables—then the
least squares estimates tend to also have low variance, and hence will
perform well on test observations. However, if n is not much larger
than p, then there can be a lot of variability in the least squares fit,
resulting in overfitting and consequently poor predictions on future
observations not used in model training. And if p > n, then there is no
longer a unique least squares coefficient estimate: there are infinitely many solutions. Each of these least squares solutions gives zero error
on the training data, but typically very poor test set performance
due to extremely high variance.1 By constraining or shrinking the
estimated coefficients, we can often substantially reduce the variance
at the cost of a negligible increase in bias. This can lead to substantial
improvements in the accuracy with which we can predict the response
for observations not used in model training.
- Model Interpretability: It is often the case that some or many of the
variables used in a multiple regression model are in fact not associ-
ated with the response. Including such irrelevant variables leads to
unnecessary complexity in the resulting model. By removing these
variables—that is, by setting the corresponding coefficient estimates
to zero—we can obtain a model that is more easily interpreted. Now
least squares is extremely unlikely to yield any coefficient estimates
that are exactly zero. In this chapter, we see some approaches for au-
tomatically performing feature selection or variable selection—that is, for excluding irrelevant variables from a multiple regression model.

