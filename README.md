# Lending-Club
Lending_Club_Data
In the first part, I developed a number of time series models to understand the loans issued by LendingClub. The main tasks include visualizing data and developing statistical models to help LendingClub management understand better the changes in the characteristics of loans issued in NY over time. I developed models for the total dollar value of loans per capita, to guide LendingClub in its attempts to increase its market share in NY.

In the first part of the project, I will use two main data files: lendingClub.csv and nyEcon.csv. The first data file contains data for all the loans issued in the platform from June, 2007 to March, 2017. The data is aggregated to the state-month level. In peer-to-peer (P2P) lending platforms, consumers borrow from other consumers. The typical process is as follows: Consumers who are in need of borrowing money make a request by entering their personal information, including the SSN number, and the amount of money requested. If a request passes the initial checks, LendingClub’s algorithm assigns a grade to the request, which translates into an interest rate (the higher the grade, the lower the interest rate). Other consumers who would like to invest into personal loans lend the money. For the most part, the lending is automated, so the P2P lending model is different from crowdfunding models. nyEcon.csv includes some economic indicators for NY for the same timeframe (from June, 2007 to March, 2017). I joined this dataset with the original dataset to use the variables in my models. I also got the 2010 U.S. Census data for the population of each state (at the month level). Again, I joined this dataset.

Most business time series are not as good looking, or as some macroeconomic data. As you will see in the LendingClub data too, clear trends (incl. cycles) and seasonality may not exist. In the second part of this project, you will analyse the dataset: retail sales. The large drop in retail sales after the 2008 crisis created a challenge in making predictions using a model trained in the past data. Unfortunately, a similar drop in retail sales is pending due to COVID-19, making this problem most timely. In the second part of the project, I used retailSales.csv which includes U.S. retail sales from January, 1992 to February, 2020.

Data Dictionaries:
lendingClub.csv (All averages are the values averaged over the # of loans per state per month)
Variable -Definition

date-Monthly date

state-State abbreviation

Loans (avg and total)-The amount of loan issued in dollars

term (average)-The period in which the number of payments made are calculated (months)

intRate (average)-Interest rate on the loan (in percentages)

grade (average)-Loan grade assigned by the algorithm (A=1, B=2, C=3, D=4, E=5, F=6)

empLength (average)-Employment length of the borrower (in years)

annualInc (average)-The self-reported annual income provided by the borrower during registration

verifStatus (average)-Indicates if the income is verified by LendingClub (Verified=1, Not Verified=0)

homeOwner (average)-The home ownership status provided by the borrower during registration or obtained from the credit report (OWN=1, RENT OR OTHERWISE=0)

openAcc (average)-The number of open credit lines in the borrower's credit file

revolBal (average)-Total credit revolving balance

revolUtil (average)-Revolving line utilization rate, or the amount of credit the borrower is using relative to all available revolving credit-totalAcc (average)-The total number of credit lines currently open in the borrower's credit file

countOfLoans-The number of loans per month per state (tally taken during aggregation)

nyEcon.csv
Variable - Definition

date-Monthly date

NYCPI-Consumer price index in New York

NYUnemployment-Unemployment rate in New York -Seasonally adjusted

NYCondoPriceIdx-Condo price index in New York -Seasonally adjusted

NYSnapBenefits-Number of SNAP benefits recipients in New York

retailSales.csv
Variable - Definition

date-Monthly date

sales-U.S. retail sales in million dollars

usEcon.csv
Variable - Definition

date-Monthly date

income-Personal income (in billions of dollars) -Seasonally adjusted

unemployment-Unemployment rate -Seasonally adjusted

tenYearTreasury-10-Year treasury constant maturity minus 2-Year treasury constant maturity

CPI-Consumer price index

inflation-Inflation rate -Calculated from the consumer price index

vehicleSales-Total vehicle sale in the U.S. (in millions of units) -Seasonally adjusted

houseSales-New houses sold in the U.S. (in thousands) -Seasonally adjusted

Predicting/forecasting the LendingClub loans
Data processing
Load the LendingClub dataset into R and call it tsLCOrg. Convert the dataset into a tsibble using date as index and state as key. Hint: You might need lubridate’s help for this. Inspect and describe the data. I have used summary() to inspect the data. As we can see, currently, the date, state, avgLoans, totalLoans, avgTerm, avgIntRate, avgGrade, ,avgAnnualInc, avgVerifStatus, avgHomeOwner, avgRevolBal , countOfLoans, monthly_date don’t have any NA values

AvgEmpLength, avgRevolUtil, avgTotalAcc, avgOpenAcc have Na values of 2, 12,9, and 9 respectively.

Load the dataset with the NY Economy indicators.

Visit the U.S. Census Bureau’s data portal to download the population data for each state from the 2010 Census, and (i) add the population column to tsLCOrg. Then, (ii) calculate the loan amount per capita and add the new variable as loansPerCapita. (iii) Join it with the NY Economy data by date and state. Save the new tsibble as tsLC.

Exploratory analysis
Plot the loans per capita for the states within the top 10th percentile and bottom 10th percentile in terms of population. Compare the two plots and share observations. What might be a (statistical) reason for the difference in variance? The top ten percentile population states have low variability in the data across states because of a well distributed population due to higher sample size. Because of this, the loansPerCapita distribution seems to follow the same path. Conversely, the lower population means fewer data points, which would show the high variance that we are currently observing.

###Created anomaly plots to compare the NY data with Massachusetts and Colorado. Used the STL decomposition and interquartile range to mark the anomalies.

Apply STL decomposition to the loan per capita in NY.For the issued loans, identify/report the month in which the trend reverses.
The month in which the trend reversed seems to be at the beginning of 2016(Jan/Feb). What do you think is the reason for the change in trend in this month? The reason could be the increased Rate of interest by the Fed in December 2015.

Create a seasonal plot and a seasonal subseries plot for NY. Share your observations. Do your observations change if you limit the data to the last three years?
We see that there is a large jump in LoansPerCapita in the month of March 2016. There are also jumps in July and October of 2015 and 2014. In March, the financial year ends,which is why , we see a dip in March, while April shows an increase. Rest of the seasonality plots are pretty consistent except for a slight positive trend for data after 2012. Also the LPC has increased from 2012 to 2015 with an overall dip in 2016. Looking at the subseries graph, we can observe that the average mean ranges from 0.5 to 1. We observe that the data points fall in 2016 in all months except June. The small number of sales in March is probably due to incomplete counting at the time the data was collected. When we limit our data to the last 3 years, the subseries plot shows that the average mean ranges from 2-2.5.

Plot the autocorrelation function and partial autocorrelation function results for NY. What does the ACF plot tell you? What does the difference from the PCF plot tell?
We see an exponential plot which tells us that there is high autocorrelation between the non seasonal lags. It also shows a Spike at 12, meaning it shows seasonality. The PACF shows spikes in the first 3 lags, which means the first 3 lags are highly auto correlated. It also shows a single spike at 12 showing its seasonal autocorrelation.

Create a lag plot for NY for the lags 1, 5, 10, 15, 20, 25. Discuss your observations.
Here, the months of the variable on the vertical axis indicated by colours. The lines connect points in chronological order. The relationship is strongly positive at lags 1 and 15, reflecting the strong seasonality in the data. The plot for most months is centred. Plotting of peaks against troughs would show a negative relationship.

First, plot the loans per capita in NY over time. Then, create a fifth order moving average smoothing and plot the smoothed values on the actual loan data.
Modeling the loans issued in NY
Make a seasonal naive and drift forecast for NY data five years into the future, and display both models as visualizations. Discuss the results of these models. Do you think they capture the change in the amount of loans per capita? Why or why not?
The drift forecasting works by extrapolating the data from the beginning of time series till the period of forecast. While in the seasonal naïve forecast, the values are obtained by correlating that value to the last value of the same season Drift model slightly captures the change but not by too much. The forecast hence has a wider range. In this case the seasonality is of less significance as seen from the season plots.

Build a time series regression using both the time trend and seasons, as well as other variables you can use to explain the loan issued per capita. Discuss the results of the regression, and what you can learn from the statistically significant coefficients.
Hint: Note that you cannot use some of the variables to explain the loans per capita. Hint: You might also need to remove the variables with any missing values. I took variables that by domain knowledge struck to me as the most useful in this prediction. Also took out variables with missing values. This led to an R squared value of 90.86% and adjusted r squared value to be 89.52%. The variables with p value lower than critical value of 5% were trend and NYCondoPriceIdx. These satisfied the condition for rejecting the null hypothesis of the variable being non significant.

Plot the fitted values from the model above and an alternative model excluding the time trend and seasons. Compare two plots and discuss your observations.
The fitted values in the model with all the other variables from the previous model gave an R-squared value of 90.86. The second model after removing trend and season gave an R-squared value of 86.75. This tells us that the previous model performs better which is also indicated by the p-value of trend variable.

Create a predictive modeling plot using the model from (b) using two train/test splits. In the first split, use the data from 2014 and before for training, and in the second split, use the data from 2015 and before for training. Compare and discuss.
From the plots we can observe that the model using 2014 split is a better model because we can observe the predicted values align well with the actual values. Trend and seasonality is similar for the first model.

Check the residual diagnostics for the model from (b). Does it look fine? Discuss.
The time plot shows low variance which indicates evenly spread residuals. There is some variance in the residuals after 2015. The ACF shows a spike at lag 3. The histogram is mostly normal.

Build an ARIMA model using the same variables from (b) and using a grid search. What are the orders of the autoregressive model, differencing, and moving average model (p,d,q)? Which ones of the variables are significant? Are they the same as (b)?
The order of the autoregressive model, differencing, and moving average model are 3, 0, and 2 respectively. While order of seasonal autoregressive model, differencing, and moving average model are 0, 0 and 1 respectively. The significant variables are NYCPI, NYCondoPriceIdx and avgIntRate. This is because their standard error is low.

Check the differencing suggested by the KPSS test. Does it align with the ARIMA model’s differencing? Answer the next question (h) only if your response is negative.
The KPSS test differencing suggests differencing once whereas ARIMA shows 0.

If KPSS suggests a different degree for differencing, repeat the grid search in ARIMA using the degree suggested by the KPSS test. What is the (p,d,q) of the new model?
Compare the new model performance with the model from (f). What do you think is going on here? (Research and) discuss. Pro tip: You can run a constrained grid in ARIMA by presetting any of the parameters. The (p, d, q) of the new model is (0, 1, 4). The ARIMA() function did not find this model because it does not consider all possible models in its search. We can make it work harder by using the arguments stepwise=FALSE and approximation=FALSE.

Predictive modeling of the loans issued in NY
Set the seed to 333 and split the data into training (earlier than March, 2016) and test sets (on and after March, 2016). Build and compare the performance of the following models. Based on RMSE, which model is the best forecasting model? Time series regression with only trend and season Time series regression you built in 3(b) ARIMA grid search model without any parameters ARIMA grid search model you built in 3(f) The RMSE value for i) is 0.70, ii) is 0.64, iii) is 0.97 and iv) is 0.58. From this we can assume that model iv) performs the best forecasting when compared to the other models.

Set the seed to 333 and split the data differently this time: training set (before April, 2016) and test set (on and after April, 2016). Build and compare the performance of the same models. Based on RMSE, which model is the best forecasting model now?
The RMSE value for i) is 0.27, ii) is 0.60, iii) is 1.64 and iv) is 0.3. From this we can assume that model i) performs the best forecasting when compared to the other models.

The only difference between the two sets of models (a) vs. (b) is that the second one uses one more month of data for training. How do you explain the resulting change?
The RMSE for April 2016 split is lower compared to March 2016 split.

Part 2: Predicting/forecasting the U.S. retail sales
Preparation and exploration
Load the U.S. retail sales data into R and call it tsRetail.Convert the dataset into a tsibble using date as index.
Plot the retail sales over time for (i) the full data, and for (ii) a subset starting from 2010. Share your observations. The data is highly seasonal and we can see that there is an upwards trend as well. In the full dataset we notice that in 2008, due to the economic recession, we see that there is a dip in the graph.The data from 2010 onwards, we do not observe any anomalies, only the high seasonality and the upwards trend.

Understanding the time series
Create a seasonal, and a seasonal subseries plot for the subset data starting from 2015.Create an STL decomposition plot (i) for the full data, and (ii) for a subset of the data between 2005 and 2015 (both bounds are inclusive). Compare and discuss.
The variation bar for trend is smaller in the full dataset compared to the subset data which means that less variance is explained for the subset data. The variation bar for seasonality is smaller in the subset data compared to the full dataset which means that less variance is explained for the full dataset.

###Create an autocorrelation function plot and a partial autocorrelation function plot. What does the ACF plot tell you? What about the difference between the ACF and PCF plots? We see that the autocorrelation plot has gradually decaying spikes which shows a high trend in the data. Also, it has seasonal high spikes showing seasonality. The Pacf shows high spikes till the 4th lag, which tells us that it is autoregressive till lag 4. Plot the seasonally adjusted sales superimposed on the actual sales data. Use appropriate coloring to make both the seasonally adjusted and actual values visible.

###Create a second order moving average smoothing and plot the smoothed values on the actual sales data. Use appropriate coloring to make both the smoothed values and actual sales data visible. What would you change in the moving average plot to achieve a plot similar to the one you created in 2(d)? Apply the change and share the outcome. To change the moving average plot to achieve a similar plot to the one in 2(d), we can increase the order of the window length. Eg: I have used 7th order MA which produces a similar plot to the one in 2(d).

###Modeling and analysis of the time series

Build a time series regression using the time trend and seasons. Report your output, and provide a short discussion of the results (e.g.coefficients). Check the residual diagnostics.
Btw, isn’t that an impressive R-squared, achieved by using only the trend and seasons? The R squared value of 97.59 is impressive with just seasonality and trend.

Residual standard error: 14160 on 325 degrees of freedom Multiple R-squared: 0.9759, Adjusted R-squared: 0.975 F-statistic: 1098 on 12 and 325 DF, p-value: < 2.22e-16 The trend and seasonality for all the years except year2 are significant. The time plot shows some changing variation over time. This heteroskedasticity will potentially make the prediction interval coverage inaccurate. We can see in the histogram that the residuals are a bit skewed, which might affect coverage probability of the prediction intervals.

Build an ARIMA model. Report your output, and provide a short discussion of the results. Check the residual diagnostics.How do you think the ARIMA model compares with the regression from 3(a)? What do the coefficients tell you in this
This is ARIMA model (1,0,3)(0,1,2)[12] with drift Yt = 826.752 + 0.926Yt-1 - 0.479Et-1 - 01030Et-2 - 0.184 Et-3 - 0.338SEt-1 - 0.027 SEt-2 Et is white noise with std error = 6247 . The AIC, BIC values are AIC=6638.23 AICc=6638.69 BIC=6668.53. The variation has reduced in the time plot as compared to the previous model. The histogram appears to be more normal.

Run unit root tests to determine the amount of ordinary and seasonal differencing needed. Apply the suggested differencing, and run a KPSS test to check whether the KPSS test gives a pass on the stationarity of time series after the differencing applied. Finally, create two PACF plots for before vs. after differencing. Compare and discuss. Before: The test statistic is much bigger than the 1% critical value, indicating that the null hypothesis is rejected. That is, the data are not stationary. We can difference the data, and apply the test again. Because unitroot_nsdiffs() returns 1 (indicating one seasonal difference is required), we apply the unitroot_ndiffs() function to the seasonally differenced data. These functions suggest we should do both a seasonal difference and a first difference. After: doing seasonal differencing, the ndiff as well as nsdiff becomes 0 and the kpss statistic is much smaller- 0.1574261 which means our data has become stationary. Coming to the PACFs, before, the pacf shows correlation among the first 5 lags. There is also a decay in the seasonal lags. It has seasonal negative values. Whereas after differencing, the positive autocorrelation between lags is reduced till the 3rd lag

###Set the seed to 333 and split the dataset into a training set (before 2011), and a test set (2011 and after). Test and compare the ten-year forecasting performance of a time series regression with trend and season, and an ARIMA model that uses a grid search. Which one is the better model for forecasting retail sales? Why? The Rmse value is 14250.05 and MAE is 10815.03 in time series regression whereas for Arima it is 22136.18 and 17828 respectively. This shows that the time series regression has lesser error rate amongst the two and is therefore better.

###Set the seed to 333 and split the dataset into a training set (before 2016), and a test set (2016 and after). Test and compare the five-year forecasting performance of a time series regression with trend and season, and an ARIMA model that uses a grid search. Which one is the better model for forecasting retail sales now? Why? The RMSE value is 18692.32 and MAE is 14566.9 in time series regression whereas for Arima it is 76601.62 and 75350.7 respectively. This shows that the time series regression has a lesser error rate amongst the two. Still, the time regression is a better model.

If your answers are different in 3(d) and 3(e), how do you explain the difference?
My answers stays the same

4) Checking for anomalies and reporting the results
Run the anomaly detection algorithm GESD following the STL decomposition, as implemented in the anomalize library (using defaults). Report the plot and the list of observations marked as anomalies as a table. Is there an observation in the list that is different from others? If so, how do you explain the outlier in the list of anomalies?

Quite a few data points at 2009 and one data point in the recent data in 2019 are marked as anomalies.
For the models created in 3(d) and 3(e), create plots in which the actual values are shown against the predictions from the time series regression and ARIMA models. You will create two plots in total, and in each of the plots there will be three lines (actual data, predictions from the regression, predictions from the ARIMA). Use appropriate coloring to make the actual, regression, and ARIMA model lines distinguishable. In both plots, limit the portion of data visible in plots to the last ten years starting from 2010.
