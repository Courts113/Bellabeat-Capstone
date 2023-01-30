# CAPSTONE - Bellabeat Data Analytics 
Capstone Project for Google Analytics Course
## Scenario
Bellabeat, founded by Urška Sršen and Sando Mur, is a high-tech company that focuses on manufacturing smart products for women. These products collect data on sleep, reproductive health, stress, and activity to empower women with knowledge about their own health. Sršen knows that an analysis of Bellabeat’s available consumer data would reveal more opportunities for growth. She has asked the marketing analytics team to focus on a Bellabeat product and analyze smart device usage data in order to gain insight into how people are already using their smart devices. Then, using this information, she would like high-level recommendations for how these trends can inform Bellabeat marketing strategy. 
## Ask
### Key Stakeholders
+ Urška Sršen: Bellabeat’s cofounder and Chief Creative Officer 
+ Sando Mur: Mathematician and Bellabeat’s cofounder; key member of the Bellabeat executive team 
+ Bellabeat Executive Team
### Business Task
Analyze how consumers utilize non-Bellabeat smart devices and apply insignts to a Bellabeat product to develop a marketing strategy to promote company growth.
## Prepare
### Data used for analysis
> Furberg, R., Brinton, J., Keating, M., & Ortiz, A. (2016). Crowd-sourced Fitbit datasets 03.12.2016-05.12.2016 [Data set]. Zenodo. https://doi.org/10.5281/zenodo.53894
### Key Features of the Data
* 18 CSV files
* 33 eligible fitbit users responded to distributed surverys via Amazon Mechanical Turk between 03.12.2016-05.12.2016. 
* Data consists of personal tracker data including sleep monitoring, physical activity, and heart rate.
### ROCCC
* Reliability: Data was obtained from 33 eligible fitbit users via Amazon Mechanical Turk surverys.
* Original: Data was obtained from 33 eligible fitbit users via Amazon Mechanical Turk surverys.
* Comprehensive: Data includes minute level output of sleep monitoring, heart rate, and physical activity.
* Current: Data was obtained from 03.12.2016-05.12.2016
* Cited:Furberg, R., Brinton, J., Keating, M., & Ortiz, A. (2016). Crowd-sourced Fitbit datasets 03.12.2016-05.12.2016 [Data set]. Zenodo. https://doi.org/10.5281/zenodo.53894
### Limitations
* Data is not current
* Data was obtained from only 33 users which is too small to be representative of the population as a whole
* Unknown demographics of users
* Unknown selection process of users
* Data is not consistent. This was determined through the use of **DISTINCT Id** during queries of the sleep, physical activity, METs, and weight log tables. 
  + 33 users provided physical activity data
  + 27 users provided METs data
  + 24 users provided sleep data
  + 8 users provided weight log data
## Process
### During this capstone, SQL via Bigquery was used to clean and manipulate the data.  

#### Data tables used
* dailyActivity
* SleepDay
* WeightLogInfo
* hourlyCalories
* DailyMETs

Remove duplicate entries from all data tables

Clean data to remove entries that included zero steps per day on dailyActivity

```
SELECT  *
FROM `capstone-project-370021.Fitbit_Data.dailyActivity_v2`
WHERE TotalSteps<>0
```

Saved new table as *DailyActivity_v3*

Organize data by Id and then by date on dailyActivity

```
SELECT
*
FROM `capstone-project-370021.Fitbit_Data.DailyActivity_v3`
ORDER BY Id, ActivityDate
```

Saved new table as *DailyActivity_v4*

Clean data to remove *null* values from Sleep Table

```
SELECT *
FROM `capstone-project-370021.Fitbit_Data.SleepDay_merged`
WHERE
Id IS NOT NULL OR
SleepDay IS NOT NULL OR
TotalMinutesAsleep IS NOT NULL OR
TotalTimeInBed IS NOT NULL OR
TotalSleepRecords IS NOT NULL
```

Clean data to remove *zero calorie days* from Calorie Table

```
SELECT *
FROM `capstone-project-370021.Fitbit_Data.dailyCalories`
WHERE Calories<>0
```

Clean data to remove *zero METs days* from DailyMETs Table

```
SELECT *
FROM `capstone-project-370021.Fitbit_Data.minuteMETsNarrow`
WHERE METs<>0
```

## Analyze/Share

### Get to know our users

Since we do not know much about the user demographics, we can analyze the data to get an idea about key characteristics of our users.

#### Average Steps per Day by ID

![AVG Steps](https://user-images.githubusercontent.com/121068001/211927676-1e288c30-357a-4d3f-962b-49fc45be9a76.png)

Here we can see how many steps the users are taking on any average day. To find this information, the following query was used:

```
SELECT 
Id,
AVG(StepTotal) AS Average_Daily_Steps
FROM `capstone-project-370021.Fitbit_Data.dailySteps_v2`
GROUP BY Id
```

As we can see, average daily steps range from 1,247 - 16,807 steps per day. Average steps out of all users was 9,011 steps per day.

#### Average BMI amongst Users

![AVG BMI](https://user-images.githubusercontent.com/121068001/211932698-22ad32f5-8cfa-4ba7-af51-a37d41ce7955.png)

In order to get a snapshot of average BMI logged by users, the following query was used:

```
SELECT 
Id,
AVG(BMI) AS average_BMI
FROM `capstone-project-370021.Fitbit_Data.weightLogInfo_v2`
GROUP BY Id
```

As the above graph shows, only 8 of the 33 users tracked their BMI through their activity tracker. 

![Breakdown of BMI](https://user-images.githubusercontent.com/121068001/213311271-2ec537e1-bff4-492e-a5b7-153111a3b81a.png)

Here is another visualization of BMI broken down into the following categories: underweight, healthy weaight, overweight, and obese as defined by the CDC.  The following query was used to gather this data:
```
SELECT
CASE
WHEN BMI < 18.5 THEN 'Underweight'
WHEN BMI BETWEEN 18.5 AND 24.9 THEN 'Healthy_weight'
WHEN BMI BETWEEN 25.0 AND 29.9 THEN 'Overweight'
WHEN BMI > 30.0 THEN 'Obese'
END AS BMI_Categories
FROM `capstone-project-370021.Fitbit_Data.AVG_BMI`
GROUP BY BMI
```

#### Average Time Asleep per Night

![AVG Total Sleep](https://user-images.githubusercontent.com/121068001/211933945-c89a92dd-5c7e-4bae-8c12-42a4399cf38e.png)

Here is a quick graph visualization of the amount of sleep each user averages per night.  As noted prior, only 24 of 33 users tracked their sleep through their activity tracker.

The following query was used to find these nightly averages:

```
SELECT 
Id,
AVG(TotalMinutesAsleep) AS average_minutes_asleep,
AVG(TotalTimeInBed) AS average_time_in_bed
FROM `capstone-project-370021.Fitbit_Data.SleepDay_merged_v2`
GROUP BY Id

```

### Variable Relationships

Let's take a further look into varying relationships among variables

#### Total Steps vs. Total Calories

![Total Calories vs  Total Steps](https://user-images.githubusercontent.com/121068001/212410058-7434febe-2ac3-41b4-a172-91a7bcb571ec.png)

Here we can see a positive correlation between total steps and calories burned amongst users

#### Total Steps vs. Total Time Asleep

![Total Steps vs  Sleep Time](https://user-images.githubusercontent.com/121068001/213291998-e7996963-0dff-45ed-8e26-c72666a7da92.png)

As seen with this visualization, there does not appear to be a relationship between total steps per day and total amount of sleep at night.

### Activity Tracker Usage

#### Average steps of all users by weekday
![Average steps per day by all users](https://user-images.githubusercontent.com/121068001/213335930-ef74bf46-990d-47ac-aeb4-e13987f6ceeb.png)

According to this graph, we can see that users are most active on Tuesdays and Saturdays. Sunday appears to be the least active day for most users.

The following query was used to find this data:
```
SELECT  
weekday_name_abbreviated,
AVG(TotalSteps) AS Average_steps
FROM `capstone-project-370021.Fitbit_Data.dailyActivity_v5` 
GROUP BY weekday_name_abbreviated
```
#### Average activity level by weekday

```
SELECT  
weekday_name_abbreviated,
AVG(VeryActiveMinutes) AS veryActive,
AVG(FairlyActiveMinutes) AS fairlyActive,
AVG(LightlyActiveMinutes) AS LightlyActive,
AVG(SedentaryMinutes) AS Sedentary,
FROM `capstone-project-370021.Fitbit_Data.dailyActivity_v6` 
GROUP BY weekday_name_abbreviated
```

![Sheet 7](https://user-images.githubusercontent.com/121068001/215548474-e23c4074-d0c3-423b-a897-4189fabcde29.png)

Users were most sedentary Mondays and Fridays


#### Average steps per hour for all users
![Average steps per hour](https://user-images.githubusercontent.com/121068001/213343576-dd97bcef-1871-4ca3-a991-4d94f980e74d.png)

Users are most active through the hours of 8 AM and 7 PM with a lull in activity around 3 PM

This information was found via the following queries:

First, I had to change the data type from TIMEDATE to STRING in order to separate the date from time:
```
SELECT  
*,
CAST(ActivityHour AS string) AS NewDate
FROM `capstone-project-370021.Fitbit_Data.hourlySteps`
```

Then, I used the SUBSTR function to separate date and time

```
SELECT 
Id,
StepTotal,
ActivityHour,
SUBSTR(NewDate, 12, 8)  AS Time
FROM `capstone-project-370021.Fitbit_Data.hourlySteps_v2` 
```
Now to find the average steps per time

```
SELECT  
Time,
AVG(StepTotal) AS Average_steps
FROM `capstone-project-370021.Fitbit_Data.hourlySteps_v3` 
GROUP BY TIME
```

## ACT

### Summary Findings:

* Users take the most steps on Tuesdays and Saturdays
* Users have the most sedentary minutes on Mondays and Fridays
* Users are most active between the hours of 8 am and 7 pm with a lull in activity around 3 PM
* Users average ~15.9 hours of sedentary minutes per day


### Marketing Strategy
* Utilize reminders every hour for users to get up and move
* Incorporate a bedtime campaign involving a reminder to "wind down for bed" 
* Promote reward or point system for activity milestones 
