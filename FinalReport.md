# **Final Report - IPO Success Prediction**
#### Group Members: Abby Pham (vp237), Charles Wu (cw725), Max Wu (mw734), Selena Kang (sk2426)
<br>

### **1. Introduction and Description of the Problem:**
An Initial Public Offering (IPO) is the first sale of stocks issued by a company to the public. IPO stocks can be very volatile in the days immediately after an IPO. For this project, we were trying to build several models to predict the success of IPOs according to our criterion: There was a strict increase in the stock price after the first day. The list of potential features was the little information available to the public pre-IPO, which included: month, day, day of the week, number of years since the company was founded, number of employees, whether or not it is a US company, the sector the company is in, the industry the company is in, the CEO's age, the CEO's gender, the President's age, the President's gender, the number of years the company had been operating before going IPO, and the offering price. We would be applying what we had learned in class into our project, including missing value imputation using Gaussian Copula, feature engineering, loss function minimization, regularization, and cross validation. The 5 models that we were going to implement were:

a) For continuous-value prediction: We would try to approach the problem in 2 ways for these 3 models: predicting real value increase/decrease and classifying whether or not there was an increase because it is very hard to predict real continuous values considering the random nature of our chosen predictors and also stock price's movement.
1. Linear Regression, which is the most simple model and also a common one used in practice but is subject to outliers;
2. Random Forest, which is, according to a research paper linked below, a more accurate model to predict the success of an IPO;
3. Ridge Regression, which utilizes L2 loss function and L2 regularization;

b) For classification prediction:

4. Logistic Regression;
5. Support Vector Machine.


These 5 models would help us answer the following questions: How should we approach the problem of IPO Success Prediction? How successful can we be if we try to predict real value increases/decreases? In other words, on our best continuous-value prediction model, how far off are our predictions from the true increases/decreases? Should we use continuous-value prediction models but turn them into classification problems instead, or will traditional classification models help achieve lower misclassification rates?

As our final stretch, we did not want to waste the price data for the whole year and we also took inspiration from the 4741 Trend Trade Project. We would be using the data for our simple trading strategy. In addition, we would be using our findings and the model with the lowest misclassification rate to pick the stocks for our portfolio. In the end, we would check to see how well we did after 262 days of trading.

### **2. The Dataset:**

The IPO dataset consists of 3762 rows and 1664 columns. Each row corresponds to a different company. Columns contain publically available information about each company including: 
- Ticker symbol 
- Day, month and year of IPO 
- Year the company was founded 
- Years between founding and IPO
- Business sector 
- Industry within the sector
- Number of employees
- Age and gender of the CEO 
- Age and gender of the president
- Stock price data from day 0 to day 261
- Financial data of the company after it went IPO
<!-- list end-->

### **3. Feature Selection:**

The features used were ‘Month’, ‘Day'’, ‘dayOfWeek’, ‘yearDifference’, 'employees', ‘CEOAge’, ‘PresidentAge’, ‘openDay0’, ‘CEOGender’, ‘PresidentGender’, ‘Industry’ and ‘Sector’. We chose these features because they were the only information that was available to the public pre-IPO. According to an expert we consulted, IPO's stock price could be very cyclical, hence we chose to include features such as ‘Month’, ‘Day'’, ‘dayOfWeek’. We reckoned that investors might be interested in the size of the company, which could be reflected from the number of employees. We also thought that investors would be more eager to invest in the company if they knew that the company's CEO and President were of old age, which might indicate how experienced the management team was and how long they had been in the field. We thought that the CEO and President's genders might also be important since until the present day, women leaders are still not as highly regarded as male leaders. The industry and sector would also be crucial in predicting the stock price because investors would tend to be more bullish to some industries and sectors than others. Finally, the market might show a keener interest in companies that had been around for while before going IPO because there would be more information about the company on the news compared to newly founded startups, hence we chose ‘yearDifference’ as one of our features. Below is a coefficient matrix for ordinal and continuous features (after we imputed missing values). 

[![correlation-matrix.jpg](https://i.postimg.cc/TYQfssmt/correlation-matrix.jpg)](https://postimg.cc/GHBW8qXD)

Figure 1: Correlation Matrix

### **4. Data Descriptive Statistics and Visualizations:**

We generated some visualizations to understand more about some of our key predictors. 

This histogram of the YearDifference shows the frequency of the number of years the company was private before appearing in the IPO. We can see that most were private for only a small number of years, before going IPO. There are few that stayed private for a large number of years before going IPO. 

[![P4.jpg](https://i.postimg.cc/Qxv55P2r/P4.jpg)](https://postimg.cc/7fMfrBPB)

Figure 2: Histogram of Year Difference

This is a histogram of the sectors that each company belongs to. There is a good variety of them all but the market is dominated by finance, healthcare and consumer services sector.  

[![P3.jpg](https://i.postimg.cc/pr2Cw72S/P3.jpg)](https://postimg.cc/qh52n16c)

Figure 3: Histogram of Sectors

Below is the histogram which shows how cyclical IPOs can be. The number of IPOs peak in October and it looks like many companies chose to go IPO either in the middle of the month or at the very end of the month. Many companies also chose to wait until the end of the week for their IPOs. 

[![cyclical.jpg](https://i.postimg.cc/pyNRV4fg/cyclical.jpg)](https://postimg.cc/9wBvJxNp)

Figure 4: The timing of companies' IPOs

From the aforementioned predictors, we were trying to predict the Price Difference in the open price and close price of Day 0, and the distribution is as follows:

[![hist.jpg](https://i.postimg.cc/g2Tdn9kn/hist.jpg)](https://postimg.cc/HrXFhhMg)

Figure 5. Histogram of Price Difference

As seen from our histogram, the price difference is very small, only varying slightly from 0 to 1. None of the stock price went down after the first day; in fact, most of them actually went up. We conjecture that the dataset was not well-collected and the author of this dataset probably imputed the same price for the closing price of Day 0 if the stock price went down. This would later prove to be a challenge for us, as we had to deal with a dataset whose classes were imbalanced. Our solution to the problem could be referenced in part 7.

### **5. Data Cleaning:**
Below is our process of cleaning the dataset:
<ol> 
<li> We first dropped all the columns that contain the financial data of the company post-IPO (which accounted for a large proportion of the dataset) because they would be unusable since we were trying to predict the company's stock price pre-IPO.
<li> Missing value imputation technique: In order to get the most out of our data, we decided to impute missing values using Gaussian Copula for the columns that were of ordinal types, namely: Year, Month, Day, dayOfWeek, yearDifference, USACompany, YearFounded, CEOAge, CEOGender, PresidentAge, PresidentGender. 
<li> We then dropped all rows that contained blank values for the other columns. </li>
<li> Finally, we removed negative values from the YearDifference column because those data did not make sense since the company could not go IPO before it was founded. </li>
</ol>

### **6. Models Used:**

For feature engineering, we chose to transform several nominal features. Since CEOGender, PresidentGender, Industry and Sector are categorical data, we used one-hot-encoding in order to include them in the linear model.

We then randomly split our data into train set (70% of the data) and test set (30% of the data).

### a) <ins>Linear Regression</ins>

Linear Regression was first attempted to predict a continuous value for the increase/decrease of the post-IPO stock at closing of day 0.  

Initial running of the linear regression model resulted in an MAE of 339.7852468493644. 

[![download.png](https://i.postimg.cc/50hL9fPN/download.png)](https://postimg.cc/7GNfmr8p)

Figure 6: Linear Regression Model (Initial Run)

For the readers' convenience, we had drawn the line y=x in the graph. The points lying on the line would be the ones that had their values correctly predicted, above the line would be the ones that had their values overpredicted, and below the line would be the ones that had their values underpredicted. From the graph, we could see that most of the values were overpredicted.

From the results above, it can be interpreted that regression might be too challenging with our current amount of data. This led us to think - would our models perform better if we only considered whether the price had gone up or down? We could therefore reframe the problem into one of classification, where we would try to predict an increase or decrease in the stock price post-opening rather than a continuous value. 

A new dataframe was thus created with the predictions and actual values, as well as the predicted and actual difference between open and close. The result would be "correctly classified" if the product of the predicted value and the true value was greater than 0, meaning that the values moved in the same direction. This resulted in a misclassification rate of 0.5797101449275363. We also noticed that a large portion of the values predicted were higher than the true values.

### b) <ins>Random Forest</ins>

According to a research paper that was trying to predict the failures of IPO, random forest regression might perform better compared to linear regression in terms of predicting the closing price of the first day. We performed random forest regression with feature engineering with a very similar approach to linear regression. As for our parameters, we chose n_estimators (the number of trees in the forest) of 1000 and random_state of 42. The initial run had an MAE value of 49.10111234383177, which was a very good result compared to linear regression.

[![download-1.png](https://i.postimg.cc/8kLtFYLn/download-1.png)](https://postimg.cc/F1sj8DWj)

Figure 7: Random Forest Model (Initial Run)

However, if we instead reframed this problem as a classification problem for increases/decreases in price (as done above for Linear Regression), the results improved dramatically. The misclassification rate for such a problem was 0.2152133580705009, far lower than that of Linear Regression. Finally, the balanced accuracy score was 0.5.

### c) <ins>Ridge Regression</ins>

We also attempted Ridge Regression because it takes into account L2 regularization, which could help us prevent overfitting. We chose an alpha of 0.00001 for our parameter.

[![download-2.png](https://i.postimg.cc/L8q3kQrP/download-2.png)](https://postimg.cc/gxWhmscc)

Figure 8: Ridge Regression Model (Initial Run)

For the continuous value prediction problem, on its initial run, Ridge Regression produced a MAE of 231.9012842889374, which was lower than Linear Regression but higher than Random Forest.

Additionally, for classification, Ridge Regression had a misclassification rate of 0.5677179962894249. The balanced accuracy, on the other hand, was 0.5677179962894249. 

### d) <ins>Support Vector Machines</ins>

In order to further explore the classification problem, we decided to run SVM. SVM produced a misclassification rate of 0.20037105751391465 and a balanced accuracy score of 0.5 for initial run.

### e) <ins>Logistic Regression</ins>

Finally, we also used Logistic Regression. The logistic model had a misclassification rate of 0.20593692022263452 and a balanced accuracy score of 0.49998925839993125 for initial run. 

### **7. Testing for Model Effectiveness and Cross Validation**

Testing for efficiency and avoiding underfitting/overfitting: For the classification models, we used misclassification rate and balanced accuracy score as a means to test for our model's effectiveness. We chose to use balanced accuracy score here because our classes were imbalanced: for most of our data (around 80% of the stocks), the stock price increased after the first day. Balanced accuracy is a metric that one can use when evaluating how good a binary classifier is, especially when the classes are imbalanced, i.e. one of the two classes appears a lot more often than the other. Balanced accuracy is based on two more commonly used metrics: sensitivity (also known as true positive rate or recall) and specificity (also known as true negative rate, or 1 – false positive rate) and is calculated by taking the mean of the two. For the continuous-value prediction models, we used Mean Absolute Error (MAE) as a means to test for our model's effectiveness when predicting continous values, and misclassification rate and balanced accuracy score after we changed our problem into a classification problem. 

In order to avoid overfitting/underfitting, we would also be performing 5-fold cross validation and look at the MAEs across the board for the first 3 models and the misclassification rates and balanced accuracy scores for all 5 models.

Five fold cross validation was performed in order to prevent overfitting. The results for each model are summarized in the table below:

| Model | MAE | Misclassification Rate | Balanced Accuracy |
|-|-|-|-|
| Linear Regression | 19489.27 ± 19294.18 | 0.3931 ± 0.0303 | 0.4771 ± 0.0836 |
| Random Forest | 23746.62 ± 17738.97 | 0.2093 ± 0.0078 | 0.5 ± 0.0 |
| Ridge | 7910.02 ± 7734.85 | 0.3669 ± 0.0453 | 0.5062 ± 0.0456 |
| SVM | - | 0.2166 ± 0.0146 | 0.4996 ± 0.0004 |
| Logistic | - | 0.2205 ± 0.0302 | 0.4988 ± 0.0012 |
<br>

As can be observed from the above, the model that performed best for predicting continuous values was Ridge Regression, as average MAE was lowest. However, for the classification problem, the best performing model was Random Forest, with an average misclassification rate of 0.2093. From our MAEs, we could see that the discrepancy between the predicted values and the actual values varied greatly among the folds, which reflected the random nature of stock price and further reinforced that we should not use our models to predict continuous values. It should also be noted that even though most of our models performed better than chance (with misclassification rates lower than 50%), our balanced accuracy scores across all models were not too decent, only hovering around 0.5, which could be explained by the imbalance nature of the classes in our dataset.

### **8. Trading Strategy:**

Since we are risk-averse investors, we chose to pick 10 stocks with the lowest predicted increase/decrease according to our continuous-value prediction model with the lowest misclassification rate, which was the random forest model. We would only be buying and selling these 10 stocks from the beginning until the end of the 262 days. Because of the limited data we had, one assumption we were making was that by the time we start trading these stocks, it would be their first day going IPO. We would also ignore the transaction costs. We would allocate equal amount of money to each stock and we would be employing a simple long-short market neutral trading strategy as follows:

For each day, we would rank the previous day Sharpe ratio of an individual stock. We would long the 5 stocks that had the highest Sharpe ratios and short the 5 with the lowest Sharpe ratios. For the first day, since we did not have the data to calculate the standard deviation, we would be using the log returns as our ranking scheme instead.

The Sharpe Ratio can be calculated as follows:

[![Sharpe-Ratio.jpg](https://i.postimg.cc/nrD8BzVC/Sharpe-Ratio.jpg)](https://postimg.cc/JHMdWRkL)

We used log returns instead of raw returns in the formula because in finance, log returns tend to follow normal distribution. The risk-free rate is generally very low so we set it to 0 for simplicity.

Below are the log returns of the 10 stocks we picked:

[![stock.jpg](https://i.postimg.cc/HkYssybs/stock.jpg)](https://postimg.cc/vcKd0DPp)

Figure 9: Log Returns of Stocks

After executing our trading strategy, this is the performance of our portfolio's log returns after 262 trading days:

[![portfolio.jpg](https://i.postimg.cc/FFb4dKrf/portfolio.jpg)](https://postimg.cc/ThPSBfWG)

Figure 10: Log Returns of Portfolio

This is the statistics of our portfolio's performance, which does not look too bad: 

[![stock-stats.jpg](https://i.postimg.cc/pXqMC1nR/stock-stats.jpg)](https://postimg.cc/vcgKHhzN)

Figure 11: Statistics of Portfolio's Log Returns 

### **9. Discussion on Weapon of Math Destruction and Fairness:**

### a) <ins>Discussion on Weapon of Math Destruction</ins>

There is an important consideration for our model and its pertinence to being a weapon of math destruction. We believe that our models would not be a weapon of math destruction. 

Most of our current models have a success rate of over 50%, which by ours and industry standards are successful models. The features in our models are composed of information available to the public, which has the advantage that almost everyone can use and apply it in every IPO, but would also potentially mean that potential abuse and consequence would arise widely and drastically. However, having analyzed the circumstances surrounding IPOs and the fact that our models are based on a day 1 prediction, we have determined that our models would not be a weapon of math destruction. Our initial concern was because of the nebulous nature of companies during their IPO and general performance in the stock market, many abstract or singular factors can have a potent and wide ranging effect on investor behavior, further compounded by the impact that the actions of individuals can have a cascading effect on others. In the case of our models, take the example of a company whose traits and features, when put into our models, produce a favorable stock price prediction. Because of the ease of use for our models and the imprecise science of stock evaluation, many investors may look at the prediction and use that as the basis for their purchase of the stock. This compounds into a self-fulfilling prophecy wherein investors buy the stock based on the prediction, other investors purchase it based on the increased demand, sending the demand and subsequent price of the stock up, resulting in the model being validated, further inducing investors that had used the model to further invest in this proven prediction. This cycle works vis-a-vis a favourable prediction as well - investors seeing a negative prediction choosing not to invest, lowering anticipated demand, lowing price, causing the exit of other investors, thus validating the prediction. 

However, there are multiple factors that prevent this from transcending into a slippery slope of companies receiving investment when their features correlate with a positive prediction and the vice versa being true of companies with incompatible features. Because our models can only be used short term, any potential cascading effects would be cut off long before they can arise beyond the first day. In addition, owing to the infinite number of potential models and factors that could impact the IPO performance and investor confidence, our singular models would not be able to coercerce any big enough change and effect that would ultimately negatively impact the markets in any long term fashion or the fidelity and accuracy of our models.

### b) <ins>Discussion on Fairness</ins>

The issue of fairness was a concerning factor in our creation of the models. Owing to the complex and byzantine factors that are actually conducive to successful IPOs, which remain inaccessible and unknown to us, we can only utilize more tangible and direct features and elements from which to draw our features from. An initial consideration of our model was to remove the gender and age of the CEO from our model to protect it from bias, but having learned that the far-reaching effects of these sensitive factors would remain prevalent in our model spread amongst the other features in a labyrinthine fashion that would be immune to our understanding, it was decided that for a more faithful and accurate model we would leave such sensitive factors in. Our best performing model for misclassification problem, which is random forest, is based on a black-box method, so there is much difficulty in ascertaining the true effects of such bias on our results, which is understandable owing to the nebulous nature of stock evaluation. Should sensitive inputs and features be changed, the result would be affected in a significant way. However, regarding glass-box models such as SVM and Logistic Regression, they performed at a comparatively similar level to Random Forest, and are at the same time more intelligible and explainable. Glass box models such as these allow us to adjust for the bias in features such as CEO gender, and therefore permit for fairness in our models without losing too much accuracy.

### **9. Conclusion and Confidence in Results:**

Except for linear regression, most of our models had a misclassification rate of below 50%, which was better than chance. Our 5-fold cross validation results showed that ridge regression was the best performing model when we left the problem as a continuous value prediction problem, and random forest was the best performing model when we reframed it into a classification problem. 

Regarding the continuous value prediction problem, we are not very confident in our results. This is due to large MAE values coupled with a high discrepancy between the predicted and the actual price. However, once we reframed this problem as a classification problem, our models were far more effective, and we are confident in their performance. Three models (Random Forest, SVM, and Logistic Regression) produced consistently good results across five different folds, and we therefore believe that the consistently strong performance across all folds indicates that the performance observed was not a fluke. We would thus be willing to use our models in production and make decisions in a business setting. Nonetheless, we would also recommend further exploration of the subject matter.

As for our trading strategy, we did not take into account many real-life constraints (eg: transaction costs), so our results might not be too realistic. Nonetheless, hit serves as an actionable proof of concept and could definitely be modified to include these constraints. We could also optimize our strategy further by considering allocating different weights for our portfolio instead of allocating the same amount of money to all the stocks. 

### **10. References:**

[Predicting IPO Failures Using Machine Learning Technique](http://fmaconferences.org/SanDiego/Papers/PredictingIPOUnderpricingUsingMachineLearningTechnique.pdf)

[Developing a Basic Long-Short Trading Strategy](https://medium.com/@shankar1593/building-a-basic-long-short-trading-strategy-72a3ce9e8aa3)

[The Sharpe ratio explained](https://www.ig.com/au/trading-strategies/the-sharpe-ratio-explained-190117)

[Missing Value Imputation Using Gaussian Copula](https://github.com/udellgroup/online_mixed_gc_imp)

[4741 TrendTrade](https://github.com/Tongcheng/4741_TrendTrade/blob/master/final_report.md)
