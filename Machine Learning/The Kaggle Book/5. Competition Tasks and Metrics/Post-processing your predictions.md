Post-processing tuning implies that your predictions are transformed, by means of a function, into something else in order to present a better evaluation. After building your custom loss or optimizing for your evaluation metric, you can also improve your results by leveraging the characteristics of your evaluation metric using a specific function applied to your predictions.

> [!note] What is Post-Processing Tuning?
> Normally, after you train your ML model, you **get predictions** (e.g., class probabilities, regression outputs). Post-processing tuning means you **don’t just submit these raw predictions** — instead, you transform them with some **function** before submission to better align with the evaluation metric used in the competition.
> 👉 The key idea:  
Even if your model is already good, **you can squeeze extra performance** by 
massaging the predictions to match the evaluation metric’s quirks.
> ### 🔹 Why is this needed?
>
>- Different competitions use different evaluation metrics (AUC, Log Loss, RMSE, F1, etc.).
  >  
>- Your model might optimize one thing (like cross-entropy) but the leaderboard >is ranked on another (like F1).
  >  
>- Post-processing tuning adjusts predictions to **better fit the leaderboard metric**.
> 🔹 Examples
>
 ![](https://i.imgur.com/SpEnlbU.png)
>![](https://i.imgur.com/fPAay1C.png)
>✅ **In short:**  
Post-processing tuning = taking raw model outputs and **massaging them with a clever function** to better exploit the evaluation metric. It’s not about making the model smarter, but about **wrapping predictions smarter**.

---

Above in example 2
#### What does “overconfident” mean?
When a model outputs probabilities, those numbers are supposed to reflect **how sure it is** about its prediction.

- A **well-calibrated** model:
    
    - If it predicts **0.8 (80%)** probability of “dog”, then out of 100 such cases, ~80 should actually be dogs.
        
- An **overconfident** model:
    
    - It outputs **extremely high probabilities** (like 0.99) even when it’s wrong more often than that.
        
    - Example:
        
        - Predicts `0.99` for "cat", but only **70%** of those are truly cats.
            
        - The confidence (99%) is much higher than reality (70%).
So “overconfident” = the model **believes too strongly in its predictions**, even when it makes mistakes.
### 🔹 Why is this a problem?

- **Log Loss / Cross-Entropy** punishes overconfident wrong predictions very heavily.
    
    - Predict `0.99` for wrong class → huge penalty.
        
- It reduces **trustworthiness** (like in medicine or finance, where probabilities matter).
    
- In competitions, overconfident models often perform worse on metrics that care about probability calibration.
#### 🔹 How Post-Processing Fixes It
![](https://i.imgur.com/bGc9DYD.png)
✅ In short:

- **Overconfident model** = predicts probabilities that are too close to 0 or 1, even when it’s not that accurate.
    
- **Post-processing calibration** makes predictions more realistic and often improves leaderboard scores.

---



Let’s take the Quadratic Weighted Kappa, for instance. We mentioned previously that this metric is useful when you have to deal with the prediction of an ordinal value. To recap, the original Kappa coefficient is a chance-adjusted index of agreement between the algorithm and the ground truth. It is a kind of accuracy measurement corrected by the probability that the match between the prediction and the ground truth is due to a fortunate chance.
Here is the original version of the Kappa coefficient, as seen before:
![](https://i.imgur.com/7j9XjsO.png)
The matrix pp contains the penalizations to weight errors differently, which is very useful for ordinal predictions since this matrix can penalize much more when the predictions deviate further from the ground truths. Using the quadratic form, that is, squaring the resulting k, makes the penalization even more severe. However, optimizing for such a metric is really not easy, since it is very difficult to implement it as a cost function. Post-processing can help you.

An example can be found in the PetFinder.my Adoption Prediction competition (https://www.kaggle.com/c/petfinder-adoption-prediction). In this competition, given that the results could have 5 possible ratings (0, 1, 2, 3, or 4), you could deal with them either using a classification or a regression. If you used a regression, a post-processing transformation of the regression output could improve the model’s performance against the Quadratic Weighted Kappa metric, outper-forming the results you could get from a classification directly outputting discrete predictions.


In the case of the PetFinder competition, the post-processing consisted of an optimization process that started by transforming the regression results into integers, first using the boundaries [0.5,1.5, 2.5, 3.5] as thresholds and, by an iterative fine-tuning, finding a better set of boundaries that maximized the performance. The fine-tuning of the boundaries required the computations of an optimizer such as SciPy’s optimize.minimize, which is based on the Nelder-Mead algorithm. The boundaries found by the optimizer were validated by a cross-validation scheme. You can read more details about this post-processing directly from the post made by Abhishek Thakur during the competition: https://www.kaggle.com/c/petfinder-adoption-prediction/discussion/76107.

```
Aside from the PetFinder competition, many other competitions have demonstrated that smart post-processing can lead to improved results and rankings. We’ll point
out a few examples here:
• https://www.kaggle.com/khoongweihao/post-processing-technique-c-f-1st-place-jigsaw
• https://www.kaggle.com/tomooinubushi/postprocessing-based-on-leakage
• https://www.kaggle.com/saitodevel01/indoor-post-processing-by-cost-minimization
```
Unfortunately, post-processing is often very dependent on the metric you are using (understand-ing the metric is imperative for devising any good post-processing) and often also data-specific,for instance, in the case of time series data and leakages. Hence, it is very difficult to generalize any procedure for figuring out the right post-processing for any competition. Nevertheless, always be aware of this possibility and be on the lookout in a competition for any hint that post-processing results is favorable. You can always get hints about post-processing from previous competitions that have been similar, and by forum discussion – eventually, someone will raise the topic.

# Predicted probability and its adjustment
evaluating or optimizing for the log loss may not prove enough. The main problems to be on the lookout for when striving to achieve correct probabilistic predictions with your model are:
•Models that do not return a truly probabilistic estimate
•Unbalanced distribution of classes in your problem
•Different class distribution between your training data and your test data (on both public and private leaderboards)

The first point alone provides reason to check and verify the quality of classification predictions in terms of modeled uncertainty. In fact, even if many algorithms are provided in the Scikit-learn package together with a predict_proba method, this is a very weak assurance that they will return a true probability.

Let’s take, for instance, decision trees, which are the basis of many effective methods to model tabular data. The probability outputted by a classification decision tree (https://scikit-learn. org/stable/modules/generated/sklearn.tree.DecisionTreeClassifier.html) is based on terminal leaves; that is, it depends on the distribution of classes on the leaf that contains the case to be predicted. If the tree is fully grown, it is highly likely that the case is in a small leaf with very few other cases, so the predicted probability will be very high. If you change parameters such as max_depth, max_leaf_nodes, or min_samples_leaf, the resulting probability will drastically change from higher values to lower ones depending on the growth of the tree.

Decision trees are the most common base model for ensembles such as bagging models and random forests, as well as boosted models such as gradient boosting (with its high-performing implementations XGBoost, LightGBM, and CatBoost). But, for the same reasons – probability estimates that are not truly based on solid probabilistic estimations – the problem affects many other commonly used models, such as support-vector machines and k-nearest neighbors. Such aspects were mostly unknown to Kagglers until the Otto Group Product Classification Challenge (https://www.kaggle.com/c/otto-group-product-classification-challenge/overview/), when it was raised by Christophe Bourguignat and others during the competition (see https:// www.kaggle.com/cbourguignat/why-calibration-works), and easily solved at the time using the calibration functions that had recently been added to Scikit-learn.

Aside from the model you will be using, the presence of imbalance between classes in your problem may also result in models that are not at all reliable. Hence, a good approach in the case of unbal-anced classification problems is to rebalance the classes using undersampling or oversampling strategies, or different custom weights for each class to be applied when the loss is computed by the algorithm. All these strategies may render your model more performant; however, they will surely distort the probability estimates and you may have to adjust them in order to obtain an even better model score on the leaderboard.
> [!note] ### 📈 What to Do (Leaderboard Tricks):
 To get the best **score on a competition leaderboard**, you may need to:
 > - **Adjust the predicted probabilities afterward** (called **post-processing**).
 > - Use **calibration techniques** (like Platt scaling, isotonic regression).
 > - Tune thresholds on your validation set for classification decision boundaries.

### ✅ 1. **Adjust the Predicted Probabilities (Post-processing)**

- Models often output **probabilities** (e.g., 0.7 chance this is class 1).
    
- You can tweak those values before submitting:
    
    - Multiply them by a factor.
        
    - Shift them up/down.
        
    - Apply a logit or softmax transform.
        

📌 **Why?** Because sometimes the model is right, but the _raw probability values_ are not optimal for the competition metric (e.g., LogLoss or AUC).

### ✅ 2. **Use Calibration Techniques**

Sometimes, the predicted probabilities are not well-calibrated, meaning they don’t reflect true likelihoods.

Two common calibration methods:

- **Platt Scaling**: Fits a logistic regression model on your model’s output.
    
- **Isotonic Regression**: A non-parametric model that fits a flexible curve to calibrate the probabilities.
    

📌 **Use this when?** Your model outputs probabilities, and your competition metric is sensitive to **probability accuracy**, like LogLoss or Brier score.
 
 
 ### ✅ 3. **Tune Thresholds for Classification**

Most classifiers output probabilities, but at some point you must decide:

> When do I classify this as class 1?

- The usual threshold is **0.5**.
    
- But you can **tune it** on your validation set. For example:
    
    - If class 1 is rare, use 0.2 or 0.3.
        
    - You can even tune different thresholds for different subgroups.
        

📌 **Why?** To **maximize your competition metric** (e.g., F1 score, accuracy, precision-recall).

## 🧠 How to Learn This Skill

These tricks are not taught much in regular ML courses. Here's a good path:

### 📚 Read:

1. **Kaggle Discussion Sections**: Search for “post-processing”, “calibration”, “threshold tuning”.
    
2. **Kaggle Learn** → “Model Performance Metrics” + “Feature Engineering” + “Intro to ML”.
    
3. Research papers / blogs on:
    
    - Platt Scaling
        
    - Isotonic Regression
        
    - Threshold tuning

---

Finally, a third point of concern is related to how the test set is distributed. This kind of information is usually concealed, but there are often ways to estimate it and figure it out (for instance, by trial and error based on the public leaderboard results)

For instance, this happened in the iMaterialist Furniture Challenge (https://www.kaggle.com/c/imaterialist-challenge-furniture-2018/) and the more popular Quora Question Pairs (https://www.kaggle.com/c/quora-question-pairs). Both competitions gave rise to various discussions on how to post-process in order to adjust probabilities to test expectations (see https://swarbrickjones.wordpress.com/2017/03/28/cross-entropy-and-training-test-class-imbalance/ and https://www.kaggle.com/dowakin/probability-calibration-0-005-to-lb for more details on the method used). From a general point of view, assuming that you do not have an idea of the test distribution of classes to be predicted, it is still very beneficial to correctly predict probability based on the priors you get from the training data (and until you get evidence to the contrary, that is the probability distribution that your model should mimic). In fact, it will be much easier to correct your predicted probabilities if your predicted probability distribution matches those in the training set.

The solution, when your predicted probabilities are misaligned with the training distribution of the target, is to use the calibration function provided by Scikit-learn, CalibratedClassifierCV:
```
sklearn.calibration.CalibratedClassifierCV(base_estimator=None, *,
method='sigmoid', cv=None, n_jobs=None, ensemble=True)
```
The purpose of the calibration function is to apply a post-processing function to your predicted probabilities in order to make them adhere more closely to the empirical probabilities seen in the ground truth. Provided that your model is a Scikit-learn model or behaves similarly to one, the function will act as a wrapper for your model and directly pipe its predictions into a post-processing function. You have the choice between using two methods for post-processing. The first is the sigmoid method (also called Plat’s scaling), which is nothing more than a logistic regression. The second is the isotonic regression, which is a non-parametric regression; beware that it tends to overfit if there are few examples.

You also have to choose how to fit this calibrator. Remember that it is a model that is applied to the results of your model, so you have to avoid overfitting by systematically reworking predictions. You could use a cross-validation (more on this in the following chapter on Designing Good Validation) and then produce a number of models that, once averaged, will provide your predictions (ensemble=True). Otherwise, and this is our usual choice, resort to an out-of-fold prediction (more on this in the following chapters) and calibrate on that using all the data available (ensemble=False).


Even if CalibratedClassifierCV can handle most situations, you can also figure out some empirical way to fix probability estimates for the best performance at test time. You can use any transformation function, from a handmade one to a sophisticated one derived by genetic algorithms, for instance. Your only limit is simply that you should cross-validate it and possibly have a good final result from the public leaderboard (but not necessarily, because you should trust your local cross-validation score more, as we are going to discuss in the next chapter). A good example of such a strategy is provided by Silogram (https://www.kaggle.com/psilogram), who, in the Microsoft Malware Classification Challenge, found out a way to tune the unreliable probabilistic outputs of random forests into probabilistic ones simply by raising the output to a power determined by grid search (see https://www.kaggle.com/c/malware-classification/discussion/13509).

