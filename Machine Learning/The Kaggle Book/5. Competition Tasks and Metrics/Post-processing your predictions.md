Post-processing tuning implies that your predictions are transformed, by means of a function, into something else in order to present a better evaluation. After building your custom loss or optimizing for your evaluation metric, you can also improve your results by leveraging the characteristics of your evaluation metric using a specific function applied to your predictions.
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
