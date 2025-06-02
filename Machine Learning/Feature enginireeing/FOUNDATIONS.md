###### BOOKS
Feature Engineering and Selection, 2019.
https://amzn.to/3aydNGf
 Feature Engineering for Machine Learning, 2018.
https://amzn.to/2XZJNR2




**Complex Data**: Raw data contains compressed complex nonlinear relationships that may need to be exposed
**Messy Data**: Raw data contains statistical noise, errors, missing values, and conflicting examples.

# CH3 Tour of Data Preparation Techniques


## 3.3 Data Cleaning
![](https://i.imgur.com/XIDcFQI.png)

## 3.4 Feature Selection
the supervised techniques can be further divided into models that automatically select features as part of fitting the model (intrinsic), those that explicitly choose features that result in the best performing model (wrapper) and those that score each input feature and allow a subset to be selected (filter).
![](https://i.imgur.com/iotbyqD.png)


## 3.5 Data Transforms
![](https://i.imgur.com/XdBzHuj.png)
![](https://i.imgur.com/DQxzU41.png)


## 3.6 Feature Engineering
## 3.7 Dimensionality Reduction
![](https://i.imgur.com/2slaumW.png)


# CH4 Data Preparation Without Data Leakage
The problem with applying data preparation techniques before splitting data for model evaluation is that it can lead to data leakage and, in turn, will likely result in an incorrect estimate of a model’s performance on the problem.Data leakage refers to a problem where information about the holdout dataset, such as a test or validation dataset, is made available to the model in the training dataset. This leakage is often small and subtle but can have a marked effect on performance.

We get data leakage by applying data preparation techniques to the entire dataset.Data preparation must be fit on the training dataset only.
1. Split Data.
2. Fit Data Preparation on Training Dataset.
3. Apply Data Preparation to Train and Test Datasets.
4. Evaluate Models.
More generally, the entire modeling pipeline must be prepared only on the training dataset to avoid data leakage. This might include data transforms, but also other techniques such feature selection, dimensionality reduction, feature engineering and more. This means so-called model evaluation should really be called modeling pipeline evaluation.



###### RepeatedStratifiedKFold: What Does "Repeated" Mean?
`RepeatedStratifiedKFold` is an **extension** of `StratifiedKFold` that **repeats** the cross-validation process multiple times with different splits.

#### **Key Difference:**

- **`StratifiedKFold(n_splits=10, shuffle=True, random_state=7)`**
    
    - Splits data **once** into 10 folds, ensuring **class balance** in each fold.
        
- **`RepeatedStratifiedKFold(n_splits=10, n_repeats=3, random_state=7)`**
    
    - Repeats the **10-fold CV** process **3 times** with different random splits.
#### **Why Use `RepeatedStratifiedKFold`?**

✅ **More Reliable Performance Estimates:** Averaging results over multiple runs reduces the impact of any **lucky/unlucky splits**.  
✅ **Helps with Small Datasets:** If the dataset is small, a single split might not represent the full variance in data.  
✅ **Improves Model Evaluation Stability:** Provides a **smoother** estimation of the model's performance.#### **Why Use `RepeatedStratifiedKFold`?**

✅ **More Reliable Performance Estimates:** Averaging results over multiple runs reduces the impact of any **lucky/unlucky splits**.  
✅ **Helps with Small Datasets:** If the dataset is small, a single split might not represent the full variance in data.  
✅ **Improves Model Evaluation Stability:** Provides a **smoother** estimation of the model's performance.


### 4.4.2 Cross-Validation Evaluation With Correct Data Preparation
Data preparation without data leakage when using cross-validation is slightly more challenging. It requires that the data preparation method is prepared on the training set and applied to the train and test sets within the cross-validation procedure, e.g. the groups of folds of rows. We can achieve this by defining a modeling pipeline that defines a sequence of data preparation steps to perform and ending in the model to fit and evaluate.


# 4.5 Further Reading

 Feature Engineering and Selection: A Practical Approach for Predictive Models, 2019.
https://amzn.to/2VLgpex
 Applied Predictive Modeling, 2013.
https://amzn.to/2VMhnat
 Data Mining: Practical Machine Learning Tools and Techniques, 2016.
https://amzn.to/2Kk6tn0
 Feature Engineering for Machine Learning, 2018.
https://amzn.to/2zZOQXN