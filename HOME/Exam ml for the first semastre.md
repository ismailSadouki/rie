Here is a **complete write-up** of the full exam text as it appears (cleaned up, structured, and readable), exactly as requested — **not solved**, just written out properly.

**École Nationale Supérieure de Statistique et d’Économie Appliquée (ENSSEA)**  
**First semester Exam – Introduction to Machine Learning**  
**4th year – Statistics and Data Science**  
**Duration: 1h30**  
**Date: 19 January 2026**

**English version**

**Exercise 01 (170 pts / 170 pts)**

**Q1.** Assume a two-class problem where x|y = k follows a Gaussian distribution with mean μₖ and covariance Σₖ.

- Derive the decision boundary for LDA (assuming Σ₁ = Σ₂) and show it is linear in x. (15 pts)
- Compare LDA, QDA, and logistic regression (assuming Σ₁ = Σ₂) in terms of model assumptions, flexibility, and suitability for high-dimensional data. (15 pts)
- How does the number of parameters scale with p for each method? Discuss implications for small-sample high-dimensional settings. (15 pts)

**Q2.** Let ℐ be a learner that, given training data Dₜᵣₐᵢₙ, produces a model f̂.

- What is the definition of the generalization error GE(f̂, L)? (5 pts)
- Define overfitting and underfitting. (10 pts)
- Why is training error a poor estimate of GE? (10 pts)
- Describe the holdout method. How does the train-test split ratio affect the bias and variance of the GE estimate? (10 pts)
- Compare 5-fold cross-validation, 10-fold CV, and leave-one-out CV in terms of bias and variance. (10 pts)
- Explain the bootstrap estimator and how it corrects for bias. Justify why the bootstrap is considered a good estimator. (10 pts)

**Q3.** For a regression tree with L2 loss, show that the optimal constant prediction in node N is the mean of the target values. Derive the risk R(N) for this node. (15 pts)

Consider a classification tree with N being a node containing data {(x⁽ⁱ⁾, y⁽ⁱ⁾)}ᵢ₌₁ᴺ where y⁽ⁱ⁾ ∈ {1, …, g}. Let πₖᴺ be the proportion of class k in node N.

- For this tree, define Gini Impurity and show that minimizing Gini impurity is equivalent to minimizing the Brier score (L2 loss on probabilities). (10 pts)
- A split divides N into N₁ and N₂. Derive the weighted risk after splitting and explain why this weighting is necessary. (10 pts)
- Compare Gini impurity and entropy (log loss) in terms of differentiability and sensitivity to class probability changes. (5 pts)

**Q4.**

- Explain how bagging works, using mathematical formulas. (10 pts)

(The document appears to be cut off here — the last sentence is incomplete in the provided scan/photo.)

That is the full visible content of the exam paper as presented.

If you want me to continue with clean formatting of any missing fragment, rephrase parts, translate into French, or do anything else with this text (without solving the questions), just say so.

Here is the clean, complete transcription of the **second part** of the exam (Exercise 02: AdaBoost + continuation of bagging questions), written out clearly and structured exactly as it appears in the provided text — **no solutions**, just the full exam content formatted for readability.

**Continuation of Exercise 01 – Bagging (continued)**

- Let \(\hat{f}^{[m]}\) be a base learner trained on the \(m\)-th bootstrap sample. Assuming \(\text{Var}(\hat{f}^{[m]}) = \sigma^2\) and  
  \(\text{Corr}(\hat{f}^{[m]},\hat{f}^{[j]}) = \rho\) for \(m \neq j\), derive the variance of the bagged ensemble. (10 pts)

- Explain how \(\rho\) affects the variance reduction. Why is bagging particularly effective for unstable learners like decision trees? (10 pts)

- Define in-bag (IB) and out-of-bag (OOB) observations. Calculate the probability of an observation being OOB in one bootstrap sample for large \(n\). (5 pts)

- Describe how the OOB error estimate is computed and why it provides an unbiased estimate of generalization error without a separate validation set. (5 pts)

**Exercise 02 : AdaBoost (80 pts / 80 pts)**

Let us consider this dataset:

| i | X₁   | X₂   | y   |
|---|------|------|-----|
| 1 | 0.5  | 1.0  | -1  |
| 2 | 1.0  | 0.5  | -1  |
| 3 | 1.5  | 1.0  | -1  |
| 4 | 2.0  | 2.0  | +1  |
| 5 | 2.5  | 1.5  | +1  |

**Q1.** Answer these questions:

- Derive the formula for the classifier weight \(\beta^{[m]}\) in AdaBoost from the minimization of the exponential loss. (10 pts)
- Explain why base learners with lower weighted error receive higher weights. Show mathematically why misclassified observations get their weights increased after each iteration. (5 pts)
- Compare AdaBoost with bagging in terms of:  
  (i) base learner independence,  
  (ii) weighting of predictions,  
  (iii) effect on bias and variance. (5 pts)

**Q2.** For the first iteration

- Initialize the observation weights. Calculate the weighted error for three candidate stumps: (10 pts)

  – Stump 1: \(X_1 < 1.25\)  
  – Stump 2: \(X_1 < 2.0\)  
  – Stump 3: \(X_2 < 1.25\)

- Select the best stump and compute its weight \(\beta^{[1]}\). (5 pts)

- Update all observation weights and normalize them. (5 pts)

**Q3.** For the second iteration

- Using the new weights, compute the weighted errors for the same three stumps and select the best one. (10 pts)

- Calculate \(\beta^{[2]}\) and update weights again. (5 pts)

- Which observations become most influential? (5 pts)

- What pattern do you notice in how the weights evolve? (5 pts)

**Q4.**

- Construct the final additive model after these two iterations. (5 pts)

- Predict the class for a new point \((X_1, X_2) = (1.0, 1.5)\). (5 pts)

- Compute the training error of this 2-classifier ensemble. Would adding more iterations necessarily reduce this training error? Justify. (10 pts)

**Good Luck.**
