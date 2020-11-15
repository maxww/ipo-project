# **ORIE 4741 Project: IPO Success Prediction**
#### Group Members: Abby Pham (vp237), Charles Wu (cw725), Max Wu (mw734), Selena Kang (sk2426) 
<br>

### **1. Introduction:**
An Initial Public Offering (IPO) is the first sale of stocks issued by a company to the public. IPO stocks can be very volatile in the days immediately after an IPO. We will be considering the following criterion to evaluate the success of an IPO: the IPO stock is overpriced/underpriced at issuance, or in other words, yields an increase/decrease in the stock price for the first day. For this project, we are looking to investigate whether or not we could make an accurate prediction on the stock price after the first day of trading. We are also looking to identify the most reliable and effective predictors that can be used to predict the success of an IPO.

### **2. Data Descriptive Statistics and Visualizations:**

[![P1.jpg](https://i.postimg.cc/GpCLnJyD/P1.jpg)](https://postimg.cc/FYT5jSGF)

This figure shows the statistics of the closing prices over day ranges 0-261 for each company (shown for companies 0-4). From the description of the data above, we can see that some companies have prices that fluctuate with wide ranges. For example, company 16 has a standard deviation of 7003.72 while company 3729 has a standard deviation of 2.224712 * 10<sup>-14</sup>, nearly 0. Every company has different trends in their closing prices in the IPO. Here is a visual representation of their varying closing prices:

[![P2.jpg](https://i.postimg.cc/3wYTc8sC/P2.jpg)](https://postimg.cc/yDQtJHYk)

This histogram of the YearDifference shows the frequency of the number of years the company was private before appearing in the IPO. We can see that most were private for only a small number of years, mainly 0, before appearing in the IPO. There are few that remained public for a large number of years before going IPO. 

[![P4.jpg](https://i.postimg.cc/Qxv55P2r/P4.jpg)](https://postimg.cc/7fMfrBPB)

This is a histogram of the sectors that each company belongs to. There is a good variety of them all but the market is dominated by finance, healthcare and consumer services sector.  

[![P3.jpg](https://i.postimg.cc/pr2Cw72S/P3.jpg)](https://postimg.cc/qh52n16c)

### **3. Data Cleaning:**

The IPO dataset consists of 3762 rows and 1664 columns. Each row corresponds to a different company. Columns contain publically available information about each company including: 
- Ticker symbol 
- Day, month and year of IPO 
- Year the company was founded 
- Years between founding and IPO
- Number of employees 
- Business sector 
- Industry within the sector 
- Age and gender of the CEO 
- Age and gender of the president
- Stock price data from day 0 to day 261
- Financial data of the company after it went IPO
<!-- list end-->
Additionally, the dataset had to be cleaned to remove missing values.
<ol> 
<li> We first dropped all the columns that contain the financial data of the company post-IPO (which accounted for a large proportion of the dataset) because they would be unusable since we were trying to predict the company's stock price pre-IPO.
<li> We then dropped all rows that contained blank values. </li>
<li> Afterwards, we removed rows that contained unknown as the value for CEO/president gender. </li>
<li> Then, we dropped rows that had '-' as the value for number of employees. </li>
<li> Finally, we removed negative values from the YearDifference column. </li>
</ol>

After cleaning, we were left with 1147 rows and 279 columns, which we considered a sufficient amount of data to work on. We think it might not be wise to impute missing data in this situation because there is a great deal of randomness in the movement of the stock price and even though the stock price's movement of each company may be dependent on each other, the stock price itself is not, making it very difficult to impute missing values.     

### **4. Model Effectiveness**

In order to avoid overfitting/underfitting, we chose to use 5-fold cross validation to evaluate whether our models performed poorly on unseen data. 

Regarding metrics for regression, we used the R<sup>2</sup> value to assess the effectiveness of the model on test data. For classification, we decided to use the accuracy score.    

### **5. Feature Selection & Preliminary Analyses:**

#### a) Linear Regression with Feature Engineering:

Linear Regression was first attempted to predict a continuous value for the price of the post-IPO stock at closing of day 0. The features used were ‘Month’, ‘Day'’, ‘dayOfWeek’, ‘yearDifference’, ‘employees’, ‘CEOAge’, ‘PresidentAge’, ‘openDay0’, ‘CEOGender’, ‘PresidentGender’, ‘Industry’ and ‘Sector’. The predictor in this case would be the closing price of the stock at day 0. We chose these features because they were the only information that was available to the public pre-IPO.

For feature engineering, we chose to transform several nominal features. Since CEOGender, PresidentGender, Industry and Sector are categorical data, we used one-hot-encoding in order to include them in the linear model. Our resulting matrix therefore included 1147 rows and 141 columns.

Initial running of the linear regression model resulted in an R<sup>2</sup> value of -14.593315328876882 and the graph below.

Figure 1: Linear Regression Model
[![P5.jpg](https://i.postimg.cc/Jz5TMM94/P5.jpg)](https://postimg.cc/w135k8qS)

We decided to run cross-validation to confirm the results. 5 fold cross validation was performed in order to prevent overfitting, and the R<sup>2</sup> values we obtained for our prediction were [0.9428164374024275, 0.5304734058315419, -41.38543478397166, 0.9967644470907266, -0.8877929292159177]. As seen from our R<sup>2</sup> values, the model performs well in two folds, decently in one, and terribly in the last two. 

From the results above, it can be interpreted that regression might be too challenging with our current amount of data. This led us to think - would our model perform better if we only considered whether the price had gone up or down? We could therefore reframe the problem into one of classification, where we would try to predict an increase or decrease in the stock price post-opening rather than a continuous value. A new dataframe was thus created with the predictions and actual values, as well as the predicted and actual difference between open and close. This resulted in an accuracy score of approximately 60.58%. We tried re-running the model several times and noticed that the accuracy score consistently lied in the range of 60%-70%. We also noticed that a large portion of the values predicted were higher than the true values.

#### b) Random Forest Regression with Feature Engineering:

According to a research paper that was trying to predict the failures of IPO, random forest regression might perform better compared to linear regression in terms of predicting the closing price of the first day. We performed random forest regression with feature engineering with a very similar approach to linear regression. The initial run had an R<sup>2</sup> value of 0.8932314188939517, which looked very promising on paper. 

Figure 2: Random Forest Model
[![random-forest.png](https://i.postimg.cc/rFQCCHBX/random-forest.png)](https://postimg.cc/w7Rm9wx0)

However, after running 5-fold cross validation, we got R<sup>2</sup> values of [0.9999999975038855, -31.79573939717596, 0.9966313772775831, -15.481289453448529,  0.10929519824082756]. Results were therefore similar to the linear regression case. 

Afterwards, we also reframed the random forest to a classification problem of whether the price increased or decreased. The resultant accuracy score was approximately 98.26%. We tried re-running the model several times and noticed that the accuracy score was hovering around 98% every time. This result supported the research's claim that the random forest regression model could give greater accuracy than linear regression model.

### **6. Conclusion:**
For the latter half of the project, we are going to work on ranking the importance of the predictors for our model. We are also considering optimizing our model by using quantile regression loss function. Finally, in order to make use of the rest of the closing price data, we are looking into developing a strategy for portfolio allocation to optimize the returns after 1 year, in which our portfolio only contains IPO stocks. 
