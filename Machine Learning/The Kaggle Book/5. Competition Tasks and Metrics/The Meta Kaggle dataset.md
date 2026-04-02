The Meta Kaggle dataset (https://www.kaggle.com/kaggle/meta-kaggle) is a collection of rich data about Kaggle’s community and activity, published by Kaggle itself as a public dataset. It contains CSV tables filled with public activity from Competitions, Datasets, Notebooks, and Discussions. All you have to do is to start a Kaggle Notebook (as you saw in Chapters 2 and 3), add to it the Meta Kaggle dataset, and start analyzing the data. The CSV tables are updated daily, so you’ll have to refresh your analysis often, but that’s worth it given the insights you can extract.
![Uploading file...exzin]()
![[Pasted image 20250723110838.png]]•The two top metrics are closely related to each other and to binary probability classification problems. The AUC metric helps to measure if your model’s predicted probabilities tend to predict positive cases with high probabilities, and the Log Loss helps to measure how far your predicted probabilities are from the ground truth (and as you optimize for Log Loss, you also optimize for the AUC metric).
- In 3rd position, we find MAP@{K}, which is a common metric in recommender systems and search engines. In Kaggle competitions, this metric has been used mostly for information retrieval evaluations, such as in the Humpback Whale Identification competition (https:// www.kaggle.com/c/humpback-whale-identification), where you have to precisely iden- tify a whale and you have five possible guesses. Another example of MAP@{K} usage is in the Quick, Draw! Doodle Recognition Challenge (https://www.kaggle.com/c/quickdraw- doodle-recognition/), where your goal is to guess the content of a drawn sketch and you are allowed three attempts. In essence, when MAP@{K} is the evaluation metric, you can score not just if you can guess correctly, but also if your correct guess is among a certain number (the “K” in the name of the function) of other incorrect predictions.
- Only in 6th position can we find a regression metric, the RMSLE, or Root Mean Squared Logarithmic Error, and in 7th place the Quadratic Weighted Kappa, a metric particularly useful for estimating model performance on problems that involve guessing a progressive integer number (an ordinal scale problem).

# Handling never-before-seen metrics
![](https://i.imgur.com/OA7Zayq.png)


# Metrics for regression (standard and ordinal)

## Mean squared error (MSE) and R squared
![](https://i.imgur.com/oEhx12f.png)

MSE is a great instrument for comparing regression models applied to the same problem. The bad news is that the MSE is seldom used in Kaggle competitions, since RMSE is preferred. In fact, by taking the root of MSE, its value will resemble the original scale of your target and it will be easier at a glance to figure out if your model is doing a good job or not. In addition, if you are con- sidering the same regression model across different data problems (for instance, across various datasets or data competitions), R squared is better because it is perfectly correlated with MSE

## Root mean squared error (RMSE)
you should always pay attention to outliers; they can affect your model performance a lot, no matter whether you are evaluating based on MSE or RMSE.

Consequently, depending on the problem, you can get a better fit with an algorithm using MSE as an objective function by first applying the square root to your target (if possible, because it requires positive values), then squaring the results. Functions such as the TransformedTargetRegressor in Scikit-learn help you to appropriately transform your regression target in order to get better-fit- ting results with respect to your evaluation metric.

Recent competitions where RMSE has been used include:
• Avito Demand Prediction Challenge: https://www.kaggle.com/c/avito-demand-prediction
• Google Analytics Customer Revenue Prediction: https://www.kaggle.com/c/ga-customer-revenue-prediction
• Elo Merchant Category Recommendation https://www.kaggle.com/c/elo-merchant-category-recommendation


## Root mean squared log error (RMSLE)
what you care the most about when using RMSLE is the scale of your predictions with respect to the scale of the ground truth. As with RMSE, machine learning algorithms for regression can better optimize for RMSLE if you apply a logarithmic transformation to the target before fitting it (and then reverse the effect using the exponential function).
![](https://i.imgur.com/p3sEdA0.png)


## Mean absolute error (MAE)
you can easily work with it since many algorithms can directly use it as an objective function;
otherwise, you can optimize for it indirectly by just training on the square root of your target and then squaring the predictions.
In terms of downside, using MAE as an objective function results in much slower convergence, since you are actually optimizing for predicting the median of the target (also called the L1 norm), instead of the mean (also called the L2 norm), as occurs by MSE minimization. This results in more complex computations for the optimizer, so the training time can even grow exponentially based on your number of training cases (see, for instance, this Stack Overflow question: https://stackoverflow.com/questions/57243267/why-is-training-a-random-forest-regressor-with-mae-criterion-so-slow-compared-to).

Notable recent competitions that used MAE as an evaluation metric are:
• LANL Earthquake Prediction: https://www.kaggle.com/c/LANL-Earthquake-Prediction
• How Much Did It Rain? II: https://www.kaggle.com/c/how-much-did-it-rain-ii



Having mentioned the ASHRAE competition earlier, we should also mention that regression evaluation measures are quite relevant to forecasting competitions. For instance, the M5 forecasting competition was held recently (https://mofc.unic.ac.cy/m5-competition/) and data from all the other M competitions is available too. If you are interested in forecasting competitions, of which there are a few on Kaggle, please see https://robjhyndman.com/hyndsight/forecasting-competitions/ for an overview about M competitions and how valuable Kaggle is for obtaining better practical and theoretical results from such competitions.


Essentially, forecasting competitions do not require a very different evaluation to regression competitions. When dealing with forecasting tasks, it is true that you can get some unusual evaluation metrics such as the Weighted Root Mean Squared Scaled Error (https://www.kaggle.com/c/m5-forecasting-accuracy/overview/evaluation) or the symmetric mean absolute percentage error, better known as sMAPE (https://www.kaggle.com/c/demand-forecasting-kernels-only/overview/evaluation). However, in the end they are just variations of the usual RMSE or MAE that you can handle using the right target transformations.

# Metrics for classification (label prediction and probability)

## Accuracy
it can be calculated as the ratio between the number of correct numbers divided by the number of answers:
![](https://i.imgur.com/kKZz9ls.png)

This metric has been used, for instance, in Cassava Leaf Disease Classification
(https://www.kaggle.com/c/cassava-leaf-disease-classification) and Text Normalization Challenge - English Language (https://www.kaggle.com/c/text-normalization-challenge-english-language), where you scored a correct prediction only if your predicted text matched the actual string.


# Log loss and ROC-AUC
also known as cross-entropy in deep learning models.
log loss is the difference between the predicted probability and the ground truth probability:
![](https://i.imgur.com/oKX4wNm.png)
if a competition uses the log loss, it is implied that the objective is to estimate as correctly as possible the probability of an example being of a positive class.
We suggest you have a look, for instance, at the recent Deepfake Detection Challenge (https://www.kaggle.com/c/deepfake-detection-challenge) or at the older Quora Question Pairs (https://www.kaggle.com/c/quora-question-pairs).


**The ROC curve** evaluate the performance of a binary classifier and to compare multiple classifiers.The ROC curve consists of the true positive rate (the recall) plotted against the false positive rate
(the ratio of negative instances that are incorrectly classified as positive ones). It is equivalent to one minus the true negative rate (the ratio of negative examples that are correctly classified). 
If you are comparing different classifiers, and you are using the area under the curve (AUC), the classifier with the higher area is the more performant one.

If the classes are balanced, or not too imbalanced, increases in the AUC are proportional to the effectiveness of the trained model and they can be intuitively thought of as the ability of the model to output higher probabilities for true positives. We also think of it as the ability to order the examples more properly from positive to negative. However, when the positive class is rare, the AUC starts high and its increments may mean very little in terms of predicting the rare class better. As we mentioned before, in such a case, average precision is a more helpful metric.
AUC has recently been used for quite a lot of different competitions. We suggest you have a look at these three:
- IEEE-CIS Fraud Detection: https://www.kaggle.com/c/ieee-fraud-detection
- Riiid Answer Correctness Prediction: https://www.kaggle.com/c/riiid-test-answer-prediction
- Jigsaw Multilingual Toxic Comment Classification: https://www.kaggle.com/c/jigsaw-multilingual-toxic-comment-classification/

You can read a detailed treatise in the following paper: Su, W., Yuan, Y., and Zhu, M. A relationship between the average precision and the area under the ROC curve. Proceedings of the 2015 International Conference on The Theory of Information Retrieval. 2015.

## Matthews correlation coefficient (MCC)
which made its appearance in VSB Power Line Fault Detection (https://www.kaggle.com/c/vsb-power-line-fault-detection) and Bosch Production Line Performance (https://www.kaggle.com/c/bosch-production-line-performance).
![](https://i.imgur.com/jOX3CZ0.png)

Behaving as a correlation coefficient, in other words, ranging from +1 (perfect prediction) to -1 (inverse prediction), this metric can be considered a measure of the quality of the classification even when the classes are quite imbalanced.

In spite of its complexity, the formula can be reformulated and simplified, as demonstrated by Neuron Engineer (https://www.kaggle.com/ratthachat) in his Notebook: www.kaggle.com/ratthachat/demythifying-matthew-correlation-coefficients-mcc.
![](https://i.imgur.com/rppB4D6.png)
The reformulation helps to clarify, in a more intelligible form than the original, that you can get higher performance from improving both positive and negative class precision, but that’s not enough: you also have to have positive and negative predictions in proportion to the ground truth, or your submission will be greatly penalized.

## Metrics for multi-class classification
When moving to multi-class classification, you simply use the binary classification metrics that we have just seen, applied to each class, and then you summarize them using some of the averaging strategies that are commonly used for multi-class situations.
For instance, if you want to evaluate your solution based on the F1 score, you have three possible averaging choices:
<mark>raje3</mark>
Common multi-class metrics that you may encounter in Kaggle competitions are:
•Multiclass accuracy (weighted): Bengali.AI Handwritten Grapheme Classification (https://www.kaggle.com/c/bengaliai-cv19)
•Multiclass log loss (MeanColumnwiseLogLoss): Mechanisms of Action (MoA) Prediction (https://www.kaggle.com/c/lish-moa/)
•Macro-F1 and Micro-F1 (NQMicroF1): University of Liverpool - Ion Switching (https://www.kaggle.com/c/liverpool-ion-switching), Human Protein Atlas Image Classification
(https://www.kaggle.com/c/human-protein-atlas-image-classification/), Ten-
sorFlow 2.0 Question Answering (https://www.kaggle.com/c/tensorflow2-question-answering)
- Mean-F1: Shopee - Price Match Guarantee (https://www.kaggle.com/c/shopee-product-matching/). Here, the F1 score is calculated for every predicted row, then averaged, whereas the Macro-F1 score is defined as the mean of class-wise/label-wise F1 scores.

# Metrics for object detection problems
## Intersection over union (IoU)
The intersection over union (IoU) is also known as the Jaccard index. When used in segmentation problems, using IoU implies that you have two images to compare: one is your prediction and the other is the mask revealing the ground truth, which is usually a binary matrix where the value 1 stands for the ground truth and 0 otherwise. In the case of multiple objects, you have multiple masks, each one labeled with the class of the object. 

<mark>
- ...
- Metrics for multi-label classification and recommendation problems
- MAP@{K}
</mark>


