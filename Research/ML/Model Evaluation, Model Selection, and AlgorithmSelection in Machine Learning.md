link: https://arxiv.org/pdf/1811.12808

# ABSTRACT
The correct use of model evaluation, model selection, and algorithm selection
techniques is vital in academic machine learning research as well as in many
industrial settings. This article reviews different techniques that can be used for
each of these three subtasks and discusses the main advantages and disadvantages of each technique with references to theoretical and empirical studies. Further, recommendations are given to encourage best yet feasible practices in research and applications of machine learning. Common methods such as the holdout method for model evaluation and selection are covered, which are not recommended when working with small datasets. Different flavors of the bootstrap technique are introduced for estimating the uncertainty of performance estimates, as an alternative to confidence intervals via normal approximation if bootstrapping is computationally feasible. Common cross-validation techniques such as leave-one-out cross-validation and k-fold cross-validation are reviewed, the bias-variance trade-off for choosing k is discussed, and practical tips for the optimal choice of k are given based on empirical evidence. Different statistical tests for algorithm comparisons are presented, and strategies for dealing with multiple comparisons such as omnibus tests and multiple-comparison corrections are discussed. Finally, alternative methods for algorithm selection, such as the combined F -test 5x2 cross-validation and nested cross-validation, are recommended for comparing machine learning algorithms when datasets are small.



![](https://i.imgur.com/VJZARQG.png)

# 1.1 Performance Estimation: Generalization Performance vs. Model Selection
Let us summarize the main points why we evaluate the
predictive performance of a model:
1. We want to estimate the generalization performance, the predictive performance of our model on future (unseen) data.
2. 1. We want to estimate the generalization performance, the predictive performance of our model on future (unseen) data.
3. 1. We want to estimate the generalization performance, the predictive performance of our model on future (unseen) data. 
we shall note that biased performance estimates are perfectly okay in model selection and algorithm selection if the bias affects all models equally. 
f we rank different models or algorithms against each
other in order to select the best-performing one, we only need to know their "relative" performance.
For example, if all performance estimates are pessimistically biased, and we underestimate their
performances by 10%, it will not affect the ranking order. More concretely, if we obtaind three models with prediction accuracy estimates such as
M2: 75% > M1: 70% > M3: 65%,
we would still rank them the same way if we added a 10% pessimistic bias:
M2: 65% > M1: 60% > M3: 55%.
However, note that if we reported the generalization (future prediction) accuracy of the best ranked model (M2) to be 65%, this would obviously be quite inaccurate. Estimating the absolute performance of a model is probably one of the most challenging tasks in machine learning.

#### 1.2 Assumptions and Terminology
- i.i.d. We assume that the training examples are i.i.d which
means that all examples have been drawn from the same probability distribution and are statistically independent from each other. A scenario where training examples are not independent would be working with temporal data or time-series data.
- 0-1 loss and prediction accuracy.In the following article, we will focus on the prediction accuracy,
which is defined as the number of all correct predictions divided by the number of examples in the
dataset. We compute the prediction accuracy as the number of correct predictions divided by the
number of examples n. Or in more formal terms, we define the prediction accuracy ACC as
![](https://i.imgur.com/Wcw60Mq.png)
![](https://i.imgur.com/ztBTbXi.png)
Our objective is to
learn a model h that has a good generalization performance. Such a model maximizes the prediction
accuracy or, vice versa, minimizes the probability, C(h), of making a wrong prediction:
![](https://i.imgur.com/DV2sGTG.png)
Here, D is the generating distribution the dataset has been drawn from, x is the feature vector of a training example with class label y.
Lastly, since this article mostly refers to the prediction accuracy (instead of the error), we define Kronecker’s Delta function:
![](https://i.imgur.com/8spGAc4.png)
![](https://i.imgur.com/QxIYGpC.png)
Bias. Throughout this article, the term bias refers to the statistical bias (in contrast to the bias in a machine learning system). 
![](https://i.imgur.com/D83vDyO.png)
More concretely, we compute the prediction bias as the difference between the expected prediction accuracy of a model and its true prediction accuracy. For example, if we computed the prediction accuracy on the training set, this would be an optimistically biased estimate of the absolute accuracy of a model since it would overestimate its true accuracy.
```
**Prediction bias** = how much your **estimated model accuracy** differs from the model’s **true accuracy** (on unseen data).


It’s _optimistic_ because it **overestimates** how good your model really is.
```
- Variance. The variance is a measure of the variability of a model’s predictions if we repeat the learning process multiple times with small fluctuations in the training set. The more sensitive the model-building process is towards these fluctuations, the higher the variance.
# 1.3 Resubstitution Validation and the Holdout Method
we take a labeled dataset and split it into two parts: A training and a test set. Then, we fit a model to the training data and predict the labels of the test set. The fraction of correct predictions, which can be computed by comparing the predicted labels to the ground truth labels of the test set, constitutes our estimate of the model’s prediction accuracy. Here, it is important to note that we do not want to train and evaluate a model on the same training dataset (this is called resubstitution validation or resubstitution evaluation), since it would typically introduce a very optimistic bias due to overfitting. In other words, we cannot tell whether the model simply memorized the training data, or whether it generalizes well to new, unseen data. (On a side note, we can estimate this so-called optimism bias as the difference between the training and test accuracy.)

> [!note] optimism bias
> When you **train and test on the same data**, the model can:
>
>- **memorize** the patterns (and even noise) in the training data, instead of learning the true relationships.
  >  
>- thus achieve **artificially high accuracy** on the training set.
  >  
>- but when faced with **new data**, performance drops.
  >  
>
That difference is the **optimistic bias** — the amount by which your estimate of model accuracy is “too optimistic.”

Typically, the splitting of a dataset into training and test sets is a simple process of random subsampling. We assume that all data points have been drawn from the same probability distribution (with respect to each class). And we randomly choose 2/3 of these samples for the training set and 1/3 of the samples for the test set. Note that there are two problems with this approach, which we will discuss in the next sections.

# Stratification
The degree to which subsampling without replacement affects the statistic of
a sample is inversely proportional to the size of the sample. 