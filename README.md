<p align="center">
  <img src="https://github.com/vinitpatel97/Google_Capstone_Project_Cyclistic_Bike_Share_Analysis/assets/60802331/673c9ba3-1fd5-46c6-b236-5a870edcc4f8" alt="Your Image Alt Text"  />
</p>

# Case Study: Cyclistic Bike Share Analysis
**Author:** Vinit Patel </p>
**Date:** January 27, 2024

## Introduction
This case study illustrates a data analysis project completed as part of the Google Data Analytics Professional Certificate course offered by Coursera. The study focuses on Cyclistic, a bike-share company in Chicago. Its aim is to understand how two types of bike riders, casual riders, and annual members, utilize bikes differently from January 2023 to December 2023. Casual riders typically purchase single-ride or full-day passes, whereas annual members opt for annual memberships.

## About Cyclistic
In 2016, Cyclistic launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that are geotracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system anytime. The director of marketing believes the company’s future success depends on maximizing the number of annual memberships. Therefore, the team wants to understand how casual riders and annual members use Cyclistic bikes differently. From these
insights, your team will design a new marketing strategy to convert casual riders into annual members.

## Tools Used in Analysis
- Microsoft Excel
- Microsoft SQL Server
- Tableau

## Quick Links
- Course: [Google Data Analytics Professional Certificate](https://www.coursera.org/professional-certificates/google-data-analytics)
- Data Source: [divvy_tripdata](https://divvy-tripdata.s3.amazonaws.com/index.html)
- Tableau Dashboard: [Cyclistic Bike Share Analysis](https://public.tableau.com/views/BikeSharingDashboard_17063715634070/Dashboard?:language=en-US&:display_count=n&:origin=viz_share_link)

## Data Analysis Process

### Ask
**Business Task:**  As a junior data analyst working on the marketing analyst team, the objective is to design a new marketing strategy to convert casual riders into annual members.</P>
**Stakeholders:** The director of marketing and Cyclistic executive team, Cyclistic marketing analytics team

### Prepare
- The dataset consists of Cyclistic’s historical trip data from the last 12 months (January 2023 to December 2023), comprising 12 CSV files, one for each month.
- Each file within the dataset contains 13 columns related to the bike rides, providing detailed information about each ride, including ride ID, rideable type, membership type, date, coordinates, station ID, and more.

### Process
- I utilized Microsoft Excel for the cleaning process of each spreadsheet.
- I checked for duplicate records, removed blank spaces, eliminated irrelevant columns, and sorted the data by the 'started at' date in ascending order.
- Additionally, I created a column named 'ride_duration' to calculate the length of each ride by subtracting the 'started_at' value from the 'ended_at' value, formatting the result as HH:MM:SS.
- I also generated a column named 'day_of_week' to determine the day of the week for each ride's start using the 'WEEKDAY' function in each file. 
- This column was formatted as a number with no decimals, where 1 represents Sunday and 7 represents Saturday.
- The monthly files containing ride data were imported into SQL Server, and I created a table named 'Trip_Data_2023' to store the consolidated data for the entire year.

### Analyze
After performing data cleaning, I conducted the following analysis in SQL Server to address key findings.</p>
**1. Percentage of casual riders vs annual members**
```
SELECT membership_type AS membership_type, 
COUNT (membership_type) AS total_ride, 
SUM (COUNT (membership_type)) OVER () AS total_membership,
CONCAT (CAST (COUNT (membership_type) * 100.0/ SUM (COUNT (membership_type)) OVER () AS DECIMAL (10,2)), '%') membership_percentage
FROM Trip_Data_2023
WHERE membership_type IS NOT NULL
GROUP BY membership_type;
```

**2. Proportion of rides by bike type**
```
SELECT 
  bike_type AS bike_type,
  membership_type AS membership_type,  
  COUNT(bike_type) AS bike_membership_count, 
  SUM(COUNT(bike_type)) OVER (PARTITION BY bike_type) AS total_membership,
  CONCAT(
    CAST(COUNT(bike_type) * 100.0 / SUM(COUNT(bike_type)) OVER (PARTITION BY bike_type) AS DECIMAL(10,2)),
    '%'
  ) AS membership_percentage
FROM Trip_Data_2023
WHERE membership_type IS NOT NULL
GROUP BY bike_type, membership_type
ORDER BY bike_type;
```

**3.	Percentage of rides per month**
```
SELECT membership_type AS membership_type, months,
COUNT (months) AS membership_rides,
SUM (COUNT (months)) OVER (Partition by months) AS total_rides_per_month,
CONCAT (CAST ((COUNT (months)) * 100.0/ SUM (COUNT (months)) OVER (Partition by months) AS DECIMAL (10,2)), '%') AS membership_percentage
FROM Trip_Data_2023
WHERE membership_type is not null 
GROUP BY membership_type, months
ORDER BY 
CASE months
        WHEN 'January' THEN 1
        WHEN 'February' THEN 2
        WHEN 'March' THEN 3
        WHEN 'April' THEN 4
        WHEN 'May' THEN 5
        WHEN 'June' THEN 6
        WHEN 'July' THEN 7
        WHEN 'August' THEN 8
        WHEN 'September' THEN 9
        WHEN 'October' THEN 10
        WHEN 'November' THEN 11
        WHEN 'December' THEN 12
END, membership_type;
```

**4.	Number of rides by day**
```
SELECT membership_type AS membership_type, 
weekdays,
COUNT (weekdays) AS rides_per_day
FROM Trip_Data_2023
WHERE membership_type is not null 
GROUP BY membership_type, weekdays
ORDER BY 
CASE weekdays
     WHEN 'Sunday' THEN 1 
	 WHEN 'Monday' THEN 2
	 WHEN 'Tuesday' THEN 3 
	 WHEN 'Wednesday' THEN 4
	 WHEN 'Thursday' THEN 5
	 WHEN 'Friday' THEN 6
	 WHEN 'Saturday' THEN 7 
END, membership_type;
```

**5. Average ride duration by day**
```
SELECT membership_type AS membership_type, 
weekdays, 
AVG (trip_duration_minutes) as ride_duration
FROM Trip_Data_2023
WHERE membership_type IS NOT NULL
GROUP BY membership_type, weekdays
ORDER BY 
CASE weekdays
     WHEN 'Sunday' THEN 1 
	 WHEN 'Monday' THEN 2
	 WHEN 'Tuesday' THEN 3 
	 WHEN 'Wednesday' THEN 4
	 WHEN 'Thursday' THEN 5
	 WHEN 'Friday' THEN 6
	 WHEN 'Saturday' THEN 7 
END, membership_type;
```

**6.	Number of rides by month**
```
SELECT
  MONTH(started_at) AS months,
  COUNT(*) AS Number_of_rides
FROM
  Trip_Data_2023
GROUP BY
  MONTH(started_at)
ORDER BY
  months;
```

**7.	Number of rides by hour**
```
SELECT membership_type AS membership_type, 
hours,
COUNT(hours) AS rides_per_hour
FROM Trip_Data_2023
WHERE membership_type IS NOT NULL 
GROUP BY membership_type, hours 
ORDER BY hours, membership_type;
```

### Share
[Tableau Dashboard on Cyclistic Bike Share Analysis](https://public.tableau.com/views/BikeSharingDashboard_17063715634070/Dashboard?:language=en-US&:display_count=n&:origin=viz_share_link)
![image](https://github.com/vinitpatel97/Google_Capstone_Project_Cyclistic_Bike_Share_Analysis/assets/60802331/ed4c1272-5fed-4eb2-83dd-6c52da1e6548)

### Act
Based on the provided data analysis, here are three recommendations to convert casual riders into annual members:</p>
**1. Offer Targeted Membership Promotions:**</p>
Utilize the insights from the "Percentage of casual riders vs annual members" pie chart to design targeted membership promotions. Since casual riders constitute a significant portion (35.82%) of the total ridership, offering exclusive discounts or incentives for annual memberships can encourage casual riders to switch to annual memberships. For instance, consider limited time offers or referral programs that provide discounts for transitioning to an annual membership.

![image](https://github.com/vinitpatel97/Google_Capstone_Project_Cyclistic_Bike_Share_Analysis/assets/60802331/e81ee622-2a4e-4e25-9165-93e48fe1931c)

**2. Enhance Electric Bike Availability and Accessibility:**</p>
The analysis of the "Proportion of rides by bike type" horizontal bar chart indicates that electric bikes are preferred by both casual and annual members, with significant usage proportions. Capitalize on this preference by expanding the availability and accessibility of electric bikes across Divvy stations. Focus on strategic placement in high-traffic areas and ensure adequate bike stock to meet demand. Highlight the benefits of electric bikes, such as convenience and eco-friendliness, to attract more riders to the annual membership program.

![image](https://github.com/vinitpatel97/Google_Capstone_Project_Cyclistic_Bike_Share_Analysis/assets/60802331/d74c1736-614a-47a1-a3db-bb94213b97ce)

**3. Implement Tailored Engagement Strategies Based on Riding Patterns:**</P>
Leverage the insights from the "Total rides per month" stacked bar chart and the "Number of rides by hour" line chart to implement tailored engagement strategies based on riding patterns. For instance, analyze peak riding hours and popular riding months among casual riders. Develop targeted marketing campaigns or special events during these periods to promote annual memberships. Additionally, consider offering flexible subscription options, such as seasonal memberships or pay-as-you-go plans, to accommodate varying riding frequencies and preferences.

![image](https://github.com/vinitpatel97/Google_Capstone_Project_Cyclistic_Bike_Share_Analysis/assets/60802331/3d21a38a-8cfd-48c2-84b8-d043cfbd359e)

![image](https://github.com/vinitpatel97/Google_Capstone_Project_Cyclistic_Bike_Share_Analysis/assets/60802331/2455547d-2541-4c16-9543-db29dc40ae03)
