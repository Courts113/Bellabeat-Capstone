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

## Analyze

Since we do not know much about the user demographics, we can analyze the data to get an idea about key characteristics of our users.

### Average Steps per Day

![AVG Steps](https://user-images.githubusercontent.com/121068001/211927676-1e288c30-357a-4d3f-962b-49fc45be9a76.png)

Here we can see how many steps the users are taking on any average day. To find this information, the following query was used:

```
SELECT 
Id,
AVG(StepTotal) AS Average_Daily_Steps
FROM `capstone-project-370021.Fitbit_Data.dailySteps_v2`
GROUP BY Id
```

As we can see, average daily steps range from 1,247 - 16,807 steps per day. Average steps amongst all users was 9,011 steps per day.

### Average BMI amongst users

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

### Average Time Asleep per Night

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

