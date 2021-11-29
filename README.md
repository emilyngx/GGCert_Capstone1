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
* Source: Fitness Fitbit Tracker Data.
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
## Using R for exploration:
### Import libraries
```
library(tidyverse)
```
![alt text](https://github.com/emilyngx/coursera_ggcert_capstone1/blob/main/images/1.png)
### Import data
```
daily_activity <- read_csv("dailyActivity_merged.csv", show_col_types = FALSE)
daily_calories <- read_csv("dailyCalories_merged.csv", show_col_types = FALSE)
daily_intensities <- read_csv("dailyIntensities_merged.csv", show_col_types = FALSE)
daily_steps <- read_csv("dailySteps_merged.csv", show_col_types = FALSE)
sleep <- read_csv("sleepDay_merged.csv", show_col_types = FALSE)
weight <- read_csv("weightLogInfo_merged.csv", show_col_types = FALSE)
```
### Check DataFrame
```{r}
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

## Using SQL for Data Exploration & Cleaning
```SQL
/* A. EXPLORATION */
/* 1. CHECK UNIQUE IDS */
SELECT COUNT(DISTINCT Id)
FROM daily_activity;
/* 33 unique Ids */

SELECT COUNT(DISTINCT Id)
FROM daily_calories; 
/* 33 unique Ids */

SELECT COUNT(DISTINCT Id)
FROM daily_intensities;
/* 33 unique Ids */

SELECT COUNT(DISTINCT Id)
FROM daily_steps;
/* 33 unique Ids */

SELECT COUNT(DISTINCT Id)
FROM sleep;
/* 24 unique Ids */

SELECT COUNT(DISTINCT Id)
FROM weight;
/* 8 unique Ids */

/* 2. CHECK NULLS */
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
/* 1. Remove 3 duplicates from sleep table */
DELETE FROM
    sleep a
        USING sleep b
WHERE
    a.id = b.id
    AND a.sleepday = b.sleepday;
/* Check for duplicates again */
SELECT Id, SleepDay, COUNT(*)
FROM sleep
GROUP BY Id, SleepDay
HAVING COUNT(*) > 1;
/* None. Deleted duplicates in sleep table successfully */

/* 2. Join daily_activity, daily_calories, daily_intensities, and daily_steps tables */
/* Since they all have 33 unique ids and no null values, I will join them */
SELECT *
FROM daily_activity ac
INNER JOIN daily_calories ca
ON ac.id = ca.id AND ac.activitydate = ca.activityday
INNER JOIN daily_intensities i
ON ca.id = i.id AND ca.activityday = i.activityday
INNER JOIN daily_steps s
ON i.id = s.id AND i.activityday = s.activityday;

SELECT * 
FROM daily_activity
WHERE totalsteps = 0;
```


### Observation
* All 7 datasets have ID.
* The daily_activity, daily_calories, daily_intensities, and daily_steps have 940 observations.
* The date column type of each dataframe is character instead of date time.

### Cleaning
## Step 4: 
