# Google Data Analytics Professional Certificate - Capstone Project 1 - Cyclistic bike-share case study
## Ebenezer Kojo Frimpoong
## 01/22/24

---

## Analysis On  Bike Sharing Data Using SQL and Tableau

[](bike_share_pic)
![the-little-yellow-car-2652215_1280](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/35438ca2-395c-4388-899a-ac919f0528de)

## Project Overview
"Cyclistic Bike-Share Case Study" is a comprehensive analysis project focused on understanding and optimizing the bike-sharing service provided by Cyclistic. Cyclistic is a fictional bike-share company with a fleet of 5,824 bicycles that are geotracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station. Cyclistic offers single-ride, full-day, and annual membership plans. Customers who purchase annual memberships are referred to as "member" customers. Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual, single-ride or full-day users. 

This study involves an in-depth examination of user behavior, trip patterns, and various factors influencing bike usage. Utilizing data analytics tools such as SQL and Tableau, the goal of this analysis is to identify trends in the usage patterns of casual and member customers to help Cyclistic design marketing strategies aimed at converting casual users into annual members. The analysis encompasses diverse aspects, including user demographics, popular routes, peak usage times, and factors affecting ride duration. Through this project, valuable recommendations for business improvement and strategic decision-making are expected to emerge.

[](Dataset)
![Screenshot (9)](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/341ddb0d-0ef2-4963-8598-8f660e242047)

We will resolve this case following this methodology:

`ASK, PREPARE, PROCESS, ANALYSE, SHARE AND ACT`

---

## **ASK**
This phase involves defining the issue to be solved, identifying stakeholders and what their expectations from the project are.

This questions will guide the future marketing program:
1. How do annual members and casual riders use Cyclistic bikes differently?
     
      - How frequently do annual members and casual riders utilize Cyclistic bikes within a given time frame?
     
      - Are there specific days or times when annual members or casual riders are more active?
     
      - What are the most common starting and ending points for trips taken by annual members and casual riders?
     
      - Do annual members and casual riders prefer different types of bikes or routes?
     
      - Are there significant differences in the distances covered by annual members and casual riders?
     
      - How does weather or seasonal variations impact the usage patterns of annual members and casual riders?
     
      - Are there distinct preferences or behaviors in terms of bike usage during weekdays versus weekends for annual members and casual riders?
  
### Business Task
Analyze and leverage key drivers of sales performance, including product contribution to sales, seasonal sales patterns, and customer segmentation.

### Key Stakeholders
- **Lily Moreno**: The director of marketing and manager. Moreno is responsible for the development of campaigns and
initiatives to promote the bike-share program. These may include email, social media, and other channels.

- **Cyclistic marketing analytics team**: A team of data analysts who are responsible for collecting, analyzing, and reporting
data that helps guide Cyclistic marketing strategy. 

- **Cyclistic executive team**: The notoriously detail-oriented executive team will decide whether to approve the
recommended marketing program.

---

## PREPARE
Involves collecting data and information and ensuring it satisfies necessary parameters.

### Data Location
I'm using all the data for the 12 months of the year 2023. Data is stored in CSV file available [here](https://divvy-tripdata.s3.amazonaws.com/index.html). The data is public and made available by Motivate International Inc. under this [license](https://divvybikes.com/data-license-agreement). The privacy of the riders is safeguarded as there are no IDs and it's not possible to identify the riders.

### Data Organization
The dataset contains 5,719,877 Observations and 13 Attributes.

#### `Features:`

**ride_id** => Unique ID per ride

**rideable_type** => Type of bicycle used

**started_at** => Date and time at which bicycle was checked out

**ended_at** => Date and time at which bicycle was checked in

**start_station_name** => Name of the station at the start of the trip

**start_station_id** => Unique identifier for the start station

**end_station_name** => Name of the station at the end of the trip

**end_station_id** => Unique identifier for the end station

**Start_lat** => Latitude of the start station

**start_lng** => Longitude of the start station

**end_lat** => Latitude of the end station

**end_lng** => Longitude of the end station

**member_casual** => Rider type, Member or Casual

---

## Tool
The tools used in this project are BigQuery and Tableau.

---


## PROCESS
This phase of the analysis process includes cleaning the data and making sure it is fit for purpose. As well as making any modifications necessary.

A summary of the cleaning and manipulation done to the data is presented below:

1.	Removed 1,388,170 Null values which reduced the number of Observations from 5,719,877  to 4,331,707.
2.	Checked for consistency of ship_date and order_date (Nested the IF() and OR() functions to ensure ship_date is greater than or equal to order_date).
3.	Changed sales and profit columns General datatype to currency datatype.

The Data Cleaning Process:

1. Putting all the datasets into one table

```sql
------------------------------------ DATA CLEANING PROCESS ----------------------------------

------- CREATING A TEMP TABLE TO AVOID INCONSISTENCY IN DATATYPES

CREATE OR REPLACE TEMP TABLE combined_data (
  ride_id STRING,
  rideable_type STRING,
  started_at TIMESTAMP,
  ended_at TIMESTAMP,
  start_station_name STRING,
  start_statiion_id STRING,
  end_station_name STRING,
  end_station_id STRING,
  start_lat FLOAT64,
  start_lng FLOAT64,
  end_lat FLOAT64,
  end_lng FLOAT64,
  member_casual STRING

);

------- INSERTING ALL TABLES INTO TEMP TABLE
INSERT INTO combined_data
  SELECT *
  FROM `nimble-root-410821.project1.2023_01` 
  UNION ALL
  SELECT * 
  FROM `nimble-root-410821.project1.2023_02`
  UNION ALL 
  SELECT *
  FROM `nimble-root-410821.project1.2023_03`
  UNION ALL
  SELECT *
  FROM `nimble-root-410821.project1.2023_04`
  UNION ALL
  SELECT *
  FROM `nimble-root-410821.project1.2023_05`
  UNION ALL  
  SELECT *
  FROM `nimble-root-410821.project1.2023_06`
  UNION ALL
  SELECT * 
  FROM `nimble-root-410821.project1.2023_07`
  UNION ALL
  SELECT *
  FROM `nimble-root-410821.project1.2023_08`
  UNION ALL
  SELECT *
  FROM `nimble-root-410821.project1.2023_09` 
  UNION ALL
  SELECT *
  FROM `nimble-root-410821.project1.2023_10`
  UNION ALL
  SELECT *
  FROM `nimble-root-410821.project1.2023_11`
  UNION ALL
  SELECT *
  FROM `nimble-root-410821.project1.2023_12` ;


------- CHECKING ROWS AND COLUMNS

SELECT *
FROM combined_data;


```

---

2. Removing Null values alongside adding ride length, day of week, month and date columns to a temporary table

```sql
------- REMOVING NULL AND BLANK VALUES
CREATE OR REPLACE TEMP TABLE cleaned_combined_data1 AS
WITH cleaned_combined_data AS (
  SELECT
    ride_id,
    rideable_type,
    started_at,
    ended_at,
    start_station_name,
    start_statiion_id,
    end_station_name,
    end_station_id,
    start_lat,
    start_lng,
    end_lat,
    end_lng,
    member_casual 
  FROM combined_data 
  
  WHERE 
    ride_id IS NOT NULL 
    AND rideable_type IS NOT NULL
    AND started_at IS NOT NULL
    AND ended_at IS NOT NULL  
    AND start_station_name IS NOT NULL
    AND start_statiion_id IS NOT NULL  
    AND end_station_name IS NOT NULL
    AND end_station_id IS NOT NULL   
    AND start_lat IS NOT NULL
    AND start_lng IS NOT NULL
    AND end_lat IS NOT NULL 
    AND end_lng IS NOT NULL
    AND member_casual IS NOT NULL

)

------- CREATING ride_length and day_of_week COLUMNS
  SELECT
    *,
    DATETIME_DIFF(ended_at, started_at, MINUTE) AS ride_length_minutes,
    EXTRACT(DAYOFWEEK FROM started_at) AS day_of_week1,
    EXTRACT(MONTH FROM started_at) AS month1,
    EXTRACT(DATE FROM started_at) AS date
      FROM cleaned_combined_data;

```

3. Converting day of week and month columns, removing inconsistent ride length values alongside adding time period column to a temporary table.

```sql
------ CONVERTING DAY OF WEEK
CREATE OR REPLACE TEMP TABLE data_with_day_of_week1 AS
WITH data_with_day_of_week1 AS (
  SELECT
    *,
    CASE
      WHEN day_of_week1 = 1 THEN 'Sunday'
      WHEN day_of_week1 = 2 THEN 'Monday'
      WHEN day_of_week1 = 3 THEN 'Tuesday'
      WHEN day_of_week1 = 4 THEN 'Wednesday'
      WHEN day_of_week1 = 5 THEN 'Thursday'
      WHEN day_of_week1 = 6 THEN 'Friday'
  ELSE 'Saturday'
  END AS day_of_week,
     CASE
      WHEN day_of_week1 IN (1, 7) THEN 'weekend'
      ELSE 'weekday'
    END AS time_period,
  CASE
      WHEN month1 = 1 THEN 'January'
      WHEN month1 = 2 THEN 'February'
      WHEN month1 = 3 THEN 'March'
      WHEN month1 = 4 THEN 'April'
      WHEN month1 = 5 THEN 'May'
      WHEN month1 = 6 THEN 'June'
      WHEN month1 = 7 THEN 'July'
      WHEN month1 = 8 THEN 'August'
      WHEN month1 = 9 THEN 'September'
      WHEN month1 = 10 THEN 'October'
      WHEN month1 = 11 THEN 'November'
  ELSE 'December'
  END AS  month     
FROM cleaned_combined_data1

)


------- REMOVING INCONSISTENT ride_length VALUES
SELECT
    *
   FROM data_with_day_of_week1
WHERE
  ride_length_minutes > 1;

```

4. Removing the start_lat, start_lng, end_lat and end_lng columns since we won't need them in the analysis.

```sql
------- REMOVING start_lat, start_lng, end_lat, and end_long COLUMNS SINCE WE WON'T NEED THEM
CREATE OR REPLACE TEMP TABLE final_data AS
WITH final_data1 AS (
  SELECT
    * EXCEPT(start_lat, start_lng, end_lat, end_lng )
  FROM data_with_day_of_week1
  ORDER BY
    started_at

)

SELECT *
FROM final_data1;

```



## **ANALYZE**

In this phase we analyze the data using statistical methods to find patterns, relationships, and trends.


**Outlined below are the key takeaways derived from the analysis of the data:**

```sql
------- FINDING THE PROPORTION OF MEMBER AND CASUAL RIDERS BY RIDE COUNT
CREATE OR REPLACE TABLE nimble-root-410821.project1.percentage_table AS --- Creating percentage_table
SELECT
  DISTINCT member_casual,
  COUNT(*) AS ride_count,
  ROUND(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM final_data),2) AS percentage_count,
  SUM(ride_length_minutes) AS time_spent,
  ROUND(SUM(ride_length_minutes) * 100.0 / (SELECT SUM(ride_length_minutes) FROM final_data),2) AS percentage_time_spent
FROM final_data
GROUP BY
  member_casual;

```

![](Number_Rides_and_Time_Spent_by_Membership)
![Screenshot 2024-01-19 220503](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/32b68fd1-e4a6-40be-9357-07e317602925)

Based on the chart, member riders took almost 2/3 of total rides but contributed only slightly more than half of total ride time, suggesting members take more frequent but shorter rides compared to casual riders who make up over 1/3 of rides but nearly 1/2 of total ride time.

---
     

```sql
-------- CHECKING NUMBER OF RIDES AND RIDE LENGTH BY MONTH AND BY DAY: CASUAL VS MEMBER
CREATE OR REPLACE TABLE nimble-root-410821.project1.avg_ride_table AS
SELECT
  start_station_name,
  rideable_type,
  date,
  member_casual,
  day_of_week,
  time_period,
  month,
  COUNT(*) AS ride_count,
  ROUND(AVG(ride_length_minutes),2) AS avg_ride
FROM final_data
GROUP BY
  start_station_name,
  rideable_type,
  date,
  member_casual,
  day_of_week,
  time_period,
  month
ORDER BY
  ride_count DESC;  

```

[](Average_Ride_Length_by_Month_and_Day)
![Dashboard 3](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/b50a942d-9102-4aa8-a493-b6e4b7912cce)

The "average ride length by month" chart shows that January has the shortest rides for both member and casual riders. August is the peak month for member ride length, while casual riders have their longest average rides in July. From the August and July high points, there is a decreasing trend in average ride length for both members and casuals that continues through November and December.

The "average ride length by day" chart shows member riders have the shortest average ride times on Mondays, likely due to individuals beginning their workweek. Across the week, member ride lengths remain steady with the highest averages on Saturdays and Sundays. In contrast, casual riders reach their lowest point midweek on Wednesdays and hit their peaks on the weekends along with members. 


---


[](Total_Rides_by_Month_and_Day)
![Dashboard 3](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/293a836d-8e47-4123-a0bb-4ccfe1a168ca)

The "total rides by month" chart shows January has the lowest total number of rides for both member and casual riders. For members, the peak in total rides occurs in August, while casual riders reach their peak total rides in July. There is a decreasing trend through December for total rides among both member and casual rider segments.

The "total rides by day" chart shows Member ridership is highest on Thursdays, consistent on weekdays, and decreases on weekends. This indicates members are likely commuters using bikes to travel to and from work. In contrast, Casual ridership is lowest on Mondays, increases steadily during the week, and peaks on Saturdays. Casual users likely ride recreationally, with weekend days being most popular.

---


[](Total_Ride_Count_by_Bike_Type)
![Total RideS by Bike Type](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/94407fd8-8df8-4946-af53-7f5213b37095)

The chart indicates that classic bikes are the most popular choice among both member and casual riders, with electric bikes being the second most preferred option. Interestingly, docked bikes are the least used, especially by casual riders, and electric bikes are not used by member riders at all. This suggests a clear preference for classic bikes among riders, while docked bikes seem to have lower overall usage, particularly among casual riders.

---


```sql
-------- CHECKING NUMBER OF RIDES AND RIDE LENGTH BY MONTH AND BY DAY: CASUAL VS MEMBER
CREATE OR REPLACE TABLE nimble-root-410821.project1.avg_ride_table AS
SELECT
  start_station_name,
  rideable_type,
  date,
  member_casual,
  day_of_week,
  time_period,
  month,
  COUNT(*) AS ride_count,
  ROUND(AVG(ride_length_minutes),2) AS avg_ride
FROM final_data
GROUP BY
  start_station_name,
  rideable_type,
  date,
  member_casual,
  day_of_week,
  time_period,
  month
ORDER BY
  ride_count DESC;  


-------- CHECKING MOST ROOTS BY MONTH AND DAY (MEMBER)
CREATE OR REPLACE TABLE nimble-root-410821.project1.member_table AS --- Creating member_table
SELECT  
  start_station_name,
  end_station_name,
  rideable_type,
  date,
  day_of_week,
  time_period,
  member_casual,
  COUNT(*) AS ride_count,
  AVG(ride_length_minutes) AS avg_ride
FROM final_data
WHERE
  member_casual = 'member'
GROUP BY
  start_station_name,
  end_station_name,
  rideable_type,
  date,
  day_of_week,
  time_period,
  month,
  member_casual
ORDER BY
  ride_count DESC;



-------- CHECKING MOST ROOTS BY MONTH AND DAY (CASUAL)
CREATE OR REPLACE TABLE nimble-root-410821.project1.casual_table AS --- Creating casual_table
SELECT  
  start_station_name,
  end_station_name,
  rideable_type,
  date,
  day_of_week,
  time_period,
  month,
  member_casual,
  COUNT(*) AS ride_count,
  AVG(ride_length_minutes) AS avg_ride
FROM final_data
WHERE
  member_casual = 'casual'
GROUP BY
  start_station_name,
  end_station_name,
  rideable_type,
  date,
  day_of_week,
  time_period,
  month,
  member_casual
ORDER BY
  ride_count DESC;


```


[](Q1_Total_Ride_and_Average_Ride)
![Dashboard 2](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/e5dfa5c1-182b-4647-9c1f-0b953ad7d3e4)

During winter, Member ridership fluctuates during weekdays and peaks on Tuesdays and sharply declining on weekends, with a consistent average ride length throughout the week. In contrast, casual riders maintain low ride counts on weekdays but experience higher average ride lengths, especially with notable peaks on weekends.

[](Qtr1_Top_10_Rides_Member_Casual)
![Screenshot 2024-01-22 011916](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/7bae64cb-1d35-44e6-9eb5-98e8f59cca4c)

Members favor routes connecting residential areas to major work hubs and transit centers like Ellis Ave & 60th St, University Ave & 57th St and Clinton St & Washington Blvd. This indicates members rely heavily on cycling for daily commuting and utility transportation. In contrast, casual riders have a mix of work-related hubs such as university Ave & 57th St and recreational routes including Streeter Dr & Grand Ave, Shedd Aquarium, and Dusable Lake Shore Dr & Monroe St which follow scenic lakefront paths and lead to popular tourist spots. The member route preferences point to cycling as a commuting tool while casual routes suggest leisurely sightseeing. 

---

[](Q2_Total_Ride_and_Average_Ride)
![Dashboard 2](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/6183120b-b4da-47a7-8d42-ae77fe56f1d6)

During spring, member ridership exhibits fluctuation throughout weekdays, reaching a peak on Thursdays and declining on weekends, while maintaining a consistent average ride length. In contrast, casual ridership displays ride count fluctuations on weekdays, peaking on Saturdays, and demonstrates higher average ride lengths, particularly with noticeable peaks on weekends.


[](Qtr2_Top_10_Rides_Member_Casual)
![Screenshot 2024-01-22 093816](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/38ab0d7c-3145-419b-afd7-952f4e27f8e3)

For members, there is a significant presence of recreational routes in addition to commuting and work-related hubs. In contrast, casual riders tend to prefer more recreational and sightseeing paths,

---

[](Q3_Total_Ride_and_Average_Ride)
![Dashboard 2](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/bc78a446-a391-40f5-a131-8af4cfdede3e)

During spring, member ridership exhibits fluctuation throughout weekdays, reaching a peak on Thursdays and declining on weekends, while maintaining a consistent average ride length. In contrast, casual ridership displays ride count fluctuations on weekdays, peaking on Saturdays, and demonstrates higher average ride lengths, particularly with noticeable peaks on weekends.



[](Qtr3_Top_10_Rides_Member_Casual)
![Screenshot 2024-01-22 103958](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/323fb0a6-9f95-4435-ab66-30847d2e0a95)

Members show a notable presence on popular routes, resembling those observed during the spring, but with significantly higher ride counts. The routes continue to include a mix of commuting and work-related hubs alongside recreational paths. Similarly, casual riders exhibit preferences for recreational and sightseeing paths echoing patterns observed in spring.

---

[](Q4_Total_Ride_and_Average_Ride)
![Dashboard 2](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/4d3403ca-ae05-4550-b6be-81b67454a48b)

During spring, member ridership exhibits fluctuations throughout weekdays, reaching a peak on Thursdays and declining on weekends, while maintaining a consistent average ride length. In contrast, casual ridership displays ride count fluctuations on weekdays, peaking on Saturdays, and demonstrates higher average ride lengths, particularly with noticeable peaks on weekends.


[](Qtr4_Top_10_Rides_Member_Casual)
![Screenshot 2024-01-22 105025](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/0ca83f59-b6a3-4843-8342-84a42fe57054)

Members are prominently present on popular routes, similar to patterns observed in winter but with considerably higher ride counts. These routes predominantly encompass commuting and work-related paths. In contrast, casual riders display preferences for both work-related hubs and recreational or sightseeing paths with most routes being recreational paths. 


---

## ACT
Upon completion of data processing, analysis, and insight dissemination, the conclusive phase involves formulating a well-aligned course of action for Cyclistic Bike-Share, congruent with its business goals and mission. My recommendations are as follows:

1. Highlight the benefits of membership by making sure casual riders are aware of the benefits of becoming a member, such as discounted ride rates.

2. Focus the marketing campaign during the period from Spring to the end of Summer, considering that most casual riders actively use the service during this timeframe.

3. Offer discounted annual memberships during peak casual riding seasons in July and August to capture interest when ride counts are highest.

4. Run targeted promotions on weekends when casual usage peaks to motivate sign-ups. Emphasize the convenience of unlimited rides.

5. With the prevalence of classic bikes, ensure adequate availability across the network, particularly at stations along high casual usage routes.


 ---
 
## CONCLUSION

During this project, I successfully employed BigQuery for data cleansing, processing, and analysis, alongside Tableau for data visualization. These tools were instrumental in extracting valuable insights from the data, aligning with the principles of the Google Data Analytics Professional Certification program.

---

## REFERENCES

**Google Data Analytics Professional Certificate**.





