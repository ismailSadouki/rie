- is there outliers?
- `3SsnPorch` / `LowQualFinSF` / `ScreenPorch` consider log- transform
- `YrSold` → 5 unique years → keep numeric, or treat as categorical if you suspect year trends matter.
- `MSSubClass` → 15 → discrete class of building → can one-hot encode or leave numeric.




## 🎯 Big list of regression models / methods



| #   | Model / Method                                                                                                                                                                                                                                                                                                                                                                |
| --- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   |                                                                                                                                                                                                                                                                                                                                                                               |
| 2   |                                                                                                                                                                                                                                                                                                                                                                               |
| 3   |                                                                                                                                                                                                                                                                                                                                                                               |
| 4   |                                                                                                                                                                                                                                                                                                                                                                               |
| 5   | - Locally Linear Embedding (LLE)                                                                                                                                                                                                                                                                                                                                              |
| 6   | - Robust Principal Component Analysis (RPCA)                                                                                                                                                                                                                                                                                                                                  |
| 7   | ## [Autoencoders:](https://mcortesgranados.github.io/CV_MCG/CONCEPTS/MLM/Autoencoders.html)                                                                                                                                                                                                                                                                                   |
| 8   | ##  [Word Embeddings (e.g., Word2Vec):](https://mcortesgranados.github.io/CV_MCG/CONCEPTS/MLM/Word2Vec.html)                                                                                                                                                                                                                                                                  |
| 9   | ## [Self-Organizing Maps (SOM):](https://mcortesgranados.github.io/CV_MCG/CONCEPTS/MLM/SOM.html)                                                                                                                                                                                                                                                                              |
| 10  | ## [Boltzmann Machines:](https://mcortesgranados.github.io/CV_MCG/CONCEPTS/MLM/Boltzmann%20Machines.html)                                                                                                                                                                                                                                                                     |
| 11  |                                                                                                                                                                                                                                                                                                                                                                               |
| 12  |                                                                                                                                                                                                                                                                                                                                                                               |
| 13  | **CatBoost Regression** — gradient boosting variant, often robust and handles categorical data well ([LinkedIn](https://www.linkedin.com/pulse/types-machine-learning-models-from-basics-advanced-naresh-maddela-wsqhc?utm_source=chatgpt.com "Types of Machine Learning Models From Basics to Advanced"))                                                                    |
| 14  | **Ensemble methods: Bagging / Stacking / Blending** (e.g. bagged trees, stacking multiple regressors) — combine strengths of multiple models for better generalization ([michael-fuchs-python.netlify.app](https://michael-fuchs-python.netlify.app/2019/07/24/further-regression-algorithms/?utm_source=chatgpt.com "Further Regression Algorithms - Michael Fuchs Python")) |
| 15  |                                                                                                                                                                                                                                                                                                                                                                               |
| 16  |                                                                                                                                                                                                                                                                                                                                                                               |
| 17  |                                                                                                                                                                                                                                                                                                                                                                               |
| 18  |                                                                                                                                                                                                                                                                                                                                                                               |
| 19  | **Projection Pursuit Regression (PPR)** — a flexible semi‑nonlinear regression technique using smooth functions on projected components ([Wikipedia](https://en.wikipedia.org/wiki/Projection_pursuit_regression?utm_source=chatgpt.com "Projection pursuit regression"))                                                                                                     |
| 20  | **Multivariate Adaptive Regression Splines (MARS)** — piecewise-linear regression (splines) that can capture non‑linear relationships / interactions (common in flexible regression tasks) — not always in scikit-learn but used in research/Kaggle community.                                                                                                                |
| 21  |                                                                                                                                                                                                                                                                                                                                                                               |
| 22  |                                                                                                                                                                                                                                                                                                                                                                               |
| 23  | **Generalized Linear Models (GLMs)**                                                                                                                                                                                                                                                                                                                                          |
| 24  | SVR (with kernel), SVR (RBF)                                                                                                                                                                                                                                                                                                                                                  |


| #   | Model / Method                                                                         | Description / Why It’s Useful                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| --- | -------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   |                                                                                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| 2   |                                                                                        | Helps with multicollinearity and overfitting in high‑dimensional data. ([Medium](https://medium.com/%40marttraagel/predicting-house-sales-advanced-regression-techniques-in-kaggle-30fe419428c8?utm_source=chatgpt.com "Predicting house sales — Advanced Regression Techniques on Kaggle \| by Mart Traagel \| Medium"))                                                                                                                                                                                                                                                                                           |
| 3   |                                                                                        | Encourages sparsity — good for feature selection, simpler models. ([Medium](https://medium.com/%40marttraagel/predicting-house-sales-advanced-regression-techniques-in-kaggle-30fe419428c8?utm_source=chatgpt.com "Predicting house sales — Advanced Regression Techniques on Kaggle \| by Mart Traagel \| Medium"))                                                                                                                                                                                                                                                                                                |
| 4   |                                                                                        | Hybrid of Ridge & Lasso — balances sparsity and shrinkage for often better generalization. ([Medium](https://medium.com/%40marttraagel/predicting-house-sales-advanced-regression-techniques-in-kaggle-30fe419428c8?utm_source=chatgpt.com "Predicting house sales — Advanced Regression Techniques on Kaggle \| by Mart Traagel \| Medium"))                                                                                                                                                                                                                                                                       |
| 5   |                                                                                        | Linear regression on polynomial / interaction-expanded features — can model nonlinearity if degree tuned carefully. (Common baseline before boosting) ([Medium](https://medium.com/data-science/a-practical-introduction-to-9-regression-algorithms-389057f86eb9?utm_source=chatgpt.com "A Practical Introduction to 9 Regression Algorithms \| by YeeJay \| TDS Archive \| Medium"))                                                                                                                                                                                                                               |
| 6   |                                                                                        | Good for small to medium datasets; can model non-linear relationships if kernel + hyperparams well tuned. ([Medium](https://medium.com/%40marttraagel/predicting-house-sales-advanced-regression-techniques-in-kaggle-30fe419428c8?utm_source=chatgpt.com "Predicting house sales — Advanced Regression Techniques on Kaggle \| by Mart Traagel \| Medium"))                                                                                                                                                                                                                                                        |
| 7   |                                                                                        | Non-parametric, simple, sometimes competitive when data is cleaned & scaled properly. ([GitHub](https://github.com/ShovalBenjer/Housing_Price_Prediction_Advanced_Regresson_Kaggle?utm_source=chatgpt.com "GitHub - ShovalBenjer/Housing_Price_Prediction_Advanced_Regresson_Kaggle: This repository contains a comprehensive solution for predicting house prices using advanced regression techniques, dimensionality reduction, and hyperparameter tuning. The dataset used is from the Kaggle House Prices competition. The goal is to predict the final price of each home based on a variety of features."))  |
| 8   |                                                                                        | Handles non‑linearity, interactions; interpretable; base for many ensemble methods. ([EdPolicy Journal](https://direct.ewa.pub/proceedings/tns/article/view/22547?utm_source=chatgpt.com "Application and Comparison of Machine Learning and Traditional Regression Models for Air Quality Index Prediction in India \| Theoretical and Natural Science"))                                                                                                                                                                                                                                                          |
| 9   |                                                                                        | Ensemble of trees — reduces overfitting vs single tree; robust, works out-of-the-box, often strong baseline. ([GitHub](https://github.com/suzuran0y/house-price-regression-prediction?utm_source=chatgpt.com "GitHub - suzuran0y/house-price-regression-prediction: Predicting house prices using advanced regression techniques (Kaggle competition solution with model stacking & feature engineering)."))                                                                                                                                                                                                        |
| 10  | **Extra Trees / Extremely Randomized Trees**                                           | Similar to Random Forest but more random — often improves diversity, can boost generalization. ([ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S0893608018303411?utm_source=chatgpt.com "An extensive experimental survey of regression methods - ScienceDirect"))                                                                                                                                                                                                                                                                                                                           |
| 11  |                                                                                        | Builds trees sequentially to correct errors — often outperforms RF when tuned. ([Wikipedia](https://en.wikipedia.org/wiki/Gradient_boosting?utm_source=chatgpt.com "Gradient boosting"))                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 12  |                                                                                        | Highly optimized GBM, efficient & powerful, widely used in competitions and research. ([GitHub](https://github.com/suzuran0y/house-price-regression-prediction?utm_source=chatgpt.com "GitHub - suzuran0y/house-price-regression-prediction: Predicting house prices using advanced regression techniques (Kaggle competition solution with model stacking & feature engineering)."))                                                                                                                                                                                                                               |
| 13  |                                                                                        | Fast, handles large datasets and high-dimensional features well, often competitive with XGBoost. ([Manuela Cortés Granados](https://mcortesgranados.github.io/CV_MCG/CONCEPTS/machine%20learning%20models.html?utm_source=chatgpt.com "Comprehensive List of Machine Learning Models"))                                                                                                                                                                                                                                                                                                                             |
| 14  | **Boosting Variants: CatBoost**                                                        | Handles categorical features well, often reduces need for heavy preprocessing — good for raw tabular data. ([MDPI](https://www.mdpi.com/2076-3417/15/6/3320?utm_source=chatgpt.com "Comparative Analysis of Advanced Machine Learning Regression Models with Advanced Artificial Intelligence Techniques to Predict Rooftop PV Solar Power Plant Efficiency Using Indoor Solar Panel Parameters"))                                                                                                                                                                                                                  |
| 15  | **Bagging / Bootstrap‑Aggregating Ensembles**                                          | Combines multiple models to reduce variance/outliers influence — simple but effective ensemble method. ([Wikipedia](https://en.wikipedia.org/wiki/Bootstrap_aggregating?utm_source=chatgpt.com "Bootstrap aggregating"))                                                                                                                                                                                                                                                                                                                                                                                            |
| 16  | **Stacking / Blending & Model Ensembling**                                             | Combine several different models (diverse types) to leverage their strengths and smooth out weaknesses. Very effective in competitions. ([GitHub](https://github.com/suzuran0y/house-price-regression-prediction?utm_source=chatgpt.com "GitHub - suzuran0y/house-price-regression-prediction: Predicting house prices using advanced regression techniques (Kaggle competition solution with model stacking & feature engineering)."))                                                                                                                                                                             |
| 17  |                                                                                        | Flexible piecewise-linear regression model capturing non-linearities and interactions without excessive complexity. ([Wikipedia](https://en.wikipedia.org/wiki/Multivariate_adaptive_regression_spline?utm_source=chatgpt.com "Multivariate adaptive regression spline"))                                                                                                                                                                                                                                                                                                                                           |
| 18  |                                                                                        | For sparse high-dimensional data: automatically select a subset of predictors, balancing accuracy & simplicity. ([Wikipedia](https://en.wikipedia.org/wiki/Least-angle_regression?utm_source=chatgpt.com "Least-angle regression"))                                                                                                                                                                                                                                                                                                                                                                                 |
| 19  |                                                                                        | Probabilistic models — good when you want uncertainty estimation and non-parametric flexibility. ([PubMed](https://pubmed.ncbi.nlm.nih.gov/30654138/?utm_source=chatgpt.com "An extensive experimental survey of regression methods - PubMed"))                                                                                                                                                                                                                                                                                                                                                                     |
| 20  |                                                                                        | Useful when error distribution is non-normal, or when you care about medians/quantiles instead of mean. ([OUP Academic](https://academic.oup.com/bib/article/22/4/bbaa321/6032614?utm_source=chatgpt.com "Do we need different machine learning algorithms for QSAR modeling? A comprehensive assessment of 16 machine learning algorithms on 14 QSAR data sets \| Briefings in Bioinformatics \| Oxford Academic"))                                                                                                                                                                                                |
| 21  |                                                                                        | Good for capturing complex nonlinear interactions, when data size is large enough and features well-engineered. ([IIETA](https://www.iieta.org/journals/mmep/paper/10.18280/mmep.090508?utm_source=chatgpt.com "A Comparative Study of Regression Machine Learning Algorithms: Tradeoff Between Accuracy and Computational Complexity \| IIETA"))                                                                                                                                                                                                                                                                   |
| 22  |                                                                                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| 23  | **Rule‑based / Hybrid models (e.g. Cubist / M5 / rule‑plus‑NN hybrids)**               | Combine decision rules and regression — sometimes highly accurate, especially on structured tabular data. ([PubMed](https://pubmed.ncbi.nlm.nih.gov/30654138/?utm_source=chatgpt.com "An extensive experimental survey of regression methods - PubMed"))                                                                                                                                                                                                                                                                                                                                                            |
| 24  |                                                                                        | Resistant to outliers and heavy-tailed noise — good if data has many outliers or weird distributions. ([ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S0893608018303411?utm_source=chatgpt.com "An extensive experimental survey of regression methods - ScienceDirect"))                                                                                                                                                                                                                                                                                                                    |
| 25  |                                                                                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| 26  |                                                                                        | Useful when features are many and multicollinear — reduces dimensionality before regression. ([ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S0893608018303411?utm_source=chatgpt.com "An extensive experimental survey of regression methods - ScienceDirect"))                                                                                                                                                                                                                                                                                                                             |
| 27  | **Ensemble of Heterogeneous Models (Super‑Learner / “Super Ensemble”)**                | Combines many diverse base models (trees, linear, kernel, neural) — tends to outperform any single model. ([arXiv](https://arxiv.org/abs/1909.04131?utm_source=chatgpt.com "Super ensemble learning for daily streamflow forecasting: Large-scale demonstration and comparison with multiple machine learning algorithms"))                                                                                                                                                                                                                                                                                         |
| 28  | **Histogram‑based Gradient Boosting (sklearn‑hist / Light / internal histogram GBMs)** | Like GBM but more efficient, good for large datasets and reduces overfitting with good hyper‑tuning. ([MDPI](https://www.mdpi.com/2076-3417/15/6/3320?utm_source=chatgpt.com "Comparative Analysis of Advanced Machine Learning Regression Models with Advanced Artificial Intelligence Techniques to Predict Rooftop PV Solar Power Plant Efficiency Using Indoor Solar Panel Parameters"))                                                                                                                                                                                                                        |
| 29  |                                                                                        | Non‑parametric, adapts to local data structure — valuable when global linearity fails. ([GitHub](https://github.com/ShovalBenjer/Housing_Price_Prediction_Advanced_Regresson_Kaggle?utm_source=chatgpt.com "GitHub - ShovalBenjer/Housing_Price_Prediction_Advanced_Regresson_Kaggle: This repository contains a comprehensive solution for predicting house prices using advanced regression techniques, dimensionality reduction, and hyperparameter tuning. The dataset used is from the Kaggle House Prices competition. The goal is to predict the final price of each home based on a variety of features.")) |
| 30  | **Mixed / Hybrid Models: e.g. stacking linear + tree + neural + kernel**               | Combine strengths (interpretability, nonlinearity, robustness) — often top performers in real-world or competition tasks. ([GitHub](https://github.com/suzuran0y/house-price-regression-prediction?utm_source=chatgpt.com "GitHub - suzuran0y/house-price-regression-prediction: Predicting house prices using advanced regression techniques (Kaggle competition solution with model stacking & feature engineering)."))                                                                                                                                                                                           |


---












Absolutely — let’s expand the list **even further**, including more specialized, modern, and accuracy-focused regression methods that are used in research, Kaggle competitions, and industrial applications. I’ll aim to cover **50+ methods** now.

---

## ⚡ Extended List of Regression Models for Accuracy

| #   | Model / Method                                              | Notes / When It’s Useful                                                                                               |
| --- | ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| 31  |                                                             | Robust to outliers; linear but less sensitive to extreme values.                                                       |
| 32  |                                                             | Robust linear estimator; good for small datasets with outliers.                                                        |
| 33  |                                                             | Iteratively fits subsets; ignores outliers; robust for noisy datasets.                                                 |
| 34  |                                                             | Reduces dimensionality; useful when predictors are correlated.                                                         |
| 35  |                                                             | PCA for features + linear regression; reduces multicollinearity.                                                       |
| 36  |                                                             | Sparse regression; selects a subset of features for prediction.                                                        |
| 37  |                                                             | Probabilistic linear regression; regularization tuned automatically.                                                   |
| 38  |                                                             | Bayesian; identifies which features matter.                                                                            |
| 39  |                                                             | Non-parametric, models uncertainty; good for smooth functions.                                                         |
| 40  |                                                             | Sparse kernel-based regression; can improve generalization.                                                            |
| 41  |                                                             | Fits locally to capture nonlinearity; small datasets.                                                                  |
| 42  |                                                             | Ridge regression with kernels; handles nonlinearity.                                                                   |
| 43  |                                                             | Neural networks; flexible for complex nonlinear interactions.                                                          |
| 44  |                                                             | When spatial / structured features exist (e.g., images or grids).                                                      |
| 45  |                                                             | For sequential/time-series regression problems.                                                                        |
| 46  | **Extreme Learning Machines (ELM)**                         | Fast single-hidden-layer neural networks; sometimes competitive.                                                       |
| 47  | **Gradient Boosted Decision Trees (GBDT / GBM)**            | Sequential trees; widely used for accuracy-focused tabular regression.                                                 |
| 48  | **XGBoost**                                                 | Highly optimized GBDT; often first choice in Kaggle competitions.                                                      |
| 49  | **LightGBM**                                                | Histogram-based GBM; handles large datasets efficiently.                                                               |
| 50  | **CatBoost**                                                | Handles categorical data natively; reduces preprocessing.                                                              |
| 51  | **Histogram-based Gradient Boosting (sklearn 1.2+)**        | Efficient tree-based boosting for large datasets.                                                                      |
| 52  | **Extra Trees Regressor**                                   | Randomized trees; often reduces variance over Random Forest.                                                           |
| 53  | **Cubist / M5P Regression**                                 | Rule-based regression; used in tabular prediction competitions.                                                        |
| 54  | **Symbolic Regression (Genetic Programming)**               | Auto-learns formulas; interpretable + competitive on noisy data.                                                       |
| 55  | **Quantile Regression Forests**                             | Provides prediction intervals; handles outliers better.                                                                |
| 56  | **Gradient Boosted Quantile Regression**                    | Predicts medians/quantiles; robust to skewed target distributions.                                                     |
| 57  | **Generalized Additive Models (GAM)**                       | Sum of smooth functions of features; interpretable and flexible.                                                       |
| 58  | **Spline Regression (Cubic / B-spline)**                    | Fits smooth curves; good for non-linear but smooth relationships.                                                      |
| 59  | **Multivariate Adaptive Regression Splines (MARS / Earth)** | Piecewise linear regression; handles non-linearities & interactions automatically.                                     |
| 60  | **Adaptive Boosted Trees (AdaBoost Regressor)**             | Combines weak learners; less common than GBDT but still useful.                                                        |
| 61  | **Bagged Linear Models**                                    | Combines multiple linear models on bootstrap samples.                                                                  |
| 62  | **Robust Scaler + Regression Pipelines**                    | Not a model, but combining robust scaling + Ridge/Lasso often improves performance on skewed / outlier-heavy features. |
| 63  | **Deep Forest (gcForest / Cascade Forest)**                 | Ensemble of multi-layered forests; competitive with deep learning on tabular data.                                     |
| 64  | **Stacked Ensembles (Heterogeneous Models)**                | Combine linear, tree, kernel, neural networks; usually the best accuracy on tabular data.                              |
| 65  | **Mixture of Experts Regression**                           | Combines specialized regressors on different regions of data; sometimes used in Kaggle or research.                    |
| 66  | **Neural Basis Expansion Regression (N-BEATS)**             | Modern deep architecture for time series; can be adapted to tabular regression.                                        |
| 67  | **Quantile Regression Neural Networks (QRNN)**              | Combines neural nets with quantile regression; handles skewed targets and uncertainty.                                 |
| 68  | **TreeSHAP-guided Ensembles**                               | Uses SHAP values to select / weight features in tree ensembles — sometimes improves accuracy.                          |
| 69  | **Bayesian Optimization-enhanced Regression**               | Optimize hyperparameters of almost any model (GBM, NN, SVR) automatically for accuracy.                                |
| 70  | **Hybrid / Custom Pipelines**                               | Example: PLS + Ridge + LightGBM stacking; symbolic regression + XGBoost — used in high-end Kaggle solutions.           |

---

### 🔹 Observations

1. Many of the best-performing regression approaches today are **ensembles of ensembles**, e.g., stacking XGBoost + LightGBM + CatBoost + Ridge.
    
2. Robust regression and preprocessing (outliers, skewness, scaling) often **improve any model’s accuracy** by 5–20% in real-world datasets.
    
3. Neural networks are only competitive when features are engineered and dataset is moderately large; otherwise, tree-based ensembles dominate.
    
4. Probabilistic / Bayesian models (GPR, RVR, Bayesian Ridge) are useful when you want uncertainty quantification or sparse solutions.
    
5. Rule-based (M5 / Cubist) and MARS are **underutilized but can improve accuracy**, especially on tabular datasets with interactions.
    

---

If you want, I can **create a Python template** with **20+ of these models**, with:

- Preprocessing pipelines (scaling, outlier handling, skewness)
    
- CV evaluation with RMSE / R²
    
- Ready-to-run **stacking ensemble framework**
    

This way you can **quickly test 20–30 models on your dataset** without writing them manually.

Do you want me to do that next?








### Deep Learning Models

These models use neural networks for feature extraction and learning.

### Feedforward Neural Networks (FNN)

1. Multi-Layer Perceptron (MLP)

### Convolutional Neural Networks (CNN)

1. LeNet
2. AlexNet
3. VGGNet
4. GoogLeNet (Inception Networks)
5. ResNet (Residual Networks)
6. DenseNet
7. EfficientNet
8. MobileNet
9. Vision Transformers (ViTs)

### Recurrent Neural Networks (RNN)

1. Simple RNN
2. Long Short-Term Memory (LSTM)
3. Gated Recurrent Unit (GRU)
4. Bidirectional LSTM/GRU

### Transformer-Based Models

1. Transformer (Original by Vaswani et al.)
2. BERT (Bidirectional Encoder Representations from Transformers)
3. GPT (Generative Pre-trained Transformer, GPT-1, GPT-2, GPT-3, GPT-4, etc.)
4. T5 (Text-to-Text Transfer Transformer)
5. XLNet
6. RoBERTa
7. ALBERT
8. DistilBERT
9. BART (Bidirectional and Auto-Regressive Transformers)
10. Whisper (Speech-to-Text by OpenAI)

  

### Generative Models

1. Autoencoders (Vanilla, Variational, Denoising)
2. Generative Adversarial Networks (GANs) Vanilla GAN Deep Convolutional GAN (DCGAN) Conditional GAN (cGAN) StyleGAN CycleGAN Pix2Pix
3. Normalizing Flows
4. Diffusion Models (DALL-E, Stable Diffusion, Imagen, etc.)

### Graph Neural Networks (GNN)

1. Graph Convolutional Networks (GCN)
2. Graph Attention Networks (GAT)
3. GraphSAGE
4. ChebNet (Chebyshev Graph CNNs)
5. GNN Explainers

### Self-Supervised Learning Models

1. SimCLR (Simple Framework for Contrastive Learning)
2. BYOL (Bootstrap Your Own Latent)
3. MoCo (Momentum Contrast)
4. DINO (Self-Supervised Transformers)
5. MAE (Masked Autoencoders for Vision Tasks)

---

### 6. Hybrid Models & Meta-Learning

1. Stacking Models
2. Blending Models
3. Bayesian Optimization-Based Learning
4. Neural Architecture Search (NAS)
5. Few-Shot Learning Models (Siamese Networks, Prototypical Networks, Matching Networks, etc.)
6. Federated Learning Models




















Nice! If you’re working on the **Ames Housing** dataset with **Advanced Linear Regression**, here’s a clean roadmap + what you should focus on to get strong performance and avoid common mistakes.

---

# ✅ **1. Core Strategy for Advanced Linear Regression**

Ames has:

- **37 numeric features**
    
- **47 categorical features**
    

Linear regression works best when features are:

- scaled
    
- encoded correctly
    
- not heavily multicollinear
    
- reasonably linear with the target
    

So the strategy is:

### **Step-by-step Pipeline**

1. **Start with numeric features only**  
    ✔ Yes, this is _good and fast_  
    It lets you iterate quickly on modeling, transformations, feature selection, etc.
    
2. **Explore relationships with SalePrice**
    
    - Correlation heatmap
        
    - Scatterplots for top features
        
    - Check skewness → apply log1p where needed
        
    - SalePrice is skewed → do `y = np.log1p(SalePrice)`
        
3. **Transform numeric features**
    
    - `StandardScaler`
        
    - `QuantileTransformer` or `PowerTransformer` for non-normal features
        
    - Outlier handling: capping or Winsorizing
        
4. **Feature selection / regularization**
    
    - Ridge
        
    - Lasso
        
    - ElasticNet  
        These are **must-use** in Ames.
        
5. **Then add categorical features**  
    After your numeric pipeline is stable:
    
    - Use `OneHotEncoder(handle_unknown="ignore")`
        
    - But this creates **300–400 columns** → regularization becomes necessary.
        
6. **Use a full Scikit-learn Pipeline**  
    This is the best practice:
    

```python
preprocess = ColumnTransformer([
    ("num", StandardScaler(), numeric_cols),
    ("cat", OneHotEncoder(handle_unknown="ignore"), categorical_cols)
])

model = Pipeline([
    ("pre", preprocess),
    ("reg", ElasticNet(alpha=0.001, l1_ratio=0.5))
])
```

---

# ✅ **2. Should you start only with numeric features?**

**YES — Best strategy.**

Reasons:

- Much faster experiments
    
- Easy to check linearity, collinearity
    
- Helps you tune regularization (α) correctly
    
- Avoids exploding feature space early
    

Then add categorical features after the numeric modeling stabilizes.

---

# ✅ **3. Best Models for Advanced Linear Regression**

Here are **30+** linear + quasi-linear models you can apply:

### **Pure Linear (Baseline)**

1. Ordinary Least Squares
    
2. Ridge Regression
    
3. Lasso
    
4. ElasticNet
    
5. Bayesian Ridge
    
6. Automatic Relevance Determination (ARD)
    
7. Huber Regressor
    
8. Theil-Sen Regressor
    
9. RANSAC Regressor
    
10. Tweedie Regressor (GLM)
    
11. Poisson Regressor (GLM)
    
12. Gamma Regressor (GLM)
    

### **Regularized Linear Variants**

13. LARS (Least Angle Regression)
    
14. Lasso-LARS
    
15. Orthogonal Matching Pursuit
    
16. Quantile Regression
    
17. Polynomial Regression (degree 2)
    
18. Spline regression (patsy / scikit-learn)
    
19. RobustScaler + Ridge
    
20. RobustScaler + Lasso
    

### **Dimension Reduction + Linear**

21. PCA + Linear Regression
    
22. PCA + Ridge
    
23. PCA + Lasso
    
24. ICA + Linear Regression
    
25. Partial Least Squares (PLSRegression)
    
26. Kernel PCA + Linear Regression
    

### **Sparse Techniques**

27. L1-based feature selection + OLS
    
28. ElasticNet + SelectFromModel
    

### **High-Performance Linear-like models**

(These are NOT tree models, but generalized linear methods)

29. SGDRegressor (Linear model trained as SGD)
    
30. Passive-Aggressive Regressor
    
31. RidgeCV (Cross-validated automatically)
    
32. LassoCV
    
33. ElasticNetCV
    
34. LARS-CV
    
35. MultiTaskElasticNet (if using multi-target engineered features)
    

---

# ✅ **4. Pipeline Strategy That Performs Best**

If you want **top Kaggle-level performance with linear models**, follow this:

### **A. Preprocessing**

- Log-transform target
    
- Check multicollinearity → remove extremely correlated numeric features
    
- Normalize numeric features
    
- Encode categorical features
    

### **B. Try these models in order**

1. **ElasticNetCV** → usually the best
    
2. **RidgeCV**
    
3. **LassoLarsIC** (AIC/BIC-based selection)
    
4. **ARDRegression**
    
5. **HuberRegressor** (robust to outliers)
    
6. **PLSRegression** (helps when many correlated features)
    
7. **Polynomial (degree 2) + Ridge**
    

### **C. Cross-validation**

Use:

```python
KFold(n_splits=10, shuffle=True, random_state=42)
```

---

# Want me to generate:

🔥 A ready-to-use scikit-learn code template for your full Ames pipeline?  
🔥 Or a full modelling notebook structure?  
🔥 Or help optimize your current model?























----


---








# 🧠 **Short answer: WHY per-feature transforms?**

Because features **do not share the same distribution**, so:

### ✔ Feature with right-skew → log1p

### ✔ Feature with heavy skew → Yeo-Johnson

### ✔ Feature with mixed signs → Yeo-Johnson (log fails)

### ✔ Feature with insane outliers → Quantile (normal or uniform)

### ✔ Feature with low skew → no transform

### ✔ Feature ordinal (quality, condition) → do NOT transform






















# List of Machine Learning Models

Below is a list of 150 machine learning models, algorithms, and techniques:

- Linear Regression
- Ridge Regression
- Lasso Regression
- Decision Trees
- Random Forest
- Bagging
- Boosting
- Gradient Boosting Machines (GBM)
- XGBoost
- LightGBM
- CatBoost
- Support Vector Machines (SVM)
- k-Nearest Neighbors (KNN)
- Principal Component Analysis (PCA)
- Independent Component Analysis (ICA)
- Factor Analysis
- Canonical Correlation Analysis (CCA)
- Naive Bayes
- Gaussian Mixture Model (GMM)
- Hidden Markov Models (HMM)
- Logistic Regression
- Elastic Net
- Radial Basis Function (RBF) Kernel
- Fourier Transform
- Wavelet Transform
- Isomap
- t-Distributed Stochastic Neighbor Embedding (t-SNE)
- Uniform Manifold Approximation and Projection (UMAP)
- Robust Principal Component Analysis (RPCA)
- Locally Linear Embedding (LLE)
- Autoencoders
- Variational Autoencoders (VAE)
- Generative Adversarial Networks (GAN)
- Deep Belief Networks (DBN)
- Restricted Boltzmann Machine (RBM)
- K-Means Clustering
- Mini-Batch K-Means
- DBSCAN (Density-Based Spatial Clustering of Applications with Noise)
- Agglomerative Hierarchical Clustering
- Mean-Shift Clustering
- Fuzzy C-Means Clustering
- Affinity Propagation
- Gaussian Processes
- Conditional Random Fields (CRF)
- Recurrent Neural Networks (RNN)
- Long Short-Term Memory (LSTM)
- Gated Recurrent Unit (GRU)
- Bidirectional RNN
- Echo State Network (ESN)
- Hopfield Network
- Boltzmann Machine
- Word2Vec
- GloVe (Global Vectors for Word Representation)
- Doc2Vec
- FastText
- BERT (Bidirectional Encoder Representations from Transformers)
- GPT (Generative Pre-trained Transformer)
- Transformer-XL
- T5 (Text-To-Text Transfer Transformer)
- AdaBoost
- Multi-Adaboost
- MARS (Multivariate Adaptive Regression Splines)
- Isolation Forest
- One-Class SVM
- Word Embeddings
- Cuckoo Search
- Firefly Algorithm
- Particle Swarm Optimization (PSO)
- Genetic Algorithms
- Ant Colony Optimization
- Simulated Annealing
- Differential Evolution
- Extreme Learning Machines (ELM)
- Self-Organizing Maps (SOM)
- Locally Weighted Regression (LWR)
- CART (Classification and Regression Trees)
- SVD (Singular Value Decomposition)
- Non-Negative Matrix Factorization (NMF)
- Elastic Net
- Multi-Layer Perceptron (MLP)
- Radial Basis Function Neural Network (RBFNN)
- Quickprop
- Cascade Correlation
- NeuroEvolution of Augmenting Topologies (NEAT)
- Fuzzy Logic Systems
- Monte Carlo Methods
- Q-Learning
- Deep Q Network (DQN)
- Policy Gradient Methods
- Actor-Critic Models
- Proximal Policy Optimization (PPO)
- Trust Region Policy Optimization (TRPO)
- Asynchronous Advantage Actor-Critic (A3C)
- Deep Deterministic Policy Gradient (DDPG)
- Twin Delayed DDPG (TD3)
- Soft Actor-Critic (SAC)
- Model-Driven Reinforcement Learning
- Bayesian Optimization
- Hyperband
- Successive Halving Algorithm
- Thompson Sampling
- Online Learning
- K-Prototypes Clustering
- Mixture of Experts (MoE)
- Stacked Generalization (Stacking)
- Bootstrapped Ensembles
- Spatial Transformer Networks
- Residual Networks (ResNet)
- Inception Networks
- DenseNet
- MobileNet
- EfficientNet
- Capsule Networks
- Siamese Networks
- Style Transfer Networks
- PointNet
- Graph Neural Networks (GNN)
- Graph Convolutional Networks (GCN)
- Gated Graph Neural Networks (GGNN)
- GraphSAGE
- Attention Mechanism
- Memory Networks
- Capsule Networks
- Neural Architecture Search (NAS)
- Evolutionary Algorithms for Neural Architecture Search
- Transfer Learning
- Meta-Learning
- Few-Shot Learning
- Multi-Instance Learning
- Zero-Shot Learning
- Reinforcement Learning from Human Feedback (RLHF)
- Inverse Reinforcement Learning (IRL)
- Curriculum Learning
- Domain Adaptation
- Adversarial Training
- Model Distillation
- Contrastive Learning
- Prototype Learning
- Active Learning
- Ensemble Learning
- Federated Learning
- Homomorphic Encryption
- Quantum Machine Learning
- Neuro-Fuzzy Systems
- Online Machine Learning
- Multi-Task Learning
- Temporal Difference Learning
- Temporal Convolutional Networks (TCN)
- Word Mover's Distance
- Successive Halving Algorithm