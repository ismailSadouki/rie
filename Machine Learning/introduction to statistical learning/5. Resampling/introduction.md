The process of evaluating a model’s performance is known as model assessment, whereas the process of selecting the proper level of flexibility for a model is known as model selection. The bootstrap is used in several contexts, most commonly to provide a measure of accuracy of a parameter estimate or of a given statistical learning method.

The test error is the average error that results from using
a statistical learning method to predict the response on a new observation—
that is, a measurement that was not used in training the method. Given
a data set, the use of a particular statistical learning method is warranted
if it results in a low test error. The test error can be easily calculated if a
designated test set is available. Unfortunately, this is usually not the case.
In contrast, the training error can be easily calculated by applying the
statistical learning method to the observations used in its training.

In Sections 5.1.1–5.1.4, for simplicity we assume that we are interested
in performing regression with a quantitative response. In Section 5.1.5 we
consider the case of classification with a qualitative response. As we will
see, the key concepts remain the same regardless of whether the response
is quantitative or qualitative.
