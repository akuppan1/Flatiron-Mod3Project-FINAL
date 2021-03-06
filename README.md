# Telco Customer Churn Analysis and Recommendations

## What this project is about:
  We were tasked to analyze a telecom company's customer data in order help them mitigate their customer churn rate.

## Summary
  We saw that there indeed was a pattern for churn. Customers who churned were usually people with month-to-month contracts and churned within the first 12 months of their contract. We based our recommendations on this observation as well as others found in the data. We utilized random forest decision trees to find feature importance with GridSearchCV tool to fine-tune the parameters of the model in order to optimize the features selected. 

## The Data
https://www.kaggle.com/blastchar/telco-customer-churn

This data is from the IBM business solution called "IBM Cognos Analytics"

Additional information about the dataset can be found [**here**](https://community.ibm.com/community/user/businessanalytics/blogs/steven-macko/2018/09/12/base-samples-for-ibm-cognos-analytics)


![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/IBM-Cognos-analytics-logo-540X280.jpg?raw=true)

From: https://senturus.com/wp-content/uploads/2016/05/IBM-Cognos-analytics-logo-540X280.jpg


## Agenda
1. Clean the data to prepare it for analysis
2. Analyze the most important features within the data 
3. Give business recommendations based on the analysis of the features


### 1. Cleaning the data

Our goal here is to make the data able to play nice with the classifiers/modules that we want to use.
I saved the final cleaned dataframe to a new modified csv file called: TelcoCustomerChurn[MODIFIED].csv
Decisions that we made with the data to prep it for the classifiers:

| Column | Change | Reason |
| ----- | ----- | ----- |
| Contract | 1) Month to month --> 0, 2) One Year --> 1, 3) Two year --> 3 | This is to turn string into numbers for the random forest.
| PaymentMethod | 1) Electronic Check/Bank Transfer(auto)/Credit Card(auto) --> 0, 2) Mailed Check --> HighFrictionPayment --> 1 | Redundant information. Reduced complexity : 0 is for LowFrictionPayment and 1 is for HighFrictionPayment|
| TotalCharges | Removed rows with blank values | The blank value rows were interfering with changing column type to float |  
| TotalCharges | Scaled it with MinMax Scaler formula | To make it work well with classifier | 
| MonthlyCharges | Scaled it with MinMax Scaler formula | To make it work well with classifier | 
| tenure | Scaled it with MinMax Scalar formula | To make it work well with classifier | 
| MultipleLines, InternetService, OnlineSecurity, OnlineBackup, DeviceProtection, TechSupport, StreamingTV, StreamingMovies | Switched to 1/0 | In order to simplify the data for the classifier, I changed any yes/no to 1/0. Also male/female to 1/0. I replaced some redundant "no" information as well. |
| 'Unnamed' Index | Dropped column | I had 2 index columns. Dropped the one I don't need |


### 2. Analyzing the most important features within the data

To find the features that stand out, I went with RandomForestDecisionTree classifier and used GridSearchCV in order to find the best parameters
Customized code for ROC curve from [this link](https://medium.com/all-things-ai/in-depth-parameter-tuning-for-random-forest-d67bb7e920d)
I used this code to lower the number of parameters to pass through GridSearchCV in order to not have my computer run forever on tens of millions of runs
Managed to get GridSearchCV runs down to 259200 fits by guessing param sizes with the ROC curve checks
* 'bootstrap': [True, False],
* 'max_depth': [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, None],
* 'max_features': ['auto', None],
* 'min_samples_leaf': [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
* 'min_samples_split': [2, 3, 4, 5, 6, 7, 8, 9, 10],
* 'n_estimators': [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15]

### Results from GridSearchCV:

{'bootstrap': True, <br/>
 'max_depth': 7, <br/>
 'max_features': 'auto', <br/>
 'min_samples_leaf': 7, <br/>
 'min_samples_split': 5, <br/>
 'n_estimators': 12} 

### 3. Analysis of features and recommendations
#### Feature Importances
After I ran the RandomForestDecisionTree classifier, I plotted the feature importances in descending order:
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature%20results%20final.JPG)
<br/>
##### ------------- CONTRACT TYPES --------------------------------------------
Analyzing the month-to-month contracts we see the following information below. We see that a good portion of the people who churn happen within the first year of maintaining a month-to-month contract.  <br/>
<br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/features_results_2.PNG)
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/features_results_3.PNG)
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/features_results_4.PNG)
<br/>
<br/>
We can compare churn in month-to-month with other contract types. Here are the 1-Year and 2-Year contracts. We see that the churn goes lower as the contract number goes higher. <br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/features_results_5.PNG)
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/features_results_6.PNG)
<br/>
<br/>
<br/>
##### --------------- TENURE ----------------------------------------------------
Analyzing tenure for customers that churned, we see that it confirms the Contract type analysis from above. We see a huge spike in tenure in the first 12 months.
<br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/tenure_churn_yes_pie.PNG)
<br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_results_bar_number_churned_vs_tenure_range.PNG)
<br/>
<br/>
We also see that tenure has many contracts with high month amounts where the customer does not churn.
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_results_bar_No-Churn_analysis.PNG)
<br/>
##### ------------------ MONTHLY CHARGES -----------------------------------------------------
Below we see the month-to-month pricing for churned customers. The spikes may indicate that there is a higher number of base-plan tier users, but we don't have enough information to be sure of that. <br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_results_bar_1monthtenure_vs_monthlycharges.PNG)
<br/>
We can compare that amongst non-churned customers with 72 month tenure below. Again, we don't have too much information but we can see there are certain price tiers and a good amount are on the lower price point. <br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_results_bar_72nochurn_vs_monthlycharges.PNG)
<br/>
<br/>
We see below number churned vs. Monthly Charges:
<br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_results_bar_YES-CHURN_vs_monthlycharges.PNG)
<br/>
We see below number NOT-churned vs. Monthly Charges:
<br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_results_bar_NO-CHURN_vs_monthlycharges.PNG)

##### -------------------TOTAL CHARGES --------------------------------------------------------- <br/>

We have a bar graph of different occurences of ranges of TotalCharges. We see that it also reflects that with the customers that churned, the total charges was much less. 
This indicates that the people left early and did not pay much. <br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/features_results_YES-CHURNED_vs_totalcharges.PNG)
<br/>
<br/>
We also see with people that did not churn that there are more of those who stayed longer. 
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/features_results_NO-CHURN_TotalCharges.PNG)
<br/>
<br/>
##### ------------------- INTERNET SERVICE -------------------------------------------------------<br/>

While this result, according to the importances chart, was the lowest. It was barely touching significance. We will still take a look at it. <br/>
<br/>
We see that among the the customers that did not churn, there seems to be a not too variable distribution of different values for internet service. <br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_results_fiber2_nochurn.JPG)
<br/>
<br/>
However, we see that amongst the customers that did churn, there was a large percentage that had fiber optic. We might be able to reach out to that fiber optice service provider for some kind of ad or marketing campaign for retention and extending contracts. <br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_results_fiber1.JPG)
<br/>
<br/>

##### ------------------- TECH SUPPORT -------------------------------------------------------<br/>
We saw that in the customers who churned. There was no tech support. <br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_techsupp_yeschurn.JPG)
<br/>
<br/>
We also saw customers who did not churn, they had tech support. <br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_techsupp_nochurn.JPG)
<br/>
<br/>
##### ------------------- ONLINE SECURITY -------------------------------------------------------<br/>
Similarily, we saw that in customers who churned, they had no online security. <br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_onlinesec_yeschurn.JPG)
<br/>
<br/>
Again, we see customers who did not churn had online security.
<br/>
![](https://github.com/akuppan1/Flatiron-Mod3Project-FINAL/blob/main/README%20Pics/feature_onlinesec_nochurn.JPG)

### 4. Recommendations

We saw that month-to-month contracts churned within the first 12 months. They did not pay well and most probably require manpower in order to handle the customer wanting to switch and leave our product. We also saw that churn decreased over time. As contract length increased, less people churned. We saw that for monthly charges amongst churned customers, there was a higher number of people churning at the higher price points. Customers who churned did not have much tech support or online security. Additionally, we saw that there was a predominance of fiber optics service provider for the internet service amongst the churned customers. <br/>
<br/>

Thus we give the following recommendations:
<br/>
1) Improve retention strategy for month-to-month contracts. Either push the customer to go for longer plans or get rid of month-to-month altogether
    - Future Work : How much cost is it to onboard a customer and how much money will we lose if we got rid of month-to-month contracts?
2) Contact the fiber-optics service providers of churned customers in order to push new marketing/ad campaigns
3) Price tiers for month-to-months? Why not simplify instead? Offer better terms to begin with so it will be more painful for them to churn. Other competitors will have to compete with us.
4) Speaking of offering better terms, make the longer term contracts more enticing. Encourage longer term contracts with sweet deals to the customers that will churn. 
5) Create a survery for churned customers to see what other facts are coming in to play outside of this data. We need more data!
6) Offer tech support if it means to save a churning customer.
7) Offer online security if it means to save the customer.

<br/> 
<br/>

### 5. Future Work
1) We need more data: Customer Churn Sentiment/Surveys, competitor price points on month-to-month contracts
2) Explore location data of churned vs. non-churned customers - are there geographical qualitative data that we are missing? Do people churn more in certain regions?
3) Time data - when are they churning and on what date? Is there some major event occuring?
4) Survey the long term clients to see why they stick with us. What programs are we offering that changed in the past 72 months? What can we offer new now to new customers?
5) Decision help for getting rid of month-to-month altogether: What is the cost of onboarding a month-to-month customer vs. Getting rid of the program altogether? 
