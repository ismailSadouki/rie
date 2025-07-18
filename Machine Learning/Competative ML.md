
# 🛠️ **Top Tools for Hyperparameter Tuning**

| Tool                        | Description                                                                 |
| --------------------------- | --------------------------------------------------------------------------- |
| **Optuna**                  | Modern, fast, pruning-supported, used in top Kaggle solutions               |
| **Ray Tune**                | Scalable tuning with distributed support, early stopping, good with PyTorch |
| **Hyperopt**                | Uses TPE (Bayesian-like), simple for sklearn & keras                        |
| **Ax + BoTorch (Meta)**     | Research-grade Bayesian Optimization                                        |
| **Weights & Biases Sweeps** | Easy to visualize experiments & sweep hyperparams                           |

## 📄 **Key Research Papers / Concepts**

If you're competitive, reading a few high-impact papers gives an edge:

1. **"Practical Bayesian Optimization of Machine Learning Algorithms" – Snoek et al. (2012)**
    
    - Introduced Gaussian Process–based tuning in ML.
        
2. **"BOHB: Robust and Efficient Hyperparameter Optimization at Scale" – Falkner et al. (2018)**
    
    - Combines Bayesian Optimization + Hyperband. Widely used in Optuna and Ray Tune.
        
3. **"Population Based Training of Neural Networks" – DeepMind (2017)**
    
    - Online adaptation of hyperparameters during training.
        
4. **"A Systematic Review of Hyperparameter Optimization for Machine Learning" (2020)** – A great survey of all methods.
    

---
## 🚀 Suggested Learning Path (to become tuning expert)

1. **Read**:
    
    - Garnett’s Bayesian Optimization book (start here)
        
    - HPO in ML by Tan et al. (full theory and methods)
        
2. **Implement**:
    
    - Use `Optuna` and `Ray Tune` on real Kaggle datasets.
        
    - Try pruning + multi-objective optimization.
        
3. **Compete**:
    
    - Focus on tuning + stacking models. Tune CV folds, seeds, learning rates, max_depth, etc.
        
    - Learn how **different optimizers behave** (Adam vs SGD, etc.)


✅ **Top Tools for Tuning (You Must Know These)**

| Tool                             | Strengths                                                               |
| -------------------------------- | ----------------------------------------------------------------------- |
| **Optuna**                       | State-of-the-art, fast, pruning-aware, great for LGBM, CatBoost, DL.    |
| **Ray Tune**                     | Best for **large-scale or distributed tuning**, multi-GPU, parallelism. |
| **BOHB / SMAC3**                 | Industry-grade, based on academic research (used in AutoML frameworks). |
| **Ax + BoTorch (Meta/Facebook)** | **Research-grade Bayesian optimization**, powerful but complex.         |
| **Weights & Biases Sweeps**      | Great for experiment tracking + hyperparam tuning visually.             |
✅ **Most Important Research Papers to Read** (if you want an edge)

| Paper                                                                        | Why It's Top                                                      |
| ---------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| 🔬 Snoek et al., 2012 – _"Practical Bayesian Optimization of ML Algorithms"_ | Foundational paper that popularized Bayesian tuning.              |
| 🚀 Falkner et al., 2018 – _"BOHB"_                                           | Combines Bayesian + Hyperband (used in many modern tools).        |
| 📊 Smith et al., 2017 – _"Cyclical Learning Rates"_                          | Shows how tuning learning rate schedules boosts performance.      |
| 🧬 Jaderberg et al., 2017 – _"Population-Based Training"_ (DeepMind)         | Advanced method that **evolves hyperparameters during training**. |

## ✅ If You Want to Specialize:

- Bayesian Optimization → Garnett's book + BoTorch
    
- AutoML systems → Hutter’s book
    
- Kaggle-level practical tuning → Abhishek Thakur + Optuna
    
- Research + scaling → Tan’s 2024 book + Ray Tune



**SOTA trends**: read them
NeurIPS and ICLR competition papers: https://neurips.cc/virtual/2024/papers.html?filter=titles

**Resource**: Read 50+ Kaggle Grandmaster discussions. It’s like a war journal.


## 🧨 5. **Master Ensembling & Blending**

Dangerous competitors know how to combine weak models into a killer solution.

- Learn:
    
    - Simple averaging
        
    - Rank averaging
        
    - Correlation-based model selection
        
    - Stacking (with meta-models)
        
    - Blending public/private LB strategies
        

**Golden rule**: Avoid models that correlate too much.

https://sgfin.github.io/files/cheatsheets/cs229_2018_cheatsheet.pdf

https://sebastianraschka.com/blog/2021/dl-course.html