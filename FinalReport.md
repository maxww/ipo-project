# **Final Report - IPO Success Prediction**
#### Group Members: Abby Pham (vp237), Charles Wu (cw725), Max Wu (mw734), Selena Kang (sk2426)
<br>

### **1. Introduction and Description of the Problem:**
For this project, we are trying to build several models to predict the success of IPOs according to our criterion: There is a strict increase in the stock price after the first day. The list of potential features is the very little information available to the public pre-IPO, which includes: month, day, day of the week, number of years since the company was founded, whether or not it is a US company, the sector the company is in, the industry the company is in, the CEO's age, the CEO's gender, the President's age, the President's gender, the number of years the company had been operating before going IPO, and the offering price. We will be applying what we have learned in class into our project, including missing value imputation using Gaussian Copula, feature engineering, loss function minimization, regularization, and cross validation. The 5 models that we are going to implement are:

a) For continuous-value prediction: We will try to approach the problem in 2 ways for these 3 models: predicting real value increase/decrease and classifying whether or not there is an increase because it is very hard to predict real continuous values considering the random nature of stock price's movement.
1. Linear Regression, which is the most simple model and also a common one used in practice but is subject to outliers;
2. Random Forest, which is, according to a research paper linked below, a more accurate model to predict the success of an IPO;
3. Ridge Regression, which utilizes L2 loss function and L2 regularization;
<br>

b) For classification prediction:

4. Logistic Regression;
5. Support Vector Machine.


These 5 models will help us answer the following questions: How should we approach the problem of IPO Success Prediction? How successful can we be if we try to predict real value increases/decreases? In other words, on our best real value prediction model, how far off are our predictions from the true increases/decreases? Should we use real value prediction models but turn them into classification problems instead, or will traditional classification models help achieve lower misclassification rates?

As our final stretch, we do not want to waste the price data for the whole year, so we will be using the data for portfolio allocation and trading strategy. In the end, we will check our daily log returns of the portfolio after 262 days of trading.

### **2. The Dataset:**

The IPO dataset consists of 3762 rows and 1664 columns. Each row corresponds to a different company. Columns contain publically available information about each company including: 
- Ticker symbol 
- Day, month and year of IPO 
- Year the company was founded 
- Years between founding and IPO
- Business sector 
- Industry within the sector 
- Age and gender of the CEO 
- Age and gender of the president
- Stock price data from day 0 to day 261
- Financial data of the company after it went IPO
<!-- list end-->

### **3. Feature Selection:**

The features used were ‘Month’, ‘Day'’, ‘dayOfWeek’, ‘yearDifference’, ‘CEOAge’, ‘PresidentAge’, ‘openDay0’, ‘CEOGender’, ‘PresidentGender’, ‘Industry’ and ‘Sector’. We chose these features because they were the only information that was available to the public pre-IPO. According to an expert we consulted, IPO's stock price could be very cyclical, hence we chose to include features such as ‘Month’, ‘Day'’, ‘dayOfWeek’. We also thought that investors would be more eager to invest in the company if they knew that the company's CEO and President were of old age, which might indicate how experienced the management team was and how long they had been in the field. We thought that the CEO and President's genders might also be important since until the present day, women leaders are still not as highly regarded as male leaders. The industry and sector would also be crucial in predicting the stock price because investors would tend to be more bullish to some industries and sectors than others. Finally, the market might show a keener interest in companies that had been around for while before going IPO because there would be more information about the company on the news compared to newly founded startups, hence we chose ‘yearDifference’ as one of our features. Below is a coefficient matrix for numerical features. From the matrix, we could see that most features yielded correlation coefficients different from 0.

### **4. Data Cleaning:**
Below is our process of cleaning the dataset:
<ol> 
<li> We first dropped all the columns that contain the financial data of the company post-IPO (which accounted for a large proportion of the dataset) because they would be unusable since we were trying to predict the company's stock price pre-IPO.
<li> In order to get the most out of our data, we decided to impute missing values using Gaussian Copula for the columns that are of ordinal types, namely: Year, Month, Day, dayOfWeek, yearDifference, USACompany, YearFounded, CEOAge, CEOGender, PresidentAge, PresidentGender. 
<li> We then dropped all rows that contained blank values for the other columns. </li>
<li> Finally, we removed negative values from the YearDifference column. </li>
</ol>

### **5. Models Used:**

### a) <ins>Linear Regression</ins>

Linear Regression was first attempted to predict a continuous value for the price of the post-IPO stock at closing of day 0. In this case, we were trying to predict the closing price of the stock at day 0. 

For feature engineering, we chose to transform several nominal features. Since CEOGender, PresidentGender, Industry and Sector are categorical data, we used one-hot-encoding in order to include them in the linear model. Our resulting matrix therefore included 1147 rows and 141 columns.

Initial running of the linear regression model resulted in an MAE of 339.7852468493644. 

[![download.png](https://i.postimg.cc/50hL9fPN/download.png)](https://postimg.cc/7GNfmr8p)
Figure 1: Linear Regression Model (Initial Run)

From the results above, it can be interpreted that regression might be too challenging with our current amount of data. This led us to think - would our model perform better if we only considered whether the price had gone up or down? We could therefore reframe the problem into one of classification, where we would try to predict an increase or decrease in the stock price post-opening rather than a continuous value. 

A new dataframe was thus created with the predictions and actual values, as well as the predicted and actual difference between open and close. This resulted in a misclassification rate of 0.5797101449275363. We also noticed that a large portion of the values predicted were higher than the true values.

### b) <ins>Random Forest</ins>

According to a research paper that was trying to predict the failures of IPO, random forest regression might perform better compared to linear regression in terms of predicting the closing price of the first day. We performed random forest regression with feature engineering with a very similar approach to linear regression. As for our parameters, we chose n_estimators (the number of trees in the forest) of 1000 and random_state of 42. However, the initial run had an MAE value of 49.10111234383177, which was a very good result compared to linear regression.

[![download-1.png](https://i.postimg.cc/8kLtFYLn/download-1.png)](https://postimg.cc/F1sj8DWj)
Figure 2: Random Forest Model (Initial Run)

However, if we instead re-frame this problem as a classification problem for increases/decreases in price (as done above for Linear Regression), the results improve dramatically. The misclassification rate for such a problem was 0.2152133580705009, far lower than that of Linear Regression. Finally, the balanced accuracy score was 0.5.

### c) <ins>Ridge Regression</ins>

We also attempted Ridge Regression. 

[![download-2.png](https://i.postimg.cc/L8q3kQrP/download-2.png)](https://postimg.cc/gxWhmscc)
Figure 3: Ridge Regression Model (Initial Run)

For the continuous value prediction problem, Ridge Regression produced a MAE of 231.9012842889374, which is lower than Linear Regression but higher than Random Forest.

Additionally, for classification, Ridge Regression had a misclassification rate of 0.5677179962894249. The balanced accuracy, on the other hand, was 0.5677179962894249. 

### d) <ins>Support Vector Machines</ins>

In order to further explore the classification problem, we decided to run SVM. SVM produced a misclassification rate of 0.20037105751391465 and a balanced accuracy score of 0.5.

### e) <ins>Logistic Regression</ins>

Finally, we also used Logistic Regression. The logistic model had a misclassification rate of 0.20593692022263452 and a balanced accuracy score of 0.49998925839993125. 

### **5. Testing for Model Effectiveness and Cross Validation**

Testing for efficiency and avoiding underfitting/overfitting: For the classification models, we used misclassification rate and balanced accuracy score as a means to test for our model's effectiveness. We chose to use balanced accuracy score here because our classes are imbalanced: for most of our data (around 80% of the stocks), the stock price increased after the first day. For the continuous-value prediction models, we used Mean Absolute Error (MAE) as a means to test for our model's effectiveness when predicting continous values, and misclassification rate and balanced accuracy score after we changed our problem into a classification problem. 

In order to avoid overfitting/underfitting, we would also be performing 5-fold cross validation and look at the MAEs across the board for the first 3 models and the misclassification rates and balanced accuracy scores for all 5 models.

Five fold cross validation was performed in order to prevent overfitting. The results for each model are summarized in the table below:

| Model | MAE | Misclassification Rate | Balanced Accuracy |
|-|-|-|-|
| Linear | 7227.41 ± 28151.27 | 0.3931 ± 0.0303 | 0.4771 ± 0.0836 |
| Random Forest | 7623.82 ± 30373.92 | 0.2093 ± 0.0078 | 0.5 ± 0.0 |
| Ridge | 3033.16 ± 11325.28 | 0.3669 ± 0.0453 | 0.5062 ± 0.0456 |
| SVM | - | 0.2166 ± 0.0146 | 0.4996 ± 0.0004 |
| Logistic | - | 0.2205 ± 0.0302 | 0.4988 ± 0.0012 |
<br>

As can be observed from the above, the model that performed best for predicting continuous values was Ridge Regression, as MAE was lowest. However, for the classification problem, the best performing model was Random Forest, with a misclassification rate of 0.2093.

### **6. Trading Strategy:**

Since we are risk-averse investors, we chose to pick 10 stocks with the lowest predicted increase/decrease according to our best real-value prediction model with the lowest misclassification rate, which was the random forest model. We will only be buying and selling these 10 stocks from the beginning till the end of the 262 days. Because of the limited data we have, one assumption we are making is that by the time we start trading these stocks, it will be their first day going IPO. We will allocate equal amount of money to each stock and we will be employing a simple long-short market neutral trading strategy as follows:

For each day, we will rank the previous day Sharpe ratio of an individual stock. We will long the 5 stocks that have the highest Sharpe ratios and shorting the 5 with the lowest Sharpe ratios. For the first day, since we do not have the data to calculate the standard deviation, we will be using the log returns as our ranking scheme instead.

The Sharpe Ratio can be calculates as follows:

[![Sharpe-Ratio.jpg](https://i.postimg.cc/VsGqxvkx/Sharpe-Ratio.jpg)](https://postimg.cc/YjFLgpgR)

We used log returns instead of raw returns in the formula because in finance, log returns tend to follow normal distribution. The risk-free rate is generally very low so We set it to 0 for simplicity.

Below are the log returns of the 10 stocks we picked:

[![portfolio.jpg](https://i.postimg.cc/prZDrVfF/portfolio.jpg)](https://postimg.cc/SYnXthtS)

Figure 6: Log Returns of Stocks

After executing our trading strategy, this is the performance of our portfolio's log returns after 262 trading days:

[![stock.jpg](https://i.postimg.cc/gcSjz6RK/stock.jpg)](https://postimg.cc/c630TCFK)

Figure 7: Log Returns of Portfolio

This is the statistics of our portfolio's performance: 

[![stock-stats.jpg](https://i.postimg.cc/85Y23DJL/stock-stats.jpg)](https://postimg.cc/phDchwBd)

Figure 8: Statistics of Portfolio's Log Returns

### **7. Discussion on Weapon of Math Destruction and Fairness:**

### a) <ins>Discussion on Weapon of Math Destruction</ins>

There is an important consideration for our model and its pertinence to being a weapon of math destruction. Our current model has a success rate of over 50%, which by ours and industry standards is a successful model. The features in our model are composed of information available to the public, which has the advantage that almost everyone can use and apply it in every IPO, but would also mean that potential abuse and consequence would arise widely and drastically. Because of the nebulous nature of companies during their IPO and general performance in the stock market, many abstract or singular factors can have a potent and wide ranging effect on investor behavior, further compounded by the impact that the actions of individuals can have a cascading effect on others. In the case of our model, take the example of a company whose traits and features, when put into our model, produces a favorable stock price prediction. Because of the ease of use for our model and the imprecise science of stock evaluation, many investors may look at the prediction and use that as the basis for their purchase of the stock. This compounds into a self-fulfilling prophecy wherein investors buy the stock based on the prediction, other investors purchase it based on the increased demand, sending the demand and subsequent price of the stock up, resulting in the model being validated, further inducing investors that had used the model to further invest in this proven prediction. This cycle works vis-a-vis a favourable prediction as well - investors seeing a negative prediction choosing not to invest, lowering anticipated demand, lowing price, causing the exit of other investors, thus validating the prediction. This can transcend into a slippery slope of companies receiving investment when their features correlate with a positive prediction and the vice versa being true of companies with incompatible features. While the model may work in the short run, the truly morpheus elements that make a successful IPO investment may be lost in this superficial understanding, and owing to this new outside variable result in a long term market failure of investing into badly performing companies and bankruptying potentially successful ones, which in addition to poor financial decisions would result in the eventual demise of the merits and fidelity of our original model.

### b) <ins>Discussion on Fairness</ins>

The issue of fairness was a concerning factor in our creation of the model. Owing to the complex and byzantine factors that are actually conducive to successful IPOs, which remain inaccessible and unknown to us, we can only utilize more tangible and direct features and elements from which to draw our features from. An initial consideration of our model was to remove the gender and age of the CEO from our model to protect it from bias, but having learned that the far-reaching effects of these sensitive factors would remain prevalent in our model spread amongst the other features in a labyrinthine fashion that would be immune to our understanding, it was decided that for a more faithful and accurate model we would leave such sensitive factors in. Our model is based on a black-box method, so there is much difficulty in ascertaining the true effects of such bias on our results, which is understandable owing to the nebulous nature of stock evaluation. In this case, our model is indeed an unfair model. Should sensitive inputs and features be changed the result would be affected in a significant way. However this unfairness remains truthful and conducive to the original intentions and objectives for which the model was originally purposed for.

### **8. Conclusion and Confidence in Results:**

Except for linear regression, most of our models had a misclassification rate of below 50%, which was better than chance. Our 5-fold cross validation results showed that ridge regression was the best performing model when we left the problem as a continuous value prediction problem, and random forest was the best performing model when we reframed it into a classification problem. However, balanced accuracy score for our random forest model was not high, only averaging 0.5. This is because of the imbalanced nature of our dataset as explained above in part 5. Therefore, we are not very confident with our results because the dataset we chose to analyze was not well-collected. Therefore, we would recommend further exploration of the subject matter before using our models and findings in production in an enterprise or business setting.

### **9. References:**

[Predicting IPO Failures Using Machine Learning Technique](http://fmaconferences.org/SanDiego/Papers/PredictingIPOUnderpricingUsingMachineLearningTechnique.pdf)

[Developing a Basic Long-Short Trading Strategy](https://medium.com/@shankar1593/building-a-basic-long-short-trading-strategy-72a3ce9e8aa3)

[The Sharpe ratio explained](https://www.ig.com/au/trading-strategies/the-sharpe-ratio-explained-190117)

[Missing Value Imputation Using Gaussian Copula](https://github.com/udellgroup/online_mixed_gc_imp)