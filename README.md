**I.	INTRODUCTION**

The time series dataset consists of 1925 daily observations starting from 2015-11-23 until 2020-11-20. The dataset is sourced from Kaggle. It essentially consists of, as the title suggests, the daily closing stock prices. The models fitted are ARIMA, ETS, G/ARCH, Neural Networks, TBATS and Prophet. These models are then sorted with respect to their accuracy metrics and then the best model is selected. For the analysis, R programming language and its packages; TSA, forecast, tibble, urca, pdR, uroot, rugarch and prophet are used. The Neural Networks model is determined to be the best performing model with an RMSE score of 20.54975 and MAE score of 11.92680. Also, every model’s forecast plots were inspected, and Neural Networks model’s plot is also determined to be the best fitting and the most reasonably structure-following of them all. 

**II.	GENERAL DESCRIPTION OF THE DATA**

In this segment of the report, the data will be explored, and its structure and behavior will be identified. This includes the inspection of the time series plot of the data which shows the all-time values of closing stock prices, ACF & PACF graphs for further identifications through visual summarizations. 

 <img width="506" height="427" alt="image" src="https://github.com/user-attachments/assets/b76af2fb-54f8-42a1-8616-731205f11bc6" />

*Figure 1: Time Series Plot of the Daily Closing Stock Prices.*

This graph illustrates the most general and untouched structure of the dataset where x-axis is time and y-axis is closing stock prices. The graph shows that there is an increasing stochastic trend for the closing stock prices and possible existence of some anomalies during early-2019 and mid-2020. No observation can currently be made on the variances state.  

 <img width="511" height="431" alt="image" src="https://github.com/user-attachments/assets/942f5ffd-d1c9-4d80-8c01-0208d80f115d" />

*Figure 2: ACF and PACF Graphs of Closing Stock Prices*

These graphs of autocorrelation function and partial autocorrelation function of the time series data exhibit previously identified trend’s additional indicators. The slow decay on ACF graph means that there is non-stationarity in the dataset which should be assessed before any other analysis. In this part, the dataset is also split into train and test sets to validate results at the end.

**III.	CONFIRMATIVE DATA ANALYSIS**

During this phase of the analysis, the Kwiatkowski-Phillips-Schmidt-Shin (KPSS) test to address stationarity and the type of trend, Hylleberg-Engle-Granger-Yoo (HEGY) test to check the seasonal and regular unit roots were conducted.

A.	KPSS Test
KPSS test is a test which assesses a model’s stationarity and gives insights about what kind of trend it has if it has non-stationarity. The hypothesis for testing stationarity with KPSS is H0: Series is stationary and H1: Series is non-stationary, hypothesis of second part of KPSS test are also H0: There is deterministic trend H1: There is stochastic trend. The test results indicate that there exists non-stationarity with stochastic trend therefore differencing is necessarily done.

B.	HEGY test
This test is for identifying regular and seasonal unit roots. Its hypothesis is as follows, H0: Unit root exists H1: There is no unit root. Tests are conducted both for regular and seasonal roots. The results indicate that, after differencing, there exists no regular unit root and no seasonal unit root.

**IV.	ANOMALY DETECTION AND PREPROCESSING**

For this section of the report, anomalies in the series will be investigated to detect where they occur to smooth that said part out. Also, differencing will be concluded to make the series stationary.

A.	Anomaly Detection

<img width="505" height="361" alt="image" src="https://github.com/user-attachments/assets/713339b1-994d-4d8e-8ef9-06ed505a6618" />

*Figure 3: Anomalies and Their Removal.*

Figure 3 shows that there were anomalies, as suspected, on the extreme lows of the closing price graph which occurred during early-2019 and mid-2020. These anomalies are then cleaned to make the graph smoother and forecasts more accurate. These are all done with tsclean() algorithm from the forecast package. 

B.	Differencing and Stationarity

When there is stochastic trend in a non-stationarity time series, then it is necessary to take differencing which is done by subtracting observation t-1 from the observation t. 

<img width="508" height="363" alt="image" src="https://github.com/user-attachments/assets/01d9936e-faee-40e8-919f-e6671a56af78" />

*Figure 4: Plot of the Differenced Time Series Data*

As can be seen from figure 4, stationarity in mean has been achieved with differencing method and the stochastic trend has been dealt with. Although the series is now stationarity in mean, this does not necessarily indicate that it is also stationary in variance. In this specific case the data points are further apart as the time progresses so it can be said that there exists non-constant variance. An optimal lambda to make a box cox transformation to fix this problem is obtained and the resulting transformation does not change the data in a meaningful way, thus it is decided to not make a box-cox transformation. 

C.	Decomposition of the Time Series

The time series decomposition is a statistical technique that splits the time series into its core components which are trend for the upward or downward behavior of the series, seasonality for recurring cycles and error for the unexplained variability. 

 <img width="511" height="431" alt="image" src="https://github.com/user-attachments/assets/635987cc-f6fa-49a8-9ba4-0c3faa74710e" />

*Figure 5: Decomposition of the Time Series*

As said before, figure 5 includes the 3 components and the original plot of the series. It can be said here that there exists an upward trend before the differencing which is now assessed. It can also be seen that there are recurring cycles which has a yearly period. The residuals exhibit non-constant variance which is associated with the increasing cone-like shape on the data.

**V.	S/ARIMA & G/ARCH IDENTIFICATION AND MODELLING**

To select a model for S/ARIMA, it is needed to examine the ACF and PACF graphs to determine its parameters. Then, with suitable parameters, BIC scores should be examined to choose some of the best models possible. Also, diagnostics should be checked to identify any underlying problems.

A.	Model Selection

<img width="456" height="326" alt="image" src="https://github.com/user-attachments/assets/544ab2c7-deef-4958-84d9-fcdacc2d19a5" />

Figure 6: ACF and PACF Plots of the Differenced Series

The depicted graph above shows that the ACF and PACF both have break off points at lags 1, 2 and 4. The series is also stationary after one differencing which means the “d” parameter in ARIMA is 1. The ACF plot indicates the order of MA process while PACF shows the AR process of the series. In this case, AR and MA components can both take on the values 1, 2, 4. There exists no seasonal unit roots without a need for seasonal differencing, thus the better model for the time series is ARIMA rather than SARIMA. So, the models selected to be examined further are combinations of 0, 1, 2 and 4 for AR and MA components while “d” parameter is 1.

From these 25 combinations, 5 of them were selected to examine further because of their significant terms and comparatively low BIC values. Of these 5 models, ARIMA(4,1,0) has very low BIC value but has an insignificant term. So, this term is dropped with minimal effect to the other terms of it to obtain forecasts in the latter part of the analysis. Thus, the selected terms including the one with a dropped term are: (A) ARIMA(4,1,0) with BIC= 15795.77, (B) ARIMA(2,1,2) with BIC = 15799.74, (C) ARIMA(0,1,2) with BIC = 15794.35, (D) ARIMA(1,1,0) with BIC = 15797.06 and (E) ARIMA(2,1,0) with BIC = 15797.31.

B.	Diagnostics of ARIMA Models

To achieve meaningful results, there are three assumptions that should be met. They are normality, no serial autocorrelation and constant variance. These diagnostics can be checked with Jarque-Bera test, Box-Ljung test and ARCH test respectively. Jarque Bera Test results indicate that the normality assumption is not satisfied for any of the models, certain transformations are tried with no avail. Thus, as an assumption is not satisfied the results should be treaded carefully. From Box-Ljung tests, the residuals of (A) and (B) are determined to be uncorrelated while (C), (D) and (E) are correlated. This violation in assumption of no serial autocorrelation can be fixed by choosing different AR/MA terms as there is still information in the series with the current models of the three. It can also fix itself if a heteroscedasticity problem is also present and is fixed. Lastly, ARCH Engle's test values show every five model exhibits heteroscedasticity problem. This indicates that there exists non-constant variance problem which can be assessed with fitting a G/ARCH model or applying a box-cox transformation with an optimal lambda, but this application is already determined to be ineffective as it does not significantly affect the results.

<img width="338" height="174" alt="image" src="https://github.com/user-attachments/assets/7c48f6dc-6330-46e6-a3c6-46c68dc66c4b" />

*Figure 7: Table of Results from ARIMA Models*

In conclusion for the diagnostic tests for ARIMA models, mostly they are violated and interpretation and application of the results of the ARIMA models should be carefully made as they might have serious complications. Also, those models which don’t satisfy any of the assumptions are eliminated and it is decided that only (A) and (B) will be used to generate forecasts.

C.	G/ARCH

To fit an appropriate G/ARCH model, first a general GARCH model with default parameters is fitted to observe the direction to go towards. From the output of the default model, it is then determined that the possible best GARCH models to fit would be sGARCH, eGARCH, gjrGARCH or apARCH.

 <img width="508" height="363" alt="image" src="https://github.com/user-attachments/assets/05cc6098-dbbd-463c-ad8f-84de2301ce37" />

*Figure 8: ACF and PACF plots of Squared Residuals from (A)*

The above figure displays the squared residuals from (A)’s cutoff patterns which are similar to the other models (B) through (E). The cutoff points from the graphs are observed to be 8 and 6 for ACF and PACF respectively. Thus, GARCH order is determined as (6,8). After trying various models as mentioned above, apARCH(6,8) is deemed the best G/ARCH-type model with the lowest AIC which is 8.1497.

**VI.	FORECASTS OF THE PREVIOUS MODELS AND ETS, NEURAL NETWORKS, PROPHET AND TBATS MODELS**

A.	Forecasts of ARIMA Models (A) and (B)

First, 40 step forecasts are created from models (A) and (B). MSE values for these forecasts were checked and it is seen that they are 9506.161 and 9946.599 respectively.

<img width="513" height="366" alt="image" src="https://github.com/user-attachments/assets/1f38381a-2905-4570-9000-f6947ad2204f" />

*Figure 9: Residual Graphs of (A)*

  <img width="495" height="354" alt="image" src="https://github.com/user-attachments/assets/e2587124-4d23-4ee3-a624-bc2efb7db8de" />

*Figure 10: Residual Graphs of (B)*

Looking at the above residuals graphs it can be said that they are very similar for both models. They both exhibit cone-like structure which indicates non-stable variance, some value exceeding white noise bands and non-normal looking histogram. All this information indicates that assumptions are not satisfied and therefore results may be biased.

 <img width="509" height="364" alt="image" src="https://github.com/user-attachments/assets/d6ec5a2d-63ed-4452-8bde-e4ff4126318f" />

*Figure 11: Forecast from Model (A)*

 <img width="506" height="362" alt="image" src="https://github.com/user-attachments/assets/89484f35-03fd-40dc-81e2-1312b2ac4bf3" />

*Figure 12: Forecast from Model (B).*

Here it is seen from the above graphs that their forecast variances are very similar but mean values for the forecasts are slightly different. Model (A) more accurately represents the test data as it correctly predicted that the overall trend for the next 40 steps is going to be upwards while mode (B) failed to do so and predicted that it is going to be approximately the same with the last data point, thus, no trend.

B.	Forecasts of the apARCH Model

 <img width="505" height="360" alt="image" src="https://github.com/user-attachments/assets/2848ffb8-534c-4608-95b9-b0e2c73bc2c9" />

*Figure 13: Forecast from apARCH(6,8) Model*

40 step forecasts for previously constructed apARCH(6,8) model is then generated with MSE value of 9799.097 which is slightly greater than (A) and (B) models meaning it is slightly worse performing compared to those two models. 
From figure 3, it is seen that 95% confidence level intervals do not contain all the data from the test set and therefore this model is deemed unreliable as it fails to include every possible point from the test set in its intervals. It also fails to correctly predict the approximate trend of the time series as it exhibits mean forecast values to be downward sloping.

C.	Forecasts of the ETS Model

 The ETS (Error, Trend, Seasonality) model essentially breaks time series down to its error, trend and seasonality components to analyze and forecast the data based on their interactions. By also identifying whether the said components are additive or multiplicative it can make accurate predictions.
	The ETS model for Yahoo’s closing stock prices is determined to have an MSE score of 9963.425. These values deem this model to better than the apARCH model but slightly worse performing than the ARIMA models in terms of their MSE.

 <img width="508" height="363" alt="image" src="https://github.com/user-attachments/assets/553d4be9-ec8b-460d-9073-3c231e8fb24c" />

*Figure 14: Residual Graphs of the ETS Model*

 Checking the model diagnostics, it is seen that the cone-like shape is less visible than the previous models and the point are mostly in the white noise band. Also, the histogram is shaped like the normal distribution, but it is very dense near the mean values.

 <img width="506" height="362" alt="image" src="https://github.com/user-attachments/assets/eb604901-ed14-458d-8c1c-bf88447e7aff" />

*Figure 15: Forecast from the ETS Model*

 By looking at figure 15 the variance intervals are on point, they capture the real variance well as it includes the downward sloping part of the test data set. The mean of the forecast exhibits a constant behavior which is not the case on the test data set as while it is very chaotic, it still has an upward trend in general. Thus, in terms of how well the model fits to the structure as can be seen from the graph, this model outperforms the apARCH model, is almost identical to model (B), and slightly worse than model (A).
 
D.	Forecasts of the Neural Networks Model

The Neural Networks Model (nnetar) used in this analysis uses lagged values as input variables. By using grid search to adjust its hyperparameters, it is concluded that the best neural networks model is in the form NNAR(3,1,5)[365] with an MSE value of 8750.1 which is the lowest MSE among all models. It uses the last 3 time steps and 1 seasonal lag as inputs to make predictions. The neural network model also has 5 hidden neurons, allowing it to learn non-linear relationships in the data.

 <img width="505" height="361" alt="image" src="https://github.com/user-attachments/assets/4a91b124-4952-414c-9d44-6e27c8f0c1f2" />

*Figure 16: Residual Graphs of the Neural Networks Model*

As can be seen from the figure above, although it might not be an issue, the cone-like structure is still present in this model. Also, all points are in the white noise band and the residuals are almost normally distributed but they the density of middle values are much higher.

 <img width="521" height="372" alt="image" src="https://github.com/user-attachments/assets/a25528cf-4b51-4094-ae19-3cec613a0652" />

*Figure 17: Forecast from the Neural Networks Model*

From the figure above it is evident that the model performs marginally better than the other models in terms of how well it fits the structure of the data. The forecast very accurately predicts not only the general upward trend but the downward and upward local slope in the test data set.

E.	Forecasts of the Prophet Model

 The prophet model is a time series forecasting tool which similarly to ETS decomposes the time series into trend, seasonality and holidays in a robust and flexible way.

 <img width="514" height="367" alt="image" src="https://github.com/user-attachments/assets/160c43e5-c35d-4029-9b07-8b7245163ff7" />

*Figure 18: Residual Graphs of the Prophet Model*

 Residual graphs show the cone-like shape is not present and the points are mostly in the white noise band. Also, same case with the normality of the residuals again applies to this model as it is again structurally normal but denser towards the average.

 <img width="508" height="363" alt="image" src="https://github.com/user-attachments/assets/e9d8cd2b-531b-42ba-b20a-802ade0af3f7" />

*Figure 19: Forecast from the Prophet Model*

 The forecast graph generally seems on point as forecast is aligned with the real values, but an overfitting is not the case. It correctly predicts the slopes again but fails to do so locally.
 
F.	Forecasts of the Tbats Model

 The TBATS model is a time series forecasting method designed to handle complex seasonal patterns. It is generally a good time series model as it works well with time series that have multiple, irregular, or long seasonalities (e.g., daily data with weekly and yearly cycles). The constructed tbats model has an MSE of 9921.57.

 <img width="515" height="368" alt="image" src="https://github.com/user-attachments/assets/033c76f6-cd8e-4cd0-a47d-abd6fbec07d3" />

*Figure 20: Residual Graphs of the Tbats Model*

 The residual graphs on figure 20 shows that the cone-like shape is not present, almost all points are in the white noise band and the distribution of the residuals are very identical to normal distribution, but it has higher density for average values.

 <img width="507" height="362" alt="image" src="https://github.com/user-attachments/assets/bef72999-a742-40bc-9af8-2434fe73aa15" />

*Figure 21: Forecast from the Tbats Model*

 The illustration of the forecast as can be seen on figure 21 exhibits similar structure to the forecasts of models ETS and (B) as it has correctly predicted variance intervals which includes every point in the test dataset. But the mean forecast does not seem to be following the trend correctly thus it can be said that the forecast does not fit the structure of the data very well.
 
**VII.	CONCLUSION**

In conclusion, the time series data, Yahoo’s closing stock prices, is investigated in terms of its time plot and ACF/PACF plots to determine the orders. The data is split into train and test sets and the stabilization of the non-stationary status has been fixed by differencing. After investigating ACF/PACF’s again and checking the decomposition of the time series data, is then determined that the best values for AR and MA components would be 1, 2 or 4 for both. So, models were constructed, and it was decided that 2 of the 25 generated models were better than others so they are taken to the forecast phase. Also, certain algorithms such as apARCH, ETS, prophet, neural networks and tbats were used to generate different models and finally their forecast plots and MSE value were generated. After checking all the forecasts, it is then decided that the best fitting model for the Yahoo’s closing stock prices to be the neural networks mode which outperformed every other model in terms of its MSE which was marginally better than other models. Also, the observation of the plot of the forecast shows that it performs much better than other models as not only it fits in terms of its variance intervals, but it very accurately predicts the local downward and upward slopes as well as the general trend. 


Finally, the best model was determined to be the one with the smallest MSE and the highest R-squared which was the Random Forest model.
