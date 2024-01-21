# Google Data Analytics Professional Certificate - Capstone Project 1 - Cyclistic bike-share case study
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
I'm using all the data for the 12 months of the year 2020. Data is stored in CSV file available [here](https://divvy-tripdata.s3.amazonaws.com/index.html). The data is public and made available by Motivate International Inc. under this [license](https://divvybikes.com/data-license-agreement). The privacy of the riders is safeguarded as there are no IDs and it's not possible to identify the riders.

### Data Organization
The dataset contains 9999 rows and 21 columns.

#### `Features:`

**Row ID** => Unique ID for each row.

**Order ID** => Unique Order ID for each Customer.

**Order Date** => Order Date of the product.

**Ship Date** => Shipping Date of the Product.

**Ship Mode** => Shipping Mode specified by the Customer.

**Customer ID** => Unique ID to identify each Customer.

**Customer Name** => Name of the Customer.

**Segment** => The segment where the Customer belongs.

**Country** => Country of residence of the Customer.

**City** => City of residence of of the Customer.

**State** => State of residence of the Customer.

---

## **ANALYZE**

In this phase we analyze the data using statistical methods to find patterns, relationships, and trends.


**Outlined below are the key takeaways derived from the analysis of the data:**

```sql
------- FINDING THE NUMBER OF MEMBER AND CASUAL RIDERS BY RIDE COUNT
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











[](Average_Ride_Length_by_Bike_Type_and_Time_Period)
![Average Ride Count by Bike Type and Time Period](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/b5d42646-42fa-4a53-8129-477850ed573a)


[](Q1_Total_Ride_and_Average_Ride)
![Dashboard 2](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/db670946-8af3-4589-a6e0-f51c2cc951b3)



[](Q2_Total_Ride_and_Average_Ride)
![Dashboard 2](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/6141c6b9-ec8e-4e5f-88f2-71e1f8fbf400)



[](Q3_Total_Ride_and_Average_Ride)
![Dashboard 2](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/32edef11-2091-404e-904d-972aaba6b6b8)


[](Q4_Total_Ride_and_Average_Ride)
![Dashboard 2](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/7d9220f0-de6a-4b0a-9103-c54eea1a15da)





[](Qtr1_Top_10_Rides_Member_Casual)
![Screenshot 2024-01-20 003648](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/445b4f61-1196-4b4c-a40a-9cefd671dec8)


[](Qtr2_Top_10_Rides_Member_Casual)
![Screenshot 2024-01-20 004636](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/1a94f1cd-e756-4527-9fd9-7ebc7e0acb52)



[](Qtr3_Top_10_Rides_Member_Casual)
![Screenshot 2024-01-20 005046](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/5cf2f5c0-651d-45b1-91b2-42ac89748206)


[](Qtr4_Top_10_Rides_Member_Casual)
![Screenshot 2024-01-20 005714](https://github.com/ziraefrimpong1/Google-Data-Analytics-Professional-Certificate/assets/154938134/f12aa59f-6999-4c86-8db5-6d821645f7c9)












