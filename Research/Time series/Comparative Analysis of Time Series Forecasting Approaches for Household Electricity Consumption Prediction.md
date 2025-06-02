

The most commonly methods used for time series forecasting are Multi-Layer Perceptron (MLP), Bayesian Neural Network (BNN), Radial Basis Functions (RBF), Generalized Regression Neural Networks (GRNN) also called kernel regression, KNearest Neighbor regression (KNN), CART regression trees (CART), Support Vector Regression (SVR), Gaussian Processes (GP) and recently invented Recurrent Neural Network (RNN) and Long Short-Term Memory (LSTM) models .


# Some Methods
### Vector Autoregression
Vector autoregression (VAR) is a stochastic process model used to capture the linear interdependencies among multiple time series. The VAR model extends the idea of univariate autoregression to k time series regressions, where the lagged values of all k series appear as regressors. VAR models generalize the univariate autoregressive model (AR model) such that in VAR we regress a vector of time series variables on lagged vectors of these variables [19]

# 3.2 Experiment process
### A. Experiments on Kaggle Dataset using Weka
The results from Weka which we will discuss in the next section show that the models rooted in statistics like SVM regression and Gaussian Processes regression performed better than other models therefore we decided to use statistical models for our python implementation. We determined that ARIMA would be a good model to predict univariate data and VAR would be a good option for multivariate data.


The main problem we faced was that the size of the energy consumption data we collected was very short (from 15th December 20193rd May 2020). This resulted in a sub-optional performance of developed models. To improve the accuracy of the model we tried to experiment on data with a longer interval, so we resampled data from a 15 minutes interval to 1 hour, 3 hours, 6 hours, 12 hours and daily interval.

# 4. Result and Comment on Performance
From the presented results it is clear that overall support vector regression provides the best performance regardless of the size of the test and training dataset
The next best performance is provided by models Gaussian Processes and Multilayer Perceptron.

For the forecasting with ARIMA, the datasets with 1 hour, 3 hours and 6 hours intervals were able to capture the trend. The VAR model required weather data to be merged with the energy consumption data and we didn’t have the weather data for May 2020 thus the dataset for VAR was even smaller than ARIMA however it was still able to somewhat capture the trend for a day ahead prediction. Overall, the performance of both ARIMA and VAR models was worse than Weka models. We believe that this is caused by the fact that the dataset we used to train the ARIMA and VAR models was much smaller.




# Next OBJ
Our next objectives are to: 1) Integrate the models we implemented in python in an energy prediction platform implemented using Google Cloud Platform (GCP) so that we can get energy predictions in real time; and 2) Investigate use of RNN model for time series forecasting and compare performance with ARIMA and VAR models


# Next Read:
- Ahmed, Nesreen K., et al. “An Empirical Comparison of Machine Learning Models for Time Series Forecasting.” Econometric Reviews, vol. 29, no. 5-6, 2010, pp. 594–621., doi:10.1080/07474938.2010.481556.
- Z. Zang, Z. Li, Q. et. al., "Application of ARIMA and Markov Combination Model in Medium and Long Term Electricity Forecasting," 2019 IEEE 3rd International Electrical and Energy Conference (CIEEC) 2019, pp. 287-292.
- Frank, E., & Mark, A. (2016). Hall, and Ian H. Witten (2016). The WEKA Workbench. Online Appendix for" Data Mining: Practical Machine Learning Tools and Techniques.
- Schulz, Eric, et al. “A Tutorial on Gaussian Process Regression: Modelling, Exploring, and Exploiting Functions.” 2016, doi:10.1101/095190.
- “A Beginner's Guide to Multilayer Perceptrons (MLP).” Pathmind, pathmind.com/wiki/multilayer-perceptron.
- 