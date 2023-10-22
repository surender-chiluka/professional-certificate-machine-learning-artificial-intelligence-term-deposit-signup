# Will the clinet Signup for a Term Deposit

[Jupyter Notebook](term-deposit-sign-up.ipynb)

The dataset is provider [at UCI Machine Learning repository](https://archive.ics.uci.edu/dataset/222/bank+marketing) 

## Business Understanding
Based on previous and the current phone campaign(s), identify if a customer will sign up for a term deposit.  
Decrease the rate of incorrectly identifying a client as not signing up for the term deposit. 

### Data Understanding
Features information as described by the dataset provider [at UCI Machine Learning repository](https://archive.ics.uci.edu/dataset/222/bank+marketing) 

1 - age (numeric)</br>
2 - job : type of job (categorical: "admin.","unknown","unemployed","management","housemaid","entrepreneur","student", "blue-collar","self-employed","retired","technician","services")</br>
3 - marital : marital status (categorical: "married","divorced","single"; note: "divorced" means divorced or widowed)</br>
4 - education (categorical: "unknown","secondary","primary","tertiary")</br>
5 - default: has credit in default? (binary: "yes","no")</br>
6 - balance: average yearly balance, in euros (numeric)</br>
7 - housing: has housing loan? (binary: "yes","no")</br>
8 - loan: has personal loan? (binary: "yes","no")</br>
9 - contact: contact communication type (categorical: "unknown","telephone","cellular")</br>
10 - day: last contact day of the month (numeric)</br>
11 - month: last contact month of year (categorical: "jan", "feb", "mar", ..., "nov", "dec")</br>
12 - duration: last contact duration, in seconds (numeric)</br>
13 - campaign: number of contacts performed during this campaign and for this client (numeric, includes last contact)</br>
14 - pdays: number of days that passed by after the client was last contacted from a previous campaign (numeric, -1 means client was not previously contacted)</br>
15 - previous: number of contacts performed before this campaign and for this client (numeric)</br>
16 - poutcome: outcome of the previous marketing campaign (categorical: "unknown","other","failure","success")</br>
17 - y - has the client subscribed a term deposit? (binary: "yes","no")</br>


### Data Analysis 
* Age Group                              - Clients between the age of above 60 are likely to open term deposit. Between Age 15 to 25 also seems more likely then compared to the age group 30 to 60
* Balance                                - Clients with Balance of 50 to 60 K are more likely to open the term deposit. Balance under -5000 and above 70000 are limited and could be outliers in the data 
* Last contact Day of the month          - This does not clearly indicate a day of the month has much difference. Week day of the month could if available could be of more useful.
* Call duration                          - Longer the call, more likely the client subscribed. Some outliers above 3000 seconds
* Current promotion campaign count       - As the call volume increasing.. the chance of a client subscribing is going down. There seems to be some outliers above 20 call but not sure those records are not use full 
* Number of days from previous contact   - More likely to subscribe from the 1 to 2 years of previous campaign. A lot less in the first 1 year with exception of around 1 qtr. 
* Previous contacts before this campaign - Above 40 seems to be outliers.. on further looking, anything above 30 seems to be outliers.. also not much of difference in the number of call from previous campaigns
* Job                                    - No clear indication just based on the Job
* Education and Martial status           - Does not seems to have a clear indication if the clients would open a term deposit
* Default                                - Data clearly indicates, if clients if they have defaulted then they are  less likely to open a term deposit
* House                                  - Data clearly indicates, if clients have a house they are less likely to open a term deposit
* Loan                                   - Data clearly indicates, like default and house, if clients have loans they are less likely to open a term deposit
* Contact type                           - While unknown contact type has a very low subscription rate, its not much off when compared to telephone or cellular contact type
* Month                                  - Data indicates that the % clients subscribing at higher rate when the number of calls made is less.. the months with under 1000 clients contacted has about 40% subscription rate. While in may which is by far the most has a very low subscription rate. Other factors infusing but definitely don't want to use this feature. 
*Previous out come                      - There is very data with know previous outcome.. may be not use this feature.. 

### Data preparation 

* Balance                         		- Drop data where the balance is greater than 70000 or negative balance of 5000 or greater
* Last contact Day of the month   		- Potential to be not be used for modeling but decided against dropping it initially and revisit if needed 
* Call duration							- Dropping data when the call duration is above 3500 seconds or the call time is 0
* Previous contacts before this campaign 	- Dropping data where the previous contacts are over 40
* Month                                  	- Dropping this feature 
* Previous out come                      	- Dropping this feature 



### Modeling

* One hot encoder is used for categorical features. 
* StandardScaler used for scaling the data
* GridSearchCV is used to identify the best estimator based on accuracy for each classifier

* KNN  hyper parameters 
  * n_neighbors: 1, 2, 3.... 23, 24, 25
  * weights: distance, uniform
* Linear Regression hyper parameters
  * max_iter: 100, 120, 140......280, 300
  * multi_class': One vs Rest, Multinomial
* Decision Tree hyper parameters
  * max depth: 6, 7, 8, 9, 10, 11, 12
  * min samples split: 2, 4, 6 ,8, 10, 0.05
  * criterion: gini, entropy, log_loss
  * min samples leaf: 2, 4, 6, 8, 10, 0.05
  * random state: 15, 20, 42
  * splitter: 'best','random'
* SVC hyper parameters
  * kernel: linear, sigmoid, rbf
  * gamma: 0.0001,0.001, 0.01, 0.1, 1.0

### Observations 
GridSearchCV was able to identified the best for each classifier that had accuracy of 0.89. 
Best estimator of Decision Tree Classifier had better precision and recall compared to best estimator of other classifiers. 
ROC Curves indicated that Drop of Rate of True Positive start at around the threshold of, 
 0.22 for KNN, Linear Regression and SVC and 0.38 for Decision Tree. 

By lowering the threshold, as identified by ROC curve, 
      KNN had the best recall and SVC had the worst recall
Further lowering the threshold of Decision to 0.22 (to match with other estimators)
    Recall was almost same as KNN but the Accuracy and Precision was much better.
KNN, Linear Regression were the fastest to Fit, Decision Tree took bit more time and SVC took lot more time. 

Based on these observations,  for this data I would use Decision Classifier with adjusted threshold of 22. 
The threshold can be adjusted based on the clients tolerance 

### Next steps 
Would run more analysis with KNN and Decision Classifier with additional hyper parameters. 
Also would explore not just with the best estimated estimator by GridSearchCV but would also use the top 5 or 10 estimators for each classifiers

On the technical side, I would like to organize the code by adding more common routines 
