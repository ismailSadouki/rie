

1. [[Idea]]
2. [[Readings]]
3. [[Mastering Bayesian ML]]
4. [[Dynamical Systems]]
	- _Henon map_, _tent map_, _noisy oscillators_
	- Predator-Prey
	- Chaotic Time Series
	- Dense layers
	- Sprott B
5. [[Metaheuristic Optimization]]
	- [[Genetic Algorithms]]
6. [[Simulation]]
7. [[Uncertainty Quantification (UQ)]]
	- uncertainty analysis
8. [[ablation study]]
9. [[Probabilistic Programming]]
10. [[MT/books|books]] 
11. [[Chaos theory]]
12. [[Networking]]


### ⚙️ **Methodology:**

1. **Modeling Discrete Dynamical Systems:**
    
    - Use classical chaotic maps (e.g., logistic map, Sprott B) as case studies.
        
    - Generate data using different parameter settings and noise levels.
        
2. **Bayesian Neural Network Implementation:**
    
    - Implement a probabilistic neural network using either:
        
        - MC Dropout (Gal & Ghahramani)
            
        - BayesianDense layers (TFP or Pyro)
            
    - Output mean and variance for each prediction.
        
3. **Optimization with Genetic Algorithms:**
    
    - Apply GA to optimize architecture and training parameters:
        
        - Number of layers, hidden units
            
        - Learning rate, dropout rate
            
        - Uncertainty calibration score
            
4. **Evaluation Metrics:**
    
    - Prediction accuracy
        
    - Negative log-likelihood
        
    - Uncertainty quality (expected calibration error, sharpness)
        
    - Ablation studies on GA vs. baseline



### ✨ **Contributions:**

- First known application of BNNs to model **difference equations** with explicit uncertainty.
    
- Integration of **GA optimization** in BNN training for dynamical systems.
    
- A reproducible framework for combining probabilistic learning and evolutionary optimization in time-discrete systems.



|الترتيب|الكتاب|لماذا هو مناسب لك|
|---|---|---|
|✅ 1|**Neural Networks and Deep Learning** – _Michael Nielsen_|أفضل بداية ممكنة – يشرح كل شيء ببساطة وعمق بدون تعقيد|
|✅ 2|**Machine Learning with PyTorch and Scikit-Learn** – _Sebastian Raschka_|عملي ومنظم جدًا – يربط بين ML وDL، ويشرح PyTorch بسلاسة|
|✅ 3|**Deep Learning with PyTorch** – _Eli Stevens (الكتاب الرسمي)_|يأخذك لمستوى احترافي في PyTorch – مشروع كامل + تدريب|
|✅ 4|**Dive Into Deep Learning (D2L.ai)** – _Zhang et al._|يغطي المفاهيم العميقة بوضوح + كود عملي – مثالي لمرحلة متوسطة|
|✅ 5|**The Elements of Statistical Learning (ESL)**|ضروري لتحليل النتائج في مشروعك، وفهم التقييم الإحصائي|
|✅ 6|**Probabilistic Deep Learning** – _Oliver Dürr_|أهم مرجع حاليًا لفهم BNN وUncertainty + تطبيقي جدًا|
|✅ 7|**Introduction to Genetic Algorithms** – _Sivanandam_|ممتاز لتعلم خوارزميات GA التي ستدمجها في مشروعك|
|✅ 8|**Bayesian Reasoning and Machine Learning** – _David Barber_|مرجع قوي لفهم الجانب الاحتمالي والـ VI بعمق|
|⏳ 9|**Understanding Machine Learning** – _Shalev-Shwartz_|تستخدمه لاحقًا لو أردت مستوى أكاديمي عالي في النظرية|
|⏳ 10|**Deep Learning** – _Goodfellow et al._|مرجع شامل وثقيل، تقرأ منه ما تحتاج لاحقًا فقط|











Bayesian Neural Networks for Learning Chaotic Maps: A Metaheuristic Optimization Approach --- what is chaotic maps

