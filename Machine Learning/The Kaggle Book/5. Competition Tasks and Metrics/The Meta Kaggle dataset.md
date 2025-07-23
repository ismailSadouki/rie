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
