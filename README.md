# Python_music_box_data_project

## Introduction

Music box is a music app similar to spotify. The dataset this project used contains song data and user behavior data between 2017/03/30 - 2017/5/12. Considering the enormous data(600K users, 14M-row play data), I first downsampled by user id to 10%. pyspark was heavily used throughout the project. There are two goals, the first part focused on churn prediction based on user behavior during the 6-week of time that the data span; the second part focused on music recommendation based on popularity in the last 7/30 days. 


## ETL

A couple features were generated:
(1)
Label: Use 2017-03-30 ~ 2017-04-28 data as the feature time window and check each user's activity during 2017-03-30 ~ 2017-04-28. If a user is active during this period, he/she is labeled 1, otherwise 0
(2)
Frequency features: For each event Play/Search/Download, count the number of event for the last 1, 3, 7, 14 and 30 days.
(3)
Recency features: Use end date of feature time window as the snapshot day, count the number of days from last event to the sanpshot day.
(4)
Profile feature: device_type: 1 if device is iPhone, 2 if otherwise.
(5)
Total play time features: Add up total play time for each user during the last 1, 3, 7, 14 and 30 days.
(6)
'Love' songs features: Count the number of songs played more than 85% of the song length for each user during the last 1, 3, 7, 14 and 30 days.

## Method & Results

### Churn Rate Prediction
sklearn package was used to fit models. I first defined metric scores(AUC) that were used to compare model performance. Fitted logistic regression, random forest, gradient boosting tree(GBT), and neural network models, and hand tuned paramters. GBT and random forest outperformed the other two, both test AUC exceeded 0.9. Performed grid search for both random forest and GBT models, and result didn't improve significantly. Ramdon forest yielded a slightly higher test AUC of 0.903553 with slight overfit (train AUC : 0.932353).

### Recommender System
I constructed utility matrix which contains implicit rating for each song/user pair based on the ratio of total play time of a song over the maxium play time of song for a given user. For each song, I calcuated the average rating and it was then used to calculate the recommendation score. Other factor used to calculate recommendation score are log of times a song being played/downloaded/searched during the last 7/30 days. The total of these four factors add up to recommendation score. 10 songs with highest recommendation score would be recommended to all users as 'Hot this week' and 'Hot this month'.


## Discussion & Future Work

Models used in churn rate prediction tends to have overfitting issues. Feature demension deduction can be introduced to improve model performance and accuracy.
When calculating recommendation score, rating and user behavior scores were assigned to equal weight. Further study can re-evaluate the weights for each factor.
