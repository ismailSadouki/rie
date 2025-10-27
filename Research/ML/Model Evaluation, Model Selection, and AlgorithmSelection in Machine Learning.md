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
Let us have a look at an example using
the Iris dataset 2 , which we randomly divide into 2/3 training data and 1/3 test data
![](https://i.imgur.com/5dompqm.png)
When we randomly divide a labeled dataset into training and test sets, we violate the assumption of statistical independence. 
The Iris datasets consists of 50 Setosa, 50 Versicolor, and 50 Virginica
flowers; the flower species are distributed uniformly:
![](https://i.imgur.com/AAeyiYu.png)
problem of imbalanced split.


stratification is an approach to maintain the original class proportion in resulting subsets.
It shall be noted that random subsampling in non-stratified fashion is usually not a big concern when
working with relatively large and balanced datasets. However, in my opinion, stratified resampling is usually beneficial in machine learning applications. Moreover, stratified sampling is incredibly easy to implement, and Ron Kohavi provides empirical evidence [Kohavi, 1995] that stratification has a positive effect on the variance and bias of the estimate in k-fold cross-validation.

# 1.5 Holdout Validation
- step 1:
it is important that the test set is only
used once to avoid introducing bias when we estimating the generalization performance. Typically, we assign 2/3 to the training set and 1/3 of the data to the test set. Other common training/test splits are 60/40, 70/30, or 80/20 – or even 90/10 if the dataset is relatively large. 
- step 2:
![](https://i.imgur.com/BSx72MS.png)
hyperparameters are the parameters of our learning algorithm, or meta-parameters. And
we have to specify these hyperparameter values manually – the learning algorithm does not learn
these from the training data in contrast to the actual model parameters. Since hyperparameters are not learned during model fitting, we need some sort of "extra procedure" or "external loop" to optimize
these separately – this holdout approach is ill-suited for the task. So, for now, we have to go with some
fixed hyperparameter values – we could use our intuition or the default parameters of an off-the-shelf
algorithm if we are using an existing machine learning library
- Step 3: 
the next question is: How "good" is the performance of the resulting model?
Since the learning algorithm has not "seen" this test set before, it should provide a relatively unbiased estimate of its performance on new, unseen data.
we take the predicted class labels and compare them to the
"ground truth," the correct class labels, to estimate the models generalization accuracy or error.
- step 4:
Since we assume that our
samples are i.i.d., there is no reason to assume the model would perform worse after feeding it all the
available data. As a rule of thumb, the model will have a better generalization performance if the
algorithms uses more informative data – assuming that it has not reached its capacity, yet.

two types of problems that occur when a dataset is split into separate training and test sets. The first problem that occurs is the violation of independence and the changing class proportions upon subsampling. 
Walking through the holdout validation method (Section 1.5) touched upon a second problem we encounter upon subsampling a dataset: Step 4 mentioned capacity of a model, and whether additional data could be useful or not. To follow up on the capacity issue: If a model has not reached its capacity, the performance estimate would be pessimistically biased.
> [!note] “Pessimistic” means **too low**.
> So if an estimate is _pessimistically biased_, it means your evaluation **underestimates** how well the model would actually perform in reality.
> in other words The model probably performs **better** in general than what the holdout/test results show, but our evaluation method makes it look worse.
> # Why would the estimate be pessimistically biased?
> This usually happens when **the training set is too small** — which is common in the **holdout method**. Thus, your holdout test result gives a **pessimistically low estimate** of what the model could achieve if trained on more data.

This assumes that the algorithm could learn a better model if it was given more data – by splitting off a portion of the dataset for testing, we withhold valuable data for estimating the generalization performance (for instance, the test dataset). To address this issue, one might fit the model to the whole dataset after estimating the generalization performancs. 
e model to the whole dataset after
estimating the generalization performance (see Figure 2 step 4). However, using this approach, we cannot estimate its generalization performance of the refit model, since we have now "burned" the test dataset. It is a dilemma that we cannot really avoid in real-world application, but we should be aware that our estimate of the generalization performance may be pessimistically biased if only a portion of the dataset, the training dataset, is used for model fitting (this is especially affects models fit to relatively small datasets).
