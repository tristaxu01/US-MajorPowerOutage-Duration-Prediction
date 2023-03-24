### by Trista Xu and Tunan Li

Between January 2000 to July 2016, there were total of 1534 number power outrage that impacted at least 50,000 customers or caused an unplanned firm load loss of atleast 300 MW occured in United State. The largest power outage affected approximately 3.5 million people, and a maximum of 41,788 MW Peak Demand Power was lost during a single outage event. The focus of our website is to investigate the relationship between monthly electricity prices in the residential sector (labeled as "RES.PRICE" in the dataset) and power outages caused by intentional attacks. Specifically, we aim to determine whether regions with a top 25% residential electricity price are more likely to experience power outages caused by intentional attacks.

-----

# Framing the Problem 

### Prediction Problem

The aim of this project is to predict the duration of power outages in minutes, using previous major outage data from January 2000 to July 2016. This is a regression problem that will help local organizations and individuals take preventive measures to minimize losses before an unexpected power outage event occurs.

### Response Variable and Model Evaluation

Power outages can result in significant financial losses and negatively impact people's lives. To predict the quantitative data of the power outage duration in minutes, we will use external factors such as climate, geography, and local electricity information. To measure the ability of our model to predict quantitative data, we will use mean squared error. By doing so, we hope to provide accurate and useful information to help individuals and organizations prepare and prevent significant losses resulting from power outages.

### Feature Investigation

By the time of prediction, our team has thoroughly explored the data on major power outage events in the continental US and has collected basic information about each outage event, including location, occurrence time, regional information, etc. The data cleaning and investigation process has been documented on a [separate website](https://github.com/tristaxu01/Intentional-Power-Outage), which also explores the relationship between electricity prices and power outage causes. After the preparation process, we further explored the relationship between the outage duration and factors such as US state, state population, outage occurrence time, and climate region to improve the accuracy of our model.

# Baseline Model

### Feature Description

For the baseline model, our team selected four columns: "CLIMATE.REGION", "POPULATION", "MONTH", and "START.HOUR" extracted from the "OUTAGE.START (Y-M-D time)" column. During pipeline construction, we standardized the "POPULATION" column and one-hot encoded the rest of the features. Among the selected features, "POPULATION" is quantitative discrete data, "MONTH" and "START.HOUR" are quantitative ordinal data, and "CLIMATE.REGION" is categorical. We used one-hot encoding to transform "MONTH", "START.HOUR", and "CLIMATE.REGION" into quantitative nominal columns as model features.

### Feature Selection

We included the "POPULATION" column because greater population indicates urbanization and maturing infrastructure, making places with more population less likely to experience long power outages. 

* "POPULATION" column was included due to greater population indicating urbanization and mature infrastructure, which makes places with more population less likely to experience long power outages.
* "CLIMATE.REGION" was included as more certain climate regions are likely to experience extreme weather and longer power outages.
* "MONTH" feature is essential as different months correspond to national festivals and electric usage peaks.
* "Start hour" of the power outage was added to the model as outages starting at midnight may require more time to restore due to fewer resources available.

### Choice of Regressor

For the baseline model, we selected Random Forest Regressor because it can handle a large number of features, has lower overfitting risk compared to other models, and can model nonlinear relationships between features, which is useful for predicting outage duration. The algorithm also considers interactions between categorical and quantitative features, making it suitable for the diverse and complex data in this prediction problem. Overall, the Random Forest Regressor is a good choice for outage duration prediction due to its flexibility and ability to handle complex data.

### Performance Analysis

Based on the high RMSE value of around 5000, it seems that the current model is not accurate enough for practical use. One reason for this could be that the feature selection process was based solely on intuition, and the selected features may not be sufficiently informative to predict the duration of power outages. Additionally, the model's overall simplicity may not capture the complex relationships and interactions between the features and the outage duration. Therefore, it may be necessary to consider more advanced modeling techniques and more informative features to improve the model's accuracy.

# Final Model

### Feature Description

During the final model construction, we notice that there are outliers that falls far outside the normal range of y(outrage duration) column and this has hugely affect the slope of the fitting line. Our team concluded that those outliers has hugely affect our predeiction. Therefore, we decide to drop the upper 15% percentile of the data. This lowered our root mean square error from aroung 5000 to 1000. 

Then we perfrom the cross validation, to select best pipeline foe our final model. Accoding to the table We choose to add "U.S._STATE", "CAUSE.CATEGORY", "YEAR", "IND.CUST.PCT(%)", "COM.PRICE(cents / kilowatt-hour)","RES.CUST.PCT(%)". 

### Feature Selection

Upon further consideration, our team performed a groupby analysis on the duration data using several categorical features to observe differences in duration time across different groups. 

* We added a new column called "CAUSE.CATEGORY" using one-hot encoding, as different categories of outage causes can have varying durations (for example, system operability disruption versus public appeal) and the latter category typically takes longer to restore. 

  <iframe src="assets/cause_duration.html" width=800 height=600 frameBorder=0></iframe>

* "U.S._STATE" column as a one-hot encoded feature to account for geographic information and the varying degrees of urban construction during prediction.

  <iframe src="assets/state_duration.html" width=800 height=600 frameBorder=0></iframe>

* The "YEAR" column was also added as a categorical feature using OneHotEncoder to account for the progress in electricity network development over the years. Progressive social development leads to advancements in electricity network infrastructure, which can affect the outage duration time.

* We also added the anomaly level, which represents the oceanic El Niño/La Niña (ONI) index, to account for potential tsunamis or destruction to power stations that can lead to severe power outages.

* We included the "RES.CUST.PCT(%)" column, as areas with higher population density often have more power lines, transformers, and other electrical infrastructure that need to be maintained and repaired. This can make it more difficult and time-consuming to address power outages, especially if the outage affects multiple areas. The graph of "RES.CUST.PCT(%)" vs. outrage duration appeared to be parabolic, and therefore we performed a polynomial transformation. 

* However, we needed to discover the best degree of this polynomial transformation, so we performed cross-validation for this hyperparameter with degrees ranging from 1 to 11 and found that the best degree was one, which is the same as a simple linear regression.

* | Degree |  RMSE   |
  | :----: | :-----: |
  | Deg 1  | 1255.54 |
  | Deg 2  | 1260.03 |
  | Deg 3  | 1281.17 |
  | Deg 4  | 1276.57 |
  | Deg 5  | 2483.74 |
  | Deg 6  | 2445.5  |
  | Deg 7  | 2384.62 |
  | Deg 8  | 2325.18 |
  | Deg 9  | 2267.11 |

<iframe src="assets/no_outlier_RES_cust_plt.html" width=800 height=600 frameBorder=0></iframe>

*  Lastly, we choose to include the column IND.CUST.PCT(%), which the graph seems to be non-linear. According to the Tukley and Mosteller’s bulging rule, we use square root to our values on IND.CUST.PCT(%). The reson we include this feature is that the industrial places are likely to be a greater demand for electricity. If the power grid is not equipped to handle the demand, it can lead to strain on the system and potentially result in longer power outages.

### Choice of Regressor

For the baseline model, we selected Random Forest Regressor because it can handle a large number of features, has lower overfitting risk compared to other models, and can model nonlinear relationships between features, which is useful for predicting outage duration. The algorithm also considers interactions between categorical and quantitative features, making it suitable for the diverse and complex data in this prediction problem. Overall, the Random Forest Regressor is a good choice for outage duration prediction due to its flexibility and ability to handle complex data.

### Performance Analysis

Based on the high RMSE value of around 5000, it seems that the current model is not accurate enough for practical use. One reason for this could be that the feature selection process was based solely on intuition, and the selected features may not be sufficiently informative to predict the duration of power outages. Additionally, the model's overall simplicity may not capture the complex relationships and interactions between the features and the outage duration. Therefore, it may be necessary to consider more advanced modeling techniques and more informative features to improve the model's accuracy.


# Fairness Test

In this section, our team designed a fairness test to evaluate the performance of our model. We aimed to test if the model's predictions were biased towards areas with a greater urban area percentage than the national average. The test included two groups: X and Y. The X group consisted of outage events in states with a lower urban area percentage than the national average, while the Y group consisted of outage events in states with a higher urban area percentage than the national average.

We used the permutation test to investigate whether the model predictions had the same performance on the two different populations. In other words, we tested if the predicted outage events in areas with more urban area percentage and those in areas with less urban area percentage were from the same predicted population, and the difference between them was due to random chance.

#### Null Hypothesis: 

The performance of our model is not significantly different in predicting outage events in areas with more urban area percentage than the national average compared to areas with less urban area percentage than the national average.

#### Alternative Hypothesis: 

The performance of our model is significantly different in predicting outage events in areas with more urban area percentage than the national average compared to areas with less urban area percentage than the national average.

#### Test stistic: 

Absolute difference in root mean square between two groups

#### Significant level: 0.05

<iframe src="assets/fairness.html" width=800 height=600 frameBorder=0></iframe>

#### P value and Conclusion

The p-value of our test is 0.63, which is much higher than the significance level of 0.05. Therefore, we fail to reject the null hypothesis that our model yields a significant difference in higher urban percentage states and lower urban percentage states. Our model is likely to be fair in predicting outage events in different urban land percentage states.

