







### **Decide which features to keep**

  

You have multiple features per categorical column. You can:

  

Keep only the best-performing columns per category (the ones listed in the PrettyTable)

  

Or keep all engineered features for more complex models (tree-based models like RandomForest, XGBoost, LightGBM handle correlated features well).

  

<mark>Tip: If using linear models (Logistic Regression, Linear SVM), removing highly correlated features helps.</mark>

  

again! which method/features to keep?













Good question — these are _advanced feature engineering ideas_, and they’re often used in Kaggle “top solutions” stacks. I’ll explain them in a simple, practical way (not theory-heavy).

---

# 1. Clustering embeddings (what it means)

Instead of using raw features directly, you:

### Idea:

👉 Group rows or categories into “hidden clusters” that behave similarly.

### Example:

If you have a feature like:

```
city = [Paris, London, Tokyo, Paris, Berlin]
```

You transform it into:

```
cluster_id = [0, 1, 2, 0, 1]
```

### Advanced version:

You don’t cluster raw values — you cluster **embeddings or encoded features**:

- target encoding
    
- frequency encoding
    
- multiple categorical encodings
    

Then apply:

```python
KMeans → cluster id → one-hot or target encode
```

### Why it works:

It finds hidden structure like:

- similar behavior groups
    
- latent category similarity
    

---

# 2. Entropy-based encoding pruning

This is about **removing useless encodings intelligently**.

### Idea:

For each encoded feature:

- compute how “informative” it is
    

You use entropy:

[  
H(X) = - \sum p(x) \log p(x)  
]

### Intuition:

- High entropy → feature is random / useless
    
- Low entropy → structured → useful
    

### Kaggle usage:

After encoding:

- drop features with high entropy (noise)
    
- keep informative encodings
    

### Why it matters:

Instead of blindly keeping:

```
nom_0_te, nom_1_te, nom_2_te ...
```

You filter only meaningful ones.

---

# 3. PCA latent features (VERY IMPORTANT in Kaggle)

### Idea:

You compress many engineered features into fewer “latent signals”.

You do:

```python
from sklearn.decomposition import PCA
```

Then:

```python
PCA(all_encoded_features) → 5–50 new features
```

### What it gives you:

Instead of:

```
100 noisy features
```

You get:

```
20 dense informative components
```

### Why it works:

- removes redundancy
    
- combines correlated encodings
    
- improves generalization (less overfitting)
    

---

# 🧠 How Kaggle winners actually combine them

Typical pipeline:

### Step 1 — Encoding

- target encoding
    
- frequency encoding
    
- hashing
    
- cyclic encoding
    

### Step 2 — Interaction / clustering

- KMeans on encodings → cluster features
    

### Step 3 — Feature pruning

- remove high entropy features
    
- remove low importance features
    

### Step 4 — Compression

- PCA / SVD → latent features
    

### Step 5 — Model

- LightGBM / XGBoost / CatBoost
    

---

# 🚨 Key insight (very important)

These methods are NOT separate tricks.

They are a **pipeline of compression + structure extraction**:

```
Raw categorical → Encodings → Clusters → PCA → Model
```

---



# If you want next step

I can build you a **Kaggle-grade “Feature Engine V2” pipeline** that includes:

- target encoding (OOF)
    
- hashing (collision-safe)
    
- cyclic encoding
    
- clustering embeddings
    
- entropy pruning
    
- PCA compression
    
- feature importance filter
    

Just tell me:

> “build full pipeline”

and I’ll assemble it cleanly for your notebook.


---
---


⚖️ 4. Best strategy per model
🟦 SVM (TF-IDF)

Use:

Back-translation 🥇
Character noise 🥈
Light synonym replacement 🥉

👉 This gives biggest gains

🟩 LSTM + GloVe

Use:

Back-translation 🥇
Emoji/slang normalization 🥈
Light synonym replacement 🥉

👉 Focus on natural text


🥇 1. Pseudo-labeling (the #1 “augmentation” today)

Even though it’s not classical augmentation, it dominates recent top solutions.

How it works:

Train model on labeled data
Predict on test / unlabeled data
Keep high-confidence predictions
Add them back to training set

👉 Why it works:

Expands dataset with real distribution data
Much better than synthetic text tricks

📌 Used heavily with:

DeBERTa
RoBERTa


🥈 2. Back-translation (still the strongest “classic” augmentation)

Idea:
English → French → English (or multiple languages)





👉 Why it still works:

Produces natural paraphrases
Preserves label better than synonym replacement

📊 Evidence from research:

Improves performance especially in low-data regimes
Strong in tasks like classification + paraphrase detection

📌 Kaggle use:

Common in small datasets (toxic comments, fake news, etc.)






----
---

- [ ] 🔹 C. Noise injection :  Add noise to features during training → more robust model




### 2. Why it saves you hours in a competition

If you try to tune your hyperparameters (`max_depth`, `subsample`, `colsample_bytree`) while using a low learning rate (0.01), every single Optuna trial will take **10x longer** because it has to build 10x the number of trees.

**The Strategy:**

1. **Search Phase:** Set your learning rate to **0.1**. Your Optuna trials will finish in seconds or minutes. Because the "optimal" tree structure (depth, leaves) found at 0.1 is usually very similar to what is needed at 0.01, you are basically finding the "shape" of the model at high speed.
    
2. **Final Polish:** Once you have your best `max_depth`, `min_child_weight`, etc., you "slow down" the model to squeeze out that extra **0.001–0.005 AUC** that wins competitions.
### Summary Checklist for your 12h Competition:

1. **Hours 0-8:** Run Optuna with `learning_rate=0.1` and `MedianPruner`.
    
2. **Hour 9:** Pick the best `max_depth`, `subsample`, and `colsample`.
    
3. **Hour 10:** Run one final CV with those params, but set `learning_rate=0.01` and `n_estimators=10000` with **Early Stopping**.

| **Model Type**              | **Can use "Learning Rate Trick"?** | **Best Time-Saving Strategy**                            |
| --------------------------- | ---------------------------------- | -------------------------------------------------------- |
| **XGBoost / LightGBM**      | **YES**                            | Tune at $LR=0.1$, Final train at $LR=0.01$.              |
| **Bagging / Random Forest** | **NO**                             | Tune with fewer `n_estimators` and smaller data samples. |
| **Neural Networks**         | **YES**                            | Tune with fewer `epochs` or higher `learning_rate`.      |




links:

https://www.kaggle.com/code/tanmayunhale/genetic-algorithm-for-feature-selection/notebook
https://www.kaggle.com/datasets/uciml/breast-cancer-wisconsin-data/code?datasetId=180&sortBy=voteCount
https://www.kaggle.com/code/pouryaayria/a-complete-ml-pipeline-tutorial-acu-86#6.2.-Error-Corrolation

https://www.kaggle.com/competitions/g-research-crypto-forecasting/code?competitionId=30894&sortBy=voteCount&excludeNonAccessedDatasources=true
https://www.kaggle.com/datasets/yelp-dataset/yelp-dataset/code?datasetId=10100&sortBy=voteCount
https://www.kaggle.com/code/mpwolke/model-s-battles-on-llm-finetuning
https://www.kaggle.com/competitions/llm-classification-finetuning/code?competitionId=86518&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/prudential-life-insurance-assessment/code?competitionId=4699&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/amex-default-prediction/code?competitionId=35332&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/jane-street-market-prediction/data

https://www.kaggle.com/competitions/recruit-restaurant-visitor-forecasting/code?competitionId=7277&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/tabular-playground-series-aug-2022/code?competitionId=33108&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/tabular-playground-series-may-2022/code?competitionId=33105&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/code/alexryzhkov/aug21-lightautoml-starter

https://www.kaggle.com/competitions/tabular-playground-series-aug-2021/code?competitionId=28008&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/tabular-playground-series-mar-2021/data

https://www.kaggle.com/competitions?hostSegmentIdFilter=8&page=3

https://www.kaggle.com/competitions/two-sigma-financial-modeling/code?competitionId=5874&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/code/lonnieqin/ubiquant-market-prediction-with-dnn

https://www.kaggle.com/competitions/ubiquant-market-prediction/code?competitionId=32053&sortBy=voteCount&excludeNonAccessedDatasources=true


# top comp

https://www.kaggle.com/competitions/ai-village-ctf/code?competitionId=37381&sortBy=voteCount&excludeNonAccessedDatasources=true
https://www.kaggle.com/competitions/microsoft-malware-prediction/code?competitionId=10683&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/search?q=cybersecurity+in%3Adatasets

https://www.kaggle.com/competitions/linking-writing-processes-to-writing-quality/data

https://www.kaggle.com/code/rafjaa/dealing-with-very-small-datasets

https://www.kaggle.com/competitions/dont-overfit-ii/code?competitionId=12896&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/cat-in-the-dat-ii/code?competitionId=17000&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/cat-in-the-dat/code?competitionId=14999&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/LANL-Earthquake-Prediction/code?competitionId=11000&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/neurips-open-polymer-prediction-2025/code?competitionId=74608&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/linking-writing-processes-to-writing-quality/code?competitionId=59291&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/icecube-neutrinos-in-deep-ice/code

https://www.kaggle.com/competitions/ashrae-energy-prediction/code?competitionId=9994&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/quora-insincere-questions-classification/code?competitionId=10737&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/microsoft-malware-prediction

https://www.kaggle.com/competitions/expedia-hotel-recommendations/code?competitionId=5056&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/zillow-prize-1/code?competitionId=6649&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/code/pavansanagapati/knowledge-graph-nlp-tutorial-bert-spacy-nltk

https://www.kaggle.com/competitions/ashrae-energy-prediction/code?competitionId=9994&sortBy=voteCount&excludeNonAccessedDatasources=true


https://www.kaggle.com/competitions/energy-anomaly-detection/code?competitionId=35297&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/datasets/zynicide/wine-reviews/code?datasetId=1442&sortBy=voteCount


https://www.kaggle.com/datasets/boltzmannbrain/nab/code?datasetId=110&sortBy=voteCount

# other comp

https://www.kaggle.com/competitions/competitive-data-science-predict-future-sales/code?competitionId=8587&sortBy=voteCount&excludeNonAccessedDatasources=true


https://www.kaggle.com/competitions/playground-series-s5e3/data

https://www.kaggle.com/competitions/playground-series-s5e7/code?competitionId=91718&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/elo-merchant-category-recommendation/code?competitionId=10445&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/allstate-claims-severity/code?competitionId=5325&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/otto-group-product-classification-challenge/code?competitionId=4280&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/avito-demand-prediction/code?competitionId=8586&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/novozymes-enzyme-stability-prediction/code?competitionId=37190&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/icr-identify-age-related-conditions/code?competitionId=52784&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s3e10/code?competitionId=47789&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s4e3/code?competitionId=68699&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s3e26/code?competitionId=60893&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s3e21/code?competitionId=59109&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s3e20/code?competitionId=57095&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/code/iqbalsyahakbar/ps3e19-time-series-for-beginners

https://www.kaggle.com/code/paddykb/2nd-place-solution-ps-s3e19-gammy-sales

https://www.kaggle.com/competitions/playground-series-s3e19/code?competitionId=57094&sortBy=voteCount&excludeNonAccessedDatasources=true


https://www.kaggle.com/competitions/playground-series-s3e18/code?competitionId=53377&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s3e12/code?competitionId=49200&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/tabular-playground-series-aug-2021/code?competitionId=28008&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/30-days-of-ml/code?competitionId=27423&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/contradictory-my-dear-watson/code?competitionId=21733&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/contradictory-my-dear-watson/code?competitionId=21733&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/lish-moa/code?competitionId=19988&sortBy=voteCount&excludeNonAccessedDatasources=true

https://lightinshadow1110.github.io/kaggle-solutions/?utm_source=chatgpt.com

https://lightinshadow1110.github.io/kaggle-solutions/?utm_source=chatgpt.com

https://lightinshadow1110.github.io/kaggle-solutions/?utm_source=chatgpt.com

https://www.kaggle.com/competitions/playground-series-s5e10/code?competitionId=91721&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/neurips-open-polymer-prediction-2025/code?competitionId=74608&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/make-data-count-finding-data-references/code?competitionId=82370&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s5e2/code?competitionId=90274&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s5e5/code?competitionId=91716&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s5e7/code?competitionId=91718&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/um-game-playing-strength-of-mcts-variants/code?competitionId=70089&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s4e11/code?competitionId=84895&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/home-credit-credit-risk-model-stability/code?competitionId=50160&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s4e3/code?competitionId=68699&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s3e26/code?competitionId=60893&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/code/arunklenin/ps3e26-cirrhosis-survial-prediction-multiclass#7.1-Class-Weights

https://www.kaggle.com/competitions/icr-identify-age-related-conditions/code?competitionId=52784&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/lish-moa/code?competitionId=19988&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/santander-customer-satisfaction/code?competitionId=4986&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/amex-default-prediction/data

https://www.kaggle.com/competitions/playground-series-s3e3/code?competitionId=44631&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s3e13/code?competitionId=49201&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s3e2/code?competitionId=44630&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s3e10/code?competitionId=47789&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s3e15/code?competitionId=51982&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s3e18/code?competitionId=53377&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/jigsaw-toxic-comment-classification-challenge/code?competitionId=8076&sortBy=voteCount&excludeNonAccessedDatasources=true


# nn

https://www.kaggle.com/code/vitorgamalemos/finding-cellphone-on-image-using-cnn/notebook?scriptVersionId=50167458

https://www.kaggle.com/code/jhoward/linear-model-and-neural-net-from-scratch

https://www.kaggle.com/competitions/dogs-vs-cats/code?competitionId=3362&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/plant-seedlings-classification/code?competitionId=7880&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/code/shivamb/how-autoencoders-work-intro-and-usecases

https://www.kaggle.com/code/arunkumarramanan/awesome-cv-with-fashion-mnist-classification

https://www.kaggle.com/datasets/zalando-research/fashionmnist/code?datasetId=2243&sortBy=voteCount

https://www.kaggle.com/datasets/tawsifurrahman/covid19-radiography-database/code?datasetId=576013&sortBy=voteCount

https://www.kaggle.com/datasets/lakshmi25npathi/imdb-dataset-of-50k-movie-reviews/code?datasetId=134715&sortBy=voteCount

https://www.kaggle.com/datasets/datamunge/sign-language-mnist/code?datasetId=3258&sortBy=voteCount

https://www.kaggle.com/competitions/titanic/data

https://www.kaggle.com/competitions/titanic/code?competitionId=3136&sortBy=voteCount&searchQuery=nn&excludeNonAccessedDatasources=true

https://www.kaggle.com/datasets/uciml/iris/data


# clus
https://www.kaggle.com/code/azminetoushikwasi/different-clustering-techniques-and-algorithms

https://www.kaggle.com/code/cabaxiom/tps-jul-22-bgmm-semi-supervised#Inference

https://www.kaggle.com/code/samuelcortinhas/tps-july-22-unsupervised-clustering

https://www.kaggle.com/code/ashaykatrojwar/eda-pca-bayesian-gaussian-mixture
https://www.kaggle.com/code/thedevastator/how-to-ensemble-clustering-algorithms-updated

https://www.kaggle.com/code/thedevastator/bruteforce-clustering

https://www.kaggle.com/code/javigallego/outliers-eda-clustering-tutorial

https://www.kaggle.com/code/ricopue/tps-jul22-clusters-and-lgb

https://www.kaggle.com/code/mehrankazeminia/3-3-tps22jul-clustering-ensembling

https://www.kaggle.com/code/ambrosm/tpsjul22-gaussian-mixture-cluster-analysis

https://www.kaggle.com/competitions/tabular-playground-series-jul-2022/code?competitionId=33107&sortBy=voteCount&excludeNonAccessedDatasources=true


imba
https://www.kaggle.com/code/azminetoushikwasi/different-clustering-techniques-and-algorithms



https://www.kaggle.com/code/ravi20076/playgrounds4e02-eda-baseline

https://www.kaggle.com/competitions/tabular-playground-series-jul-2022/code?competitionId=33107&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/code/arunklenin/advanced-feature-engg-techniques-beyond-basics

https://www.kaggle.com/competitions/tabular-playground-series-aug-2021/code?competitionId=28008&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s5e7/code?competitionId=91718&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/code/liamarguedas/machine-learning-with-imbalanced-data?utm_source=chatgpt.com

http://neuralnetworksanddeeplearning.com/chap4.html



survey

https://www.kaggle.com/datasets/moemenmamdouh/kaggle-survey-2022-responses

https://www.kaggle.com/datasets/fundal/secondary-education-students-results-2nd-hisgeo-dz

https://www.kaggle.com/datasets/bhadramohit/social-media-usage-datasetapplications/code

https://www.kaggle.com/datasets/abdeldjalilbouz/algerian-car-market-comments-sentiment-dataset

https://www.kaggle.com/datasets/bhadramohit/social-media-usage-datasetapplications/code

https://www.kaggle.com/datasets/guriya79/student-mental-health-and-academic-pressure/code

https://www.kaggle.com/datasets/hasaanrana/online-shopping-dataset#:~:text=About%20Dataset,and%20product%20categories%20frequently%20purchased.

https://www.kaggle.com/datasets/fundal/secondary-education-students-results-2nd-hisgeo-dz

https://www.kaggle.com/code/sureedkumarmondal/kaggle-survey-2022-responses

https://www.kaggle.com/code/moemenmamdouh/junior-ml-students

https://www.kaggle.com/datasets/moemenmamdouh/kaggle-survey-2022-responses/code


https://www.kaggle.com/code/pavansanagapati/ensemble-learning-techniques-tutorial

https://www.kaggle.com/code/mgmarques/houses-prices-complete-solution#Mapping-Ordinal-Features

https://www.kaggle.com/code/nareshbhat/starter-guide-to-build-nlp-ml-model-in-pycaret/notebook

https://www.kaggle.com/code/prashant111/comprehensive-guide-on-feature-selection

https://www.kaggle.com/code/pavansanagapati/14-simple-tips-to-save-ram-memory-for-1-gb-dataset

https://www.kaggle.com/code/jimthompson/ensemble-model-stacked-model-example



# great notebooks


https://www.kaggle.com/code/arunklenin/ps3e15-iterative-catboost-imputer-ensemble/notebook?scriptVersionId=130271409

https://pycaret.readthedocs.io/en/latest/api/classification.html

https://www.kaggle.com/code/alexandrelemercier/all-best-tabular-classifiers-comparative-study/notebook#Optimized-CatBoost

https://www.kaggle.com/code/parulpandey/a-guide-to-handling-missing-values-in-python/notebook

https://www.kaggle.com/code/parulpandey/a-guide-to-handling-missing-values-in-python

https://www.kaggle.com/code/rtatman/data-cleaning-challenge-handling-missing-values

https://www.kaggle.com/code/alexisbcook/missing-values

https://www.kaggle.com/code/azminetoushikwasi/xgboost-wrangling-with-hyperparameters-guide

https://www.kaggle.com/code/nkitgupta/text-representations


https://www.kaggle.com/code/gazu468/all-about-bert-you-need-to-know

https://www.kaggle.com/code/vbookshelf/basics-of-bert-and-xlm-roberta-pytorch


# great comp
https://www.kaggle.com/competitions/optiver-realized-volatility-prediction/code?competitionId=27233&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/datasets/crowdflower/twitter-airline-sentiment/code?datasetId=17&sortBy=voteCount


https://www.kaggle.com/competitions/quora-insincere-questions-classification/code?competitionId=10737&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/quora-insincere-questions-classification/code?competitionId=10737&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/quora-insincere-questions-classification/code?competitionId=10737&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/code/arunmohan003/sentiment-analysis-using-lstm-pytorch

https://www.kaggle.com/datasets/lakshmi25npathi/imdb-dataset-of-50k-movie-reviews/code?datasetId=134715&sortBy=voteCount

https://www.kaggle.com/competitions/cat-in-the-dat/data?select=test.csv

https://www.kaggle.com/competitions/cat-in-the-dat-ii/code?competitionId=17000&sortBy=voteCount&excludeNonAccessedDatasources=true

# Top notebooks
https://www.kaggle.com/code/parulpandey/a-guide-to-handling-missing-values-in-python/notebook

https://www.kaggle.com/code/subinium/11-categorical-encoders-and-benchmark

https://www.kaggle.com/code/nkitgupta/text-representations


https://www.kaggle.com/code/pavansanagapati/knowledge-graph-nlp-tutorial-bert-spacy-nltk


https://www.kaggle.com/code/sudalairajkumar/simple-exploration-notebook-qiqc

https://www.kaggle.com/competitions/quora-insincere-questions-classification/data?select=embeddings.zip

https://www.kaggle.com/code/hung96ad/pytorch-starter

https://www.kaggle.com/code/sudalairajkumar/a-look-at-different-embeddings

https://www.kaggle.com/code/sudalairajkumar/winning-solutions-of-kaggle-competitions

https://www.kaggle.com/code/christofhenkel/how-to-preprocessing-when-using-embeddings

https://www.kaggle.com/competitions/instant-gratification/code?competitionId=14239&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/code/nroman/i-m-overfitting-and-i-know-it

https://www.kaggle.com/datasets/imakash3011/customer-personality-analysis/code?datasetId=1546318&sortBy=voteCount

https://www.kaggle.com/code/gaganmaahi224/9-clustering-techniques-for-customer-segmentation/notebook
https://www.kaggle.com/code/stoicstatic/twitter-sentiment-analysis-using-word2vec-bilstm/notebook

https://www.kaggle.com/code/nroman/i-m-overfitting-and-i-know-it


https://www.kaggle.com/code/cdeotte/pseudo-labeling-qda-0-969?scriptVersionId=73576226

https://www.kaggle.com/code/theoviel/improve-your-score-with-some-text-preprocessing

https://www.kaggle.com/code/adaubas/2nd-place-solution-categorical-fe-callenge

https://www.kaggle.com/code/tarunpaparaju/jigsaw-multilingual-toxicity-eda-models
https://www.kaggle.com/competitions/contradictory-my-dear-watson/code?competitionId=21733&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/quora-question-pairs/code?competitionId=6277&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/code/jeffd23/visualizing-word-vectors-with-t-sne

https://www.kaggle.com/code/rhtsingh/utilizing-transformer-representations-efficiently

https://www.kaggle.com/code/yasufuminakama/fb3-deberta-v3-base-baseline-train

https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/writeups/michael-jahrer-1st-place-with-representation-learn


https://www.kaggle.com/competitions/porto-seguro-safe-driver-prediction/code?competitionId=7082&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/tabular-playground-series-sep-2021/code?competitionId=28009&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/santa-2025/code?competitionId=119106&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/playground-series-s6e1/code?competitionId=119082&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/lish-moa/code?competitionId=19988&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/home-credit-credit-risk-model-stability/code?competitionId=50160&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/competitions/riiid-test-answer-prediction/code?competitionId=21651&sortBy=voteCount&excludeNonAccessedDatasources=true

https://www.kaggle.com/code/swarnabha/pytorch-text-classification-torchtext-lstm

https://www.kaggle.com/code/jeongyoonlee/autoencoder-pseudo-label-autolgb
# great of great
https://www.kaggle.com/code/jimthompson/ensemble-model-stacked-model-example

https://www.kaggle.com/code/bansodesandeep/sentiment-analysis-support-vector-machine

https://www.kaggle.com/code/bansodesandeep/sentiment-analysis-support-vector-machine

https://www.kaggle.com/competitions/competitive-data-science-predict-future-sales/code?competitionId=8587&sortBy=voteCount&excludeNonAccessedDatasources=true


https://www.kaggle.com/code/vad13irt/optimization-approaches-for-transformers/notebook
