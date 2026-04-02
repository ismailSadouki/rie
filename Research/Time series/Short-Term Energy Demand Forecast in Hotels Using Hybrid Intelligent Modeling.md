# Abstract
The hotel industry is an important energy consumer that needs efficient energy management methods to guarantee its performance and sustainability. The new role of hotels as prosumers increases the difficulty in the design of these methods. Also, the scenery is more complex as renewable energy systems are present in the hotel energy mix. The performance of energy management systems greatly depends on the use of reliable predictions for energy load. This paper presents a new methodology to predict energy load in a hotel based on intelligent techniques. The model proposed is based on a hybrid intelligent topology implemented with a combination of clustering techniques and intelligent regression methods (Artificial Neural Network and Support Vector Regression). The model includes its own energy demand information, occupancy rate, and temperature as inputs. The validation was done using real hotel data and compared with time-series models. Forecasts obtained were satisfactory, showing a promising potential for its use in energy management systems in hotel resorts.


#ARIMAX
#Hybrid_Intelligent_Modeling #Bagged_Decision_Trees #K-Means_Clustering #Multi-Layer_Perceptron #SVM #ANN 


After transportation, the hotel industry is the activity with the most energy consumption. The average hotel consumption ranges can be between 450 and 700 kWh/m2 per year, corresponding to over 60% of electricity.
Any scenario for the study of energy consumption and the incorporation of renewable energies in the tourism sector should be mainly focused on the study of the hotel sector.


## ARIMAX Modeling
ARIMAX models are obtained for the time series of the demanded power (Figure 2A) by a hotel including the occupation (Figure 2C) as an exogenous variable.
![](https://i.imgur.com/jZ4lPMd.png)
The first step is to study if the time series is stationary with the Augmented Dickey-Fuller (ADF) test.
The ADF tests the null hypothesis that the series is non-stationary. When the ADF is applied to demanded power data by the hotel recorded during a year, the test gives a p-value < 0.01, meaning that the demanded power data along a year is stationary. Therefore, the difference in the ARIMAX will be d = 0, ARIMAX(p,0,q).
However, the time series has a seasonal trend with a period of 24 h (to see Figure 2A,B) and the Auto-Correlation Function (ACF) and the Partial Auto-Correlation Function (PACF) keep oscillating outside the significance limits, indicating that the correlations are significantly different from zero. If we eliminate this component of seasonality to 24 h, we would obtain the values that are not predictable and the ACF and the PACF would stay within the significance bounds.
However, in the present work, we want to reproduce all the behavior of the total power demand in each hour using an ARIMAX model as reference model to compare the hybrid AI model developed in this research.

The auto.arima() function in R software is used to find the parameters p and q for the ARIMAX model [7]. The auto.arima() function uses a variation of the Hyndman–Khandakar algorithm [5,6], which combines unit root tests, minimization of the Akaike Information Criteria (AIC) to obtain an ARIMAX model (ARIMAX(p,d,q)).

To forecast the demand power of our hotel for the next 24 h, the ARIMAX model is trained with the time series of the previous 31 days. To validate the models, we select five validation days taking into account two different reasons.

Finally, the Ljung-Box test statistic is computed over the training data to examine the null hypothesis of independence of the residuals (the null hypothesis of the Ljung–Box test is that the model does not show lack of fit).The p-values obtained are less than 0.05, therefore we reject the null hypothesis, which means that the models show lack of fit (see Table 1).
![](https://i.imgur.com/DGDbgLT.png)


## Bagged Decision Trees Modeling
In general, the performance of a single decision tree is limited. That is why their structure is often improved by creating an ensemble of them and collecting their predictions. The main idea behind the ensemble model is that a group of individual learners join to form a more accurate learner.
One of the most efficient ensembles proposed for decision trees is based on Bootstrap Aggregation . They are called bagged trees and are used with the aim of reducing the variance of a decision tree. The main idea behind bagged trees is to create several subsets of data of the same size extracted from the training sample.

## Hybrid Intelligent Modeling

A Hybrid Intelligent model allows improvement of the global performance of a model. These types of models include a clustering and a regression phase;when the model was trained, the inputs are used to select a specific regression model to calculate the output.The internal structure of a hybrid model is shown in Figure 3. The internal regression models for each cluster could be any type of regression algorithm. In this work ANN and Support Vector Machine for Regression were used.
![](https://i.imgur.com/BFwLrgD.png)
## K-Means Clustering Algorithm
The K-Means algorithm is a well-known unsupervised technique used to create groups of data. The clusters contain data with similar characteristics among them. In our case, the algorithm is trained to minimize an error function based on the centroid Euclidean distance.
<mark>This algorithm obtains the best results when the data are well distributed in hyperspace, and the cluster data has a hypersphere shape</mark>
## Multi-Layer Perceptron
It is a feedforward ANN with one input layer, one or more hidden layers, and one output layer. Each layer has several neurons that depends on the number of inputs (in the case of input layer), the configuration of the MLP (for the hidden layers), and the number of outputs (for the output layer).
All the neurons in a layer have the same inputs that would be the input data in the case of the first layer, and the output of the previous layer in other case. Each neuron input has a specific weight, and all of them are added before calculating the output of the activation function that would be the output of the neuron. There are some typical activation functions such as Tan-Sigmoid, Log-Sigmoid, Linear, Step, etc., each for a specific application. For regression, the usual ones are the Tan-Sigmoid for the neurons in all layers, except for the output layer that used to have a linear function.

## Support Vector Machines for Regression
The Support Vector Regression is based on a nonlinear mapping to transform the data in a new high-dimensional space and, then, a linear regression is applied to calculate the desired output. In this work, a modification of SVR, called Least Squares SVR (LS-SVR), will be applied. LS-SVR uses the Least Squares to minimize the objective function [8].
In this case, a system of linear equations is solved with the aim of obtaining an approximation of the solution, which is not achieving the optimal solution that we would get with the original SVR algorithm. Nevertheless, this modification provides a comparable generalization performance as the SVR [11]. LS-SVR is the given name of the application of LS-SVM to regression [9,10].
![](https://i.imgur.com/a6RWNHa.png)
The weight vector (γ) as well as the width of the kernel (σ) are the only variables needed in LS-SVR [10].
# An Intelligent Model for Power Demand
The aim of this work is to synthesize a model to predict the hotel power consumption in a 24 h horizon. Instead of using only one model, the proposal is based on 24 hybrid models that calculate the consumption at each hour for the next day.
Figure 4 represents the inputs and outputs in a time scale. As can be observed, the output of the model is calculated with the previous 24 h values.
![](https://i.imgur.com/oioXQhD.png)
![](https://i.imgur.com/K0WB8K5.png)

As mentioned before, the model was trained with data of one year (electricity demand, occupancy, and mean daily temperature). The number of days used for training was 360. To validate the model, 5 days distributed along the whole year were used. The training procedure is divided in three phases:
1. Clustering training. This phase is the same for all the hybrid models, as they share the same inputs. 
2. Regression training. For each cluster, two different regression algorithms (MLP and SVR) were evaluated. In the case of ANN, different internal configurations were considered. 
3. Performance calculation. As the number of clusters for each model is not a predefined, it is necessary to calculate the best cluster assignment based on the achieved error.
As shown in Figure 3, each of the 24 submodels has a model selector to activate the local model according to the inputs. The model selector is based on the minimization of the Euclidean distance to each of the centroids of the clusters. In this way, the cluster with a lower distance to the input data is activated.



# Results and Discussion
A hybrid intelligent model was obtained using the data of a luxury hotel. The model has three inputs: the energy demand in the previous 24 h, the mean temperature of the previous day and the occupancy rate of the hotel. The model will provide hourly energy demand for the next 24 h. The training of the model was done using data from 360 days and the remaining days (5) were for validation purposes. These days were chosen to be distributed along the year: 31 December, 3 January, 11 August, and 29 September. The model is divided in 24 submodels, each one predicting the load in a specific hour of the next day. The setup of the 24 internal models used regression models based on LS-SVR or ANN. The ANN are MLP with one hidden layer of neurons and Tan-Sigmoid as the activation function. In the output layer, as the MLP is used for regression, the linear activation function is selected. The MLP training algorithm used was Levenberg–Marquardt. Up to 15 different configurations were considered for the ANN and LS-SVR. As commented, K-Fold cross validation was used to train the model. This procedure was repeated 16 times for each cluster.
The 24 h forecast of the five validation days using the hybrid model is shown in Figure 7 (solid line). It is remarkable how the forecast follows satisfactorily the real load curve. The study includes a comparison with an ARIMAX prediction method that is a technique for short-term load prediction. Also, a comparison with an advanced prediction method based on bagged decision trees is included. Both methods present an acceptable behavior with low error indexes. ARIMAX offers a higher error index while bagged decision tree, as expected, presents a much better mean square error. However, when compared to the hybrid proposal of this paper, as can be observed, the hybrid model overperforms clearly the ARIMAX proposal and performs better than the bagged decision tree method.
It is important to observe that the ARIMAX model is obtained for each prediction day using information from the previous 31 days. In this sense, the ARIMAX can have some adaptation according to the previous variations of the data. This implies that the real implementation of this method involves some online computational effort to obtain the model. In most cases this fact will not be relevant, but for some embedded applications with low cost equipment, it is necessary to take it into consideration. On the other hand, the hybrid intelligent model proposed is an explicit model that is completely obtained offline using data from the whole year. In this sense, the online computational load and storage is reduced
![](https://i.imgur.com/2IrXp7x.png)






# Research
1. Pieri, S.P.; Tzouvadakis, I.; Santamouris, M. Identifying energy consumption patterns in the Attica hotel sector using cluster analysis techniques with the aim of reducing hotels’ CO2 footprint. Energy Build. 2015, 94, 252–262. [CrossRef]
2. Deng, S.M.; Burnett, J. Study of energy performance of hotel buildings in Hong Kong. Energy Build. 2000, 31, 7–12. [CrossRef]
3. Priyadarsini, R.; Xuchao, W.; Eang, L.S. A study on energy performance of hotel buildings in Singapore. Energy Build. 2009, 41, 1319–1324. [CrossRef]
4. Cabello Eras, J.; Sousa Santos, V.; Sagastume Gutiérrez, A.; Guerra Plasencia, M.; Haeseldonckx, D.; Vandecasteele, C. Tools to improve forecasting and control of the electricity consumption in hotels. J. Clean. Prod. 2016, 137, 803–812. [CrossRef]
5. Hyndman, R.J.; Khandakar, Y. Automatic Time Series Forecasting: The forecast Package for R. J. Stat. Softw. 2008, 27, 1–22. [CrossRef]
6. Wang, X.; Smith, K.; Hyndman, R. Characteristic-Based Clustering for Time Series Data. Data Min. Knowl. Discov. 2006, 13, 335–364. [CrossRef]
7. Hyndman, R. Auto.Arima Function from Forescast v8.6 | R Documentation. Available online: https: //otexts.com/fpp2/arima-r.html (accessed on 5 May 2019).
8. Cristianini, N.; Shawe-Taylor, J. An Introduction to Support Vector Machines and Other kernel-Based Learning Methods; Cambridge University Press: New York, NY, USA, 2000.
9. Guo, Y.; Li, X.; Bai, G.; Ma, J. Time Series Prediction Method Based on LS-SVR with Modified Gaussian RBF. In Proceedings of the International Conference on Neural Information Processing, Lake Tahoe, NV, USA, 3–6 December 2012; pp. 9–17. [CrossRef]
10. Wang, L.; Wu, J. Neural network ensemble model using PPR and LS-SVR for stock et eorecasting. In International Conference on Intelligent Computing; Springer: Berlin/Heidelberg, Germany, 2012; pp. 1–8, doi:10.1007/978-3-642-24728-6_1
11. Steinwart, I.; Christmann, A. Support Vector Machines; Springer: Berlin/Heidelberg, Germany, 2008.