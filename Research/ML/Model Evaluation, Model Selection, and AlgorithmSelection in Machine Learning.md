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
![](https://i.imgur.com/hz7vObi.png)

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
# 1.7 Confidence Intervals via Normal Approximation
a confidence interval around this estimate would not
only be more informative and desirable in certain applications, but our point estimate could be quite sensitive to the particular training/test split (for instance, suffering from high variance). A simple approach for computing confidence intervals of the predictive accuracy or error of a model is via the so-called normal approximation.
Here, we assume that the predictions follow a normal distribution,
to compute the confidence interval on the mean on a single training-test split under the central limit  theorem.
we compute the prediction accuracy on a dataset S (here: test set) of size n as
follows:
![](https://i.imgur.com/9NrmWun.png)
where L(·) is the 0-1 loss function.and n denotes the number of samples in the test
dataset.
let ŷi be the predicted class label and yi be the ground truth class label of the ith test
example, respectively. So, we could now consider each prediction as a Bernoulli trial, and the number
of correct predictions X is following a binomial distribution X ∼ (n, p) with n test examples, k trials, and the probability of success p, where n ∈ N and p ∈ [0, 1] :
![](https://i.imgur.com/7fqleRo.png)
Now, the expected number of successes is computed as µ = np, or more concretely, if the model has
a 50% success rate, we expect 20 out of 40 predictions to be correct. The estimate has a variance of
![](https://i.imgur.com/mASvW9W.png)
std is 3.16.
Since we are interested in the average number of successes, not its absolute value, we compute the variance of the accuracy estimate as
![](https://i.imgur.com/R5QT27E.png)

![](https://i.imgur.com/lD7VIiw.png)
![](https://i.imgur.com/VtCGCmI.png)

---
## 📊 4️⃣ Why accuracy is considered as ppp

Accuracy (ACC) is defined as:

${ACC} = \frac{\text{Number of correct predictions}}{\text{Total number of predictions}}​$
In the binomial model:

- The **expected value** of $X/n$ (the sample proportion) is p.
    
- So, your observed accuracy $\hat{p}=k/n$ is an **estimate** of the true probability ppp that your model predicts correctly on unseen data.
    

That’s why we interpret **accuracy** as an **estimate of the success probability ppp** of the Bernoulli trial.

---
In practice, however, I would rather recommend repeating the training-test split multiple times to compute the confidence interval on the mean estimate (for instance, averaging the individual runs). In any case, <mark> one interesting take-away for now is that having fewer samples in the test set increases the variance (see n in th e denominator above) and thus widens the confidence interval.</mark>

# 2. Bootstrapping and Uncertainties
there are three related, yet different tasks or reasons why we care about model evaluation:
![](https://i.imgur.com/Nbn4fnY.png)

## 2.2 Resampling
To compute the classification error or accuracy on a dataset S, we defined the following equation:
![](https://i.imgur.com/OnWdrEQ.png)
Here, L(·) represents the 0-1 loss.
the classification error is simply the count of incorrect predictions divided by the number of samples in the dataset.
Vice versa, we compute the prediction accuracy as the number of correct
predictions divided by the number of samples.
To use the resampling methods presented in the following sections for
regression models, we swap the accuracy or error computation by, for example, the mean squared error (MSE):
![](https://i.imgur.com/NTP4cpK.png)
performance estimates may suffer from bias and variance, and we are
interested in finding a good trade-off. For instance, the resubstitution evaluation (fitting a model to
a training set and using the same training set for model evaluation) is heavily <mark>optimistically biased</mark>.
Vice versa, withholding a large portion of the dataset as a test set may lead to pessimistically biased estimates. While reducing the size of the test set may decrease this pessimistic bias, the variance of a performance estimates will most likely increase. An intuitive illustration of the relationship between bias and variance is given in Figure 3.

> [!note] 
> - **Optimistic bias** → we _overestimate_ performance (too good to be true)
   > This happens when your **evaluation method gives a model too much credit** — it looks better than it actually is.
>- **Pessimistic bias** → we _underestimate_ performance (too harsh)
>  This happens when your evaluation method is **too strict**, making the model seem worse than it really is.

![](https://i.imgur.com/2O9DVVn.png)
![](https://i.imgur.com/kUGm0bT.png)

the training set is small, the algorithm is more likely picking up noise in the training set.This observation also explains the pessimistic bias of the holdout method: A training algorithm may benefit from more training data, data that was withheld for testing. Thus, after we evaluated a model, we may want to run the learning algorithm once again on the complete dataset before we use it in a real-world application.
<mark> NEED ATTENTION</mark>


Decreasing the size of the test set brings up another problem: It may result in a substantial variance of a model’s performance estimate.
The reason is that it depends on which instances end up in training set, and which particular instances end up in test set.
Keeping in mind that each time we resample a dataset, we alter the statistics of the
distribution of the sample. Most supervised learning algorithms for classification and regression as well as the performance estimates operate under the assumption that a dataset is representative of the population that this dataset sample has been drawn from. As discussed in Section 1.4, stratification helps with keeping the sample proportions intact upon splitting a dataset. However, the change in the underlying sample statistics along the features axes is still a problem that becomes more pronounced if we work with small datasets, which is illustrated in Figure 5.
> [!note] “What if we choose a small size of test data with the stratified sample — is that still have the problem of variance?”
> ✅ **Yes — it still has the problem of variance.**  
Even if you use **stratification**, the variance **doesn’t disappear** — it only becomes _slightly less severe_.
>
stratification **does not guarantee** that:
>
>- The **feature distributions** (the XXX’s) are still representative.
  >  
>- The **relationships** between features and target remain the same.
  >  
>- You have _enough samples_ for a stable estimate.
If your **test set is small**, then:
>
>- Random fluctuations have a **large effect** on the measured performance.
  >  
>- The **variance** of your performance metric (e.g. accuracy, RMSE) increases.
  >  
>- One or two “hard” or “easy” test examples can change the result significantly.

![](https://i.imgur.com/tkkCYQC.png)
One way to obtain a more robust performance estimate that is less variant to how we split the data into training and test sets is to repeat the holdout method k times with different random seeds and compute the average performance over these k repetitions:
![](https://i.imgur.com/E7g5sId.png)
This repeated holdout procedure, sometimes also called Monte Carlo Cross-Validation, provides a better estimate of how well our model may perform on a random test set, compared to the standard holdout validation method. Also, it provides information about the model’s stability – how the model, produced by a learning algorithm, changes with different training set splits. Figure 6 shall illustrate how repeated holdout validation may look like for different training-test split using the Iris dataset to fit to 3-nearest neighbors classifiers.
![](https://i.imgur.com/SRjpLD6.png)
![](https://i.imgur.com/teCRWMe.png)

# 2.4 The Bootstrap Method and Empirical Confidence Intervals
Monte Carlo Cross-Validation may have convinced us that repeated holdout
validation could provide us with a more robust estimate of a model’s performance on random test sets compared to an evaluation based on a single train/test split via holdout validation. In addition, the repeated holdout may give us an idea about the stability of our model. This section explores an alternative approach to model evaluation and for estimating uncertainty using the bootstrap method.


Let us assume that we would like to compute a confidence interval around a performance estimate to judge its certainty – or uncertainty. How can we achieve this if our sample has been drawn from an unknown distribution? Maybe we could use the sample mean as a point estimate of the population mean, but how would we compute the variance or confidence intervals around the mean if its distribution is unknown? Sure, we could collect multiple, independent samples; this is a luxury we often do not have in real world applications, though. Now, the idea behind the bootstrap is to generate "new samples" by sampling from an empirical distribution.

The bootstrap method is a resampling technique for estimating a sampling distribution, and in the context of this article, we are particularly interested in estimating the uncertainty of a performance estimate.
the idea of the bootstrap method is to generate new data from a population by repeated sampling from
the original dataset with replacement – in contrast, the repeated holdout method can be understood as
sampling without replacement. Walking through it step by step, the bootstrap method works like this:
![](https://i.imgur.com/pbI4vRF.png)
As discussed previously, the resubstitution accuracy usually leads to an extremely optimistic bias,
> [!note] What “resubstitution accuracy” means
> **Resubstitution accuracy** = The accuracy you get when you **evaluate the model on the same data it was trained on**. So basically, you _reuse_ the training data for evaluation instead of holding out separate test data.

since a model can be overly sensible to noise in a dataset. Originally, the bootstrap method aims to determine the statistical properties of an estimator when the underlying distribution was unknown and additional samples are not available. So, in order to exploit this method for the evaluation of 
predictive, such as hypotheses for classification and regression, we may prefer a slightly different approach to bootstrapping using the so-called Leave-One-Out Bootstrap (LOOB) technique.
Here, we use out-of-bag samples as test sets for evaluation instead of evaluating the model on the
training data. Out-of-bag samples are the unique sets of instances that are not used for model fitting.
<mark> Robert Tibshirani recommend drawing 50 to 200 bootstrap samples as being sufficient for producing reliable estimates [Efron and Tibshirani, 1994].</mark>
Taking a step back, let us assume that a sample that has been drawn from a normal distribution. Using basic concepts from statistics, we use the sample mean x̄ as a point estimate of the population mean µ:
![](https://i.imgur.com/AyFUyfJ.png)
![](https://i.imgur.com/8SIBJij.png)
Although the approach outlined above seems intuitive, what can we do if our samples do not follow a normal distribution? A more robust, yet computationally straight-forward approach is the percentile method as described by B. Efron [Efron, 1981]. Here, we pick the lower and upper confidence bounds as follows:
![](https://i.imgur.com/2ZX1uGV.png)
In practice, if the data is indeed (roughly) following a normal distribution, the "standard" confidence
interval and percentile method typically agree as illustrated in the Figure 8.
![](https://i.imgur.com/3cPnkws.png)

In 1983, Bradley Efron described the .632 Estimate, a further improvement to address the pessimistic
bias of the bootstrap cross-validation approach described above [Efron, 1983]. The pessimistic bias
in the "classic" bootstrap method can be attributed to the fact that the bootstrap samples only contain
approximately 63.2% of the unique examples from the original dataset. For instance, we can compute the probability that a given example from a dataset of size n is not drawn as a bootstrap sample as follows:
![](https://i.imgur.com/dm0RZr9.png)
![](https://i.imgur.com/ptBzM6N.png)
![](https://i.imgur.com/wQPUmAI.png)
for reasonably large datasets, so that we select approximately 0.632 × n unique examples as bootstrap training sets and reserve 0.382 × n out-of-bag examples for testing in each iteration, which is illustrated in Figure 9.
![](https://i.imgur.com/Yw1u9xT.png)
Now, to address the bias that is due to this the sampling with replacement, Bradley Efron proposed the .632 Estimate mentioned earlier, which is computed via the following equation:
![](https://i.imgur.com/aHUiE56.png)
![](https://i.imgur.com/1UDgq9f.png)
Each bootstrap iteration gives you **two accuracy estimates**:

1. **Training (resubstitution) accuracy** — tends to be _too optimistic_, since the model is evaluated on data it trained on.
    
2. **Holdout (OOB) accuracy** — tends to be _too pessimistic_, since the model was trained on only ~63% of the data.
    

Efron’s .632 estimator **balances** these two extremes by taking a weighted average:

- 63.2% weight to the **OOB accuracy** (since ~63% of data are included per bootstrap),
    
- 36.8% weight to the **training accuracy** (to offset pessimism).
    

Then, averaging across all bbb bootstrap iterations gives the final unbiased estimate.
### 🔹 Why This Works

The bias from bootstrap sampling with replacement arises because:

- Each model is trained on less data than the original dataset,
    
- So OOB evaluation _underestimates_ performance.
    

By blending in some of the training accuracy (which is _too optimistic_), the .632 rule **corrects the overall bias**.

Now, while the .632 Boostrap attempts to address the pessimistic bias of the estimate, an optimistic
bias may occur with models that tend to overfit so that Bradley Efron and Robert Tibshirani proposed
The .632+ Bootstrap Method [Efron and Tibshirani, 1997]. Instead of using a fixed weight ω = 0.632 in
![](https://i.imgur.com/NOGEhvu.png)
![](https://i.imgur.com/VPUEntn.png)
![](https://i.imgur.com/LN9NylW.png)
![](https://i.imgur.com/1zMwXnX.png)



# 3. Cross-validation and Hyperparameter Optimization
In this context, lazy learning (or instance-based learning) means that there is no training or model fitting stage: A k-nearest neighbors model literally stores or memorizes the tThus, each training instance represents a parameter in the k-nearest neighbors model. In short, nonparametric models are models that cannot be described by a fixed number of parameters that are being adjusted to the training set. The structure of parametric models is not decided by the training data rather than being set a priori; nonparamtric models do not assume that the data follows certain probability distributions unlike parametric methods (exceptions of nonparametric methods that make such assumptions are Bayesian nonparametric methods). Hence, we may say that nonparametric methods make fewer assumptions about the data than parametric methods.raining data and uses it only at prediction time.  
In contrast to k-nearest neighbors, a simple example of a parametric method is logistic regression,
a generalized linear model with a fixed number of model parameters: a weight coefficient for each
feature variable in the dataset plus a bias (or intercept) unit.

For fitting a model to the training data, a hyperparameter of a logistic regression
algorithm could be the number of iterations or passes over the training set (epochs) in gradient-based optimization.

The process of finding the best-performing model from a set of models that were
produced by different hyperparameter settings is called model selection.


### The Three-Way Holdout Method for Hyperparameter Tuning

http://bookszlibb74ugqojhzhg2a63w5i2atv5bqarulgczawnbmsb6s6qead.onion/book/2157178/cb6d23/sampling-of-populations-methods-and-applications-4th-ed.html

http://bookszlibb74ugqojhzhg2a63w5i2atv5bqarulgczawnbmsb6s6qead.onion/book/1058159/3c8de0/an-introduction-to-multivariate-statistical-analysis-wiley-series-in-probability-and-statistics.html?dsource=recommend