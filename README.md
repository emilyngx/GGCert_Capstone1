# Bellabeat Case Study
## Introduction
This is the final project for the Google Data Analytics Certification.
## Step 1: Ask
### Business Task
* Analyze smart device usage data in order to gain insight into how consumers use non-Bellabeat smart devices (Fitbit) and apply the insights into one of Bellabeat products.

* Guiding questions:

1. What are some trends in smart device usage?
2. How could these trends apply to Bellabeat customers?
3. How could these trends help influence Bellabeat marketing strategy?

### Key Stakeholders
* Urška Sršen: Bellabeat’s cofounder and Chief Creative Officer.
* Sando Mur: Mathematician and Bellabeat’s cofounder; key member of the Bellabeat executive team.
* Bellabeat marketing analytics team: A team of data analysts responsible for collecting, analyzing, and reporting data that helps guide Bellabeat’s marketing strategy.

## Step 2: Prepare
### Data
* Source: [Fitness Fitbit Tracker Data.](https://www.kaggle.com/arashnic/fitbit)
* These datasets were generated by respondents to a distributed survey via Amazon Mechanical Turk between 04.12.2016 - 05.12.2016. There are 18 CSV files.
* Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring.
* Data is stored in "data" folder.
### Limitations
* The data is not representative of all Fitbit users. There are only 30 users.
* There is no information indicating the user’s sex. This is not helpful for Bellabeat since their target customer is woman.
* The data is old. It was collected 5 years ago.
* There is not enough information to examine the credibility and biases of the data since it was collected by a third party.
* There are missing values.
* Variation between output represents use of different types of Fitbit trackers and individual tracking behaviors / preferences.

## Step 3: Process
## 3.1. Using R for Data Exploration:
### Import libraries
```R
library(tidyverse)
```
![alt text](https://github.com/emilyngx/coursera_ggcert_capstone1/blob/main/images/0.png)
### Import data
```R
daily_activity <- read_csv("dailyActivity_merged.csv", show_col_types = FALSE)
daily_calories <- read_csv("dailyCalories_merged.csv", show_col_types = FALSE)
daily_intensities <- read_csv("dailyIntensities_merged.csv", show_col_types = FALSE)
daily_steps <- read_csv("dailySteps_merged.csv", show_col_types = FALSE)
sleep <- read_csv("sleepDay_merged.csv", show_col_types = FALSE)
weight <- read_csv("weightLogInfo_merged.csv", show_col_types = FALSE)
```
### Check DataFrame
```R
str(daily_activity)
```
![alt text](https://github.com/emilyngx/coursera_ggcert_capstone1/blob/main/images/2.jpg)
```
summary(daily_activity)
```
![alt text](https://github.com/emilyngx/coursera_ggcert_capstone1/blob/main/images/3.jpg)
```
str(daily_calories)
```
![alt text](https://github.com/emilyngx/coursera_ggcert_capstone1/blob/main/images/4.png)
```
summary(daily_calories)
```
![alt text](https://github.com/emilyngx/coursera_ggcert_capstone1/blob/main/images/5.png)
```
str(daily_intensities)
```
![alt text](https://github.com/emilyngx/coursera_ggcert_capstone1/blob/main/images/6.png)
```
summary(daily_intensities)
```
![alt text](https://github.com/emilyngx/coursera_ggcert_capstone1/blob/main/images/7.png)
```
str(daily_steps)
```
![alt text](https://github.com/emilyngx/coursera_ggcert_capstone1/blob/main/images/8.png)
```
summary(daily_steps)
```
![alt text](https://github.com/emilyngx/coursera_ggcert_capstone1/blob/main/images/9.png)
```
str(sleep)
```
![alt text](https://github.com/emilyngx/coursera_ggcert_capstone1/blob/main/images/10.png)
```
summary(sleep)
```
![alt text](https://github.com/emilyngx/coursera_ggcert_capstone1/blob/main/images/11.png)
```
str(weight)
```
![alt text](https://github.com/emilyngx/coursera_ggcert_capstone1/blob/main/images/12.png)
```
summary(weight)
```
![alt text](https://github.com/emilyngx/coursera_ggcert_capstone1/blob/main/images/13.png)

### Observation
* All 7 datasets have ID.
* The daily_activity, daily_calories, daily_intensities, and daily_steps have 940 observations.
* The date column type of each dataframe is character instead of date time.
* The minimum of totalsteps in daily_activity table is 0.

## 3.2. Using PostgreSQL for addtional Data Exploration & Data Cleaning:
```SQL
Alter all column names to lowercase

SELECT  'ALTER TABLE ' || quote_ident(c.table_schema) || '.'
  || quote_ident(c.table_name) || ' RENAME "' || c.column_name || '" TO ' || quote_ident(lower(c.column_name)) || ';' As ddlsql
  FROM information_schema.columns As c
  WHERE c.table_schema NOT IN('information_schema', 'pg_catalog') 
      AND c.column_name <> lower(c.column_name) 
  ORDER BY c.table_schema, c.table_name, c.column_name;

A. EXPLORATION
1. CHECK UNIQUE IDS
SELECT COUNT(DISTINCT Id)
FROM daily_activity;
33 unique Ids

SELECT COUNT(DISTINCT Id)
FROM daily_calories; 
33 unique Ids

SELECT COUNT(DISTINCT Id)
FROM daily_intensities;
33 unique Ids

SELECT COUNT(DISTINCT Id)
FROM daily_steps;
33 unique Ids

SELECT COUNT(DISTINCT Id)
FROM sleep;
24 unique Ids

SELECT COUNT(DISTINCT Id)
FROM weight;
8 unique Ids

2. CHECK NULLS
SELECT * 
FROM daily_activity 
WHERE NOT (daily_activity IS NOT NULL);
/* None */

SELECT * 
FROM daily_calories 
WHERE NOT (daily_calories IS NOT NULL);
/* None */

SELECT * 
FROM daily_intensities
WHERE NOT (daily_intensities IS NOT NULL);
/* None */

SELECT * 
FROM daily_steps
WHERE NOT (daily_steps IS NOT NULL);
/* None */

SELECT * 
FROM sleep
WHERE NOT (sleep IS NOT NULL);
/* None */

SELECT *
FROM weight
WHERE NOT (weight IS NOT NULL);
/* There are multiple null values in fat column */
SELECT *
FROM weight;
/* There are 67 rows in weight table */
SELECT COUNT(*)
FROM weight
WHERE NOT (weight IS NOT NULL);
/* There are 65 rows in total of 67 rows that have null values in fat column */

/* 3. CHECK CONSTRAINTS */
/* 3a. Date constrain: From 2016-04-12 to 2016-05-12 */
SELECT MIN(ActivityDate), MAX(ActivityDate)
FROM daily_activity;

SELECT MIN(ActivityDay), MAX(ActivityDay)
FROM daily_calories;

SELECT MIN(ActivityDay), MAX(ActivityDay)
FROM daily_intensities;

SELECT MIN(ActivityDay), MAX(ActivityDay)
FROM daily_steps;

SELECT MIN(SleepDay), MAX(SleepDay)
FROM sleep;

SELECT MIN(Date), MAX(Date)
FROM weight;
/* min 2016-04-12, max 2016-05-12 for all tables */

/* 3b. Id constraint: 10 characters */
SELECT Id
FROM daily_activity
WHERE LENGTH(CAST(Id AS Text)) <> 10;

SELECT Id
FROM daily_calories
WHERE LENGTH(CAST(Id AS Text)) <> 10;

SELECT Id
FROM daily_intensities
WHERE LENGTH(CAST(Id AS Text)) <> 10;

SELECT Id
FROM daily_steps
WHERE LENGTH(CAST(Id AS Text)) <> 10;

SELECT Id
FROM sleep
WHERE LENGTH(CAST(Id AS Text)) <> 10;

SELECT Id
FROM weight
WHERE LENGTH(CAST(Id AS Text)) <> 10;
/* No Id with less or more than 10 characters was found in all tables */

/* 4. CHECK DUPLICATES */
SELECT Id, ActivityDate, COUNT(*)
FROM daily_activity
GROUP BY Id, ActivityDate
HAVING COUNT(*) > 1;
/* None */

SELECT Id, ActivityDay, COUNT(*)
FROM daily_calories
GROUP BY Id, ActivityDay
HAVING COUNT(*) > 1;
/* None */

SELECT Id, ActivityDay, COUNT(*)
FROM daily_intensities
GROUP BY Id, ActivityDay
HAVING COUNT(*) > 1;
/* None */

SELECT Id, ActivityDay, COUNT(*)
FROM daily_steps
GROUP BY Id, ActivityDay
HAVING COUNT(*) > 1;
/* None */

SELECT Id, SleepDay, COUNT(*)
FROM sleep
GROUP BY Id, SleepDay
HAVING COUNT(*) > 1;
/* 3 duplicates found */

SELECT Id, Date, COUNT(*)
FROM weight
GROUP BY Id, Date
HAVING COUNT(*) > 1;
/* None */

/* B. CLEANING */
/* 1. Create new sleep table with only distinct values */
CREATE TABLE sleep1 AS SELECT DISTINCT * 
FROM sleep;
/* Check for duplicates on sleep table again */
SELECT Id, SleepDay, COUNT(*)
FROM sleep1
GROUP BY Id, SleepDay
HAVING COUNT(*) > 1;
/* None */
/* I saved the cleaned sleep table as "cleaned_sleep" for visualization on Tableau in the next step */

/* 2. Delete rows that have totalsteps = 0 on daily_activity table */
SELECT COUNT(*) 
FROM daily_activity
WHERE totalsteps = 0;
/* There are 77 rows that have 0 TotalSteps */
SELECT * 
FROM daily_activity
WHERE totalsteps = 0;
/* Looking at this table, I saw that all of the records also have totaldistance = 0 and most of them have sedentaryminutes = 1440 (or 24 hours). This could be the time when users charged their FitBit. */
/* I decided to delete all records with totalsteps = 0 in order to avoid misleading records */
DELETE FROM daily_activity
WHERE totalsteps = 0;
/* I saved the cleaned daily_activity table as "cleaned_daily_activity" for visualization on Tableau in the next step */

/* 3. Join daily_activity, daily_calories, daily_intensities, and daily_steps tables */
/* Since they all have 33 unique ids and no null values, I will join them */
SELECT *
FROM daily_activity ac
INNER JOIN daily_calories ca
ON ac.id = ca.id AND ac.activitydate = ca.activityday
INNER JOIN daily_intensities i
ON ca.id = i.id AND ca.activityday = i.activityday
INNER JOIN daily_steps s
ON i.id = s.id AND i.activityday = s.activityday;
```

## Step 4 & 5: Analyze & Share
<img src="https://github.com/emilyngx/GGCert_Capstone1/blob/main/images/14.png" width=50% height=50%>

Average hours asleep of all users during the week are around 7 hours per day.

![alt text](https://github.com/emilyngx/GGCert_Capstone1/blob/main/images/15.png)

Sedentary Minutes have the strongest linear relationship to the number of minutes asleep of users per weekday with an R-squared = 0.17. The data shows a clear trend of the activity level linked to better sleep quality.

<img src="https://github.com/emilyngx/GGCert_Capstone1/blob/main/images/16.png" width=50% height=50%>

Average Calories burned per day are around 2,361 calories. There is no significant difference between calories burned on workdays and weekends. The difference between the lowest and the highest average calories burned in this case is 166.60 calories. Jogging in 20 minutes can burn approximately 160 calories.

<img src="https://github.com/emilyngx/GGCert_Capstone1/blob/main/images/17.png" width=50% height=50%>

Average Total Steps per day are around 8,311 steps, which is less than the recommended daily steps of 10,000.

<img src="https://github.com/emilyngx/GGCert_Capstone1/blob/main/images/18.png" width=50% height=50%>

On average, the smart device users are spending 80% of their day in a sedentary state. 

![alt text](https://github.com/emilyngx/GGCert_Capstone1/blob/main/images/19.png)
Calories Burned have the strongest linear relationship to the very active distance of users per day with an R-squared = 0.23. 

## Step 5: Act
#### Recommendations
* Notify users to complete a quick action (e.g. walk 20 steps, stand up, jump 10 times,...) after being sedentary for too long to burn more calories and have better sleep -> Bellabeat helps users stay active and improve sleep quality
* Encourage users to log weight, water intake, food,... -> Bellabeat helps users be mindful of their health 
* Bellabeat helps users track habits and take control of them. Different people can focus on their own goals 
