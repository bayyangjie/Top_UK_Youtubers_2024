# Data Portfolio: Excel to Power BI

# Table of contents 

- [Objective](#objective)
- [Data Source](#data-source)
- [Stages](#stages)
- [Design](#design)
  - [Mockup](#mockup)
  - [Tools](#tools)
- [Development](#development)
  - [Pseudocode](#pseudocode)
  - [Data Exploration](#data-exploration)
  - [Data Cleaning](#data-cleaning)
  - [Transform the Data](#transform-the-data)
  - [Create the SQL View](#create-the-sql-view)
- [Testing](#testing)
  - [Data Quality Tests](#data-quality-tests)
- [Visualization](#visualization)
  - [Results](#results)
  - [DAX Measures](#dax-measures)
- [Analysis](#analysis)
  - [Findings](#findings)
  - [Validation](#validation)
  - [Discovery](#discovery)
- [Recommendations](#recommendations)
  - [Potential ROI](#potential-roi)
  - [Potential Courses of Actions](#potential-courses-of-actions)
- [Conclusion](#conclusion)


# Objective 

- What is the key pain point? 

The Head of Marketing wants to find out who the top YouTubers are in 2024 to decide on which YouTubers would be best to run marketing campaigns throughout the rest of the year.


- What is the ideal solution? 

To create a dashboard that provides insights into the top UK YouTubers in 2024 that includes their 
- subscriber count
- total views
- total videos, and
- engagement metrics

This will help the marketing team make informed decisions about which YouTubers to collaborate with for their marketing campaigns.

## User story 

As the Head of Marketing, I want to use a dashboard that analyses YouTube channel data in the UK . 

This dashboard should allow me to identify the top performing channels based on metrics like subscriber base and average views. 

With this information, I can make more informed decisions about which Youtubers are right to collaborate with, and therefore maximize how effective each marketing campaign is.

# Data source 

- What data is needed to achieve our objective?

We need data on the top UK YouTubers in 2024 that includes their 
- channel names
- total subscribers
- total views
- total videos uploaded

- Where is the data coming from? 
The data is sourced from Kaggle (an Excel extract), [see here to find it.](https://www.kaggle.com/datasets/bhavyadhingra00020/top-100-social-media-influencers-2024-countrywise?resource=download)

# Stages

- Design
- Development
- Testing
- Analysis

# Design 

## Dashboard components required 
- What should the dashboard contain based on the requirements provided?

To understand what it should contain, we need to figure out what questions we need the dashboard to answer:

1. Who are the top 10 YouTubers with the most subscribers?
2. Which 3 channels have uploaded the most videos?
3. Which 3 channels have the most views?
4. Which 3 channels have the highest average views per video?
5. Which 3 channels have the highest views per subscriber ratio?
6. Which 3 channels have the highest subscriber engagement rate per video uploaded?


## Tools 


| Tool | Purpose |
| --- | --- |
| Excel | Exploring the data |
| SQL Server | Cleaning, testing, and analyzing the data |
| Power BI | Visualizing the data via interactive dashboards |
| GitHub | Hosting the project documentation and version control |


# Development

## Pseudocode

- What's the general approach in creating this solution from start to finish?

1. Get the data
2. Explore the data in Excel
3. Load the data into SQL Server
4. Clean the data with SQL
5. Test the data with SQL
6. Visualize the data in Power BI
7. Generate the findings based on the insights
8. Write the documentation
9. Publish the data to GitHub Pages

## Data exploration notes

This is the stage where we have a scan of what's in the data, errors, inconsistencies, bugs, weird and corrupted characters etc  


- Observations

1. There are at least 4 columns that contain the data we need for this analysis, which signals we have everything we need from the file without needing to contact the client for any more data. 
2. The first column contains the channel ID with what appears to be channel IDs, which are separated by a @ symbol - we need to extract the channel names from this.
3. Some of the cells and header names are in a different language - we need to confirm if these columns are needed, and if so, we need to address them.
4. We have more data than we need, so some of the columns would need to be removed


## Data cleaning 
- What do we expect the clean data to look like? (What should it contain? What constraints should we apply to it?)

The aim is to refine our dataset to ensure it is structured and ready for analysis. 

The cleaned data should meet the following criteria and constraints:

- Only relevant columns should be retained and renaming the columns that are kept.
- All data types should be appropriate for the contents of each column.
- No column should contain null values, indicating complete data for all records.

Below is a table outlining the constraints on the cleaned dataset:

| Property | Description |
| --- | --- |
| Number of Rows | 100 |
| Number of Columns | 4 |

And here is a tabular representation of the expected schema for the clean data:

| Column Name | Data Type | Nullable |
| --- | --- | --- |
| channel_name | VARCHAR | NO |
| total_subscribers | INTEGER | NO |
| total_views | INTEGER | NO |
| total_videos | INTEGER | NO |

- What steps are needed to clean and shape the data into the desired format?

1. Remove unnecessary columns by only selecting the ones needed
2. Extract Youtube channel names from the first column
3. Rename columns using aliases


### Transform the data 

```sql
/*
# 1. Select the required columns
# 2. Extract the channel name from the 'NOMBRE' column
*/

-- 1.
SELECT
    SUBSTRING(NOMBRE, 1, CHARINDEX('@', NOMBRE) -1) AS channel_name,  -- 2.
    total_subscribers,
    total_views,
    total_videos

FROM
    top_uk_youtubers_2024
```

# Testing 

- Data quality checks performed

## Row count check
```sql
/*
# Count the total number of records (or rows) are in the SQL view
*/

SELECT
    COUNT(*) AS no_of_rows
FROM
    view_uk_youtubers_2024;

```

![Row count check](https://github.com/bayyangjie/Top_UK_Youtubers_2024/blob/main/assets/images/row%20count%20check.png?raw=true)

## Column count check
### SQL query
```
SELECT 
	COUNT(*) as column_count
FROM 
	INFORMATION_SCHEMA.COLUMNS
WHERE 
	TABLE_NAME = 'view_uk_youtubers_2024'
```
![Column count check](https://github.com/bayyangjie/Top_UK_Youtubers_2024/blob/main/assets/images/column%20count%20check.png?raw=true)

## Data type check
### SQL query
```
SELECT 
	COLUMN_NAME,
	DATA_TYPE
FROM 
	INFORMATION_SCHEMA.COLUMNS
WHERE 
	TABLE_NAME = 'view_uk_youtubers_2024'
```
![Data type check](https://github.com/bayyangjie/Top_UK_Youtubers_2024/blob/main/assets/images/data%20type%20check.png?raw=true)

## Duplicate records check
### SQL query
```
SELECT 
	channel_name, COUNT(*) as duplicate_count
FROM 
	view_uk_youtubers_2024
GROUP BY 
	channel_name
HAVING 
	COUNT(*) > 1
```
![Duplicate records check](https://github.com/bayyangjie/Top_UK_Youtubers_2024/blob/main/assets/images/duplicate%20records%20check.png?raw=true)

## DAX Measures

### 1. Total Subscribers (M)
``` SQL
Total Subscriber (M) = 
VAR million = 1000000
VAR sumOfSubscribers = SUM(view_uk_youtubers_2024[total_subscribers])
VAR totalSubscribers = DIVIDE(sumOfSubscribers, million)

RETURN totalSubscribers
```

### 2. Total Views (B)
```SQL
Total Views (B) = 
 VAR billion = 1000000000
 VAR sumOfTotalViews = SUM(view_uk_youtubers_2024[total_views])
 VAR totalViews = DIVIDE(sumOfTotalViews, billion)

 RETURN totalViews
```

### 3. Total Videos
```SQL
Total Videos = 
VAR totalVideos = SUM(view_uk_youtubers_2024[total_videos])

RETURN totalVideos
```

### 4. Average Views Per Video (M)
```SQL
Avg Views per Video (M) = 
VAR sumOfTotalViews = SUM(view_uk_youtubers_2024[total_views])
VAR sumOfTotalVideos = SUM(view_uk_youtubers_2024[total_videos])
VAR avgViewsPerVideo = DIVIDE(sumOfTotalViews , sumOfTotalVideos, BLANK())
VAR finalAvgViewsPerVideo = DIVIDE(avgViewsPerVideo , 1000000, BLANK())

RETURN finalAvgViewsPerVideo
```

### 5. Subscriber Engagement Rate
```SQL
Subscriber Engagement Rate = 
 VAR sumOfTotalSubscribers = SUM(view_uk_youtubers_2024[total_subscribers])
 VAR sumOfTotalVideos = SUM(view_uk_youtubers_2024[total_videos])
 VAR subscriberEngRate = DIVIDE(sumOfTotalSubscribers , sumOfTotalVideos , BLANK())

 RETURN subscriberEngRate
```

### 6. Views per subscriber
```SQL
Views per Subscriber = 
 VAR sumOfTotalViews = SUM(view_uk_youtubers_2024[total_views])
 VAR sumOfTotalSubscribers = SUM(view_uk_youtubers_2024[total_subscribers])
 VAR viewsPerSubscriber = DIVIDE(sumOfTotalViews , sumOfTotalSubscribers , BLANK())

 RETURN viewsPerSubscriber
```


# Analysis

## Findings

### 1. Top 10 Youtubers with the most subscribers

| Rank | Channel Name     | Subscribers (M) |
|------|------------------|-----------------|
| 1    |NoCopyrightSounds | 33.9	    |
| 2    |DanTDM		  | 29.2	    |
| 3    |Dan Rhodes	  | 27.0	    |
| 4    |Missa Katy	  | 25.3	    |
| 5    |KSI		  | 25.0	    |
| 6    |MisterMax	  | 24.9	    |
| 7    |Dua Lipa	  | 24.0	    |
| 8    |Jelly		  | 23.5	    |
| 9    |colinfurze	  | 20.6	    |
| 10   |Ali-A		  | 19.5	    |


### 2. Top 3 channels with most uploaded videos

| Rank | Channel Name    | Videos Uploaded |
|------|-----------------|-----------------|
| 1    | Boomerage UK    | 186,852         |
| 2    | More Emily      | 58,052          |
| 3    | Sing King       | 43,290          |


### 3. Top 3 channels with most views

| Rank | Channel Name | Total Views (B) |
|------|--------------|-----------------|
| 1    | DanTDM       | 20.09           |
| 2    | Dan Rhodes   | 19.13           |
| 3    | Mister Max   | 16.40           |


### 4. Top 3 channels with highest average views per video


| Channel Name | Average Views per Video (M) |
|--------------|-----------------|
| 24 News HD   | 346.37          |
| Slogo        | 62.84           |
| Dua Lipa     | 45.91           |


### 5. Top 3 channels with the highest views per subscriber ratio

| Rank | Channel Name    | Views per Subscriber        |
|------|-----------------|---------------------------- |
| 1    | Dumori Bay      | 1205.86                     |
| 2    | ARPO The Robot	 | 1064.06                     |
| 3    | Awakening Music | 1043.18                     |


### 6. Top 3 channels with the highest subscriber engagement rate per video uploaded

| Rank | Channel Name    | Subscriber Engagement Rate  |
|------|-----------------|---------------------------- |
| 1    | 24 News HD      | 351,500.00                  |
| 2    | Slogo	         | 111,458.33                  |
| 3    | Dua Lipa        | 77,922.08                   |



### Notes

For this analysis, the priority is analyzing the expected ROI (net profit) for the marketing client based on three different metrics such as the YouTube channels with the most 

- subscribers
- total views
- videos uploaded


## Validation checks (SQL/Excel)


### 1. Youtubers with the most subscribers 

#### Calculation breakdown

Campaign idea = product placement 

1. NoCopyrightSounds 

- Average views per video = 6.05 million
- Product cost = $5
- Potential units sold per video = 6.05 million x 2% conversion rate = 121,000 units sold
- Potential revenue per video = 121,000 x $5 = $605,000
- Campaign cost (one-time fee) = $50,000
- **Net profit = $605,000 - $50,000 = $555,000**

2. DanTDM

- Average views per video = 5.37 million
- Product cost = $5
- Potential units sold per video = 5.37 million x 2% conversion rate = 107,400 units sold
- Potential revenue per video = 107,400 x $5 = $537,000
- Campaign cost (one-time fee) = $50,000
- **Net profit = $537,000 - $50,000 = $487,000**

3. Dan Rhodes

- Average views per video = 11.40 million
- Product cost = $5
- Potential units sold per video = 11.40 million x 2% conversion rate = 228,000 units sold
- Potential revenue per video = 228,000 x $5 = $1,140,000
- Campaign cost (one-time fee) = $50,000
- **Net profit = $1,140,000 - $50,000 = $1,090,000**


Best option from category: Dan Rhodes


### SQL query

```SQL
/* 
1. Define the variables
2. Create a CTE that rounds the average views per video
3. Select the columns that are required for the analysis
4. Filter the results by the Youtube channels with the highest subscriber bases
5. Order by net profit (from highest to lowest)
*/

-- 1. 
DECLARE @conversionRate FLOAT = 0.02;   -- The conversion rate @ 2%
DECLARE @productCost MONEY = 5.0;	-- The product cost @ $5
DECLARE @campaignCost MONEY = 50000;	-- The campaign cost @ $50,000

-- 2.
WITH ChannelData AS (
	SELECT
		channel_name,
		total_views,
		total_videos,
		ROUND(CAST(total_views AS FLOAT)/total_videos, -4) AS rounded_avg_views_per_video 
	FROM 
		view_uk_youtubers_2024
)

-- 3.
SELECT
	channel_name,
	rounded_avg_views_per_video,
	(rounded_avg_views_per_video*@conversionRate) AS potential_units_sold_per_video,
	(rounded_avg_views_per_video*@conversionRate*@productCost) AS potential_revenue_per_video,
	(rounded_avg_views_per_video*@conversionRate*@productCost)-@campaignCost AS net_profit

FROM 
	ChannelData

-- 4.
WHERE 
	channel_name IN ('NoCopyrightSounds', 'DanTDM' , 'Dan Rhodes')

-- 5.
ORDER BY
	net_profit DESC;
```
#### Output
![Most subsc](https://github.com/bayyangjie/Top_UK_Youtubers_2024/blob/main/assets/images/ROI_Youtubers%20with%20the%20highest%20subscribers.png?raw=true) 


### 2. Youtubers with the most videos uploaded

#### Calculation breakdown 

Campaign idea = sponsored video series  

1. Boomerang UK

- Average views per video = 0.02 million
- Product cost = $5
- Potential units sold per video = 20,000 x 2% conversion rate = 400 units sold
- Potential revenue per video = 400 x $5= $2,000
- Campaign cost (11-videos @ $5,000 each) = $55,000
- **Net profit = $2000 - $55,000 = -$53,000 (potential loss)**

2. More Emily

- Average views per video = 0.08 million
- Product cost = $5
- Potential units sold per video = 0.08 million x 2% conversion rate = 1600 units sold
- Potential revenue per video = 1600 x $5 = $8,000
- Campaign cost (11-videos @ $5,000 each) = $55,000
- **Net profit = $8000 - $55,000 = -$47,000 (potential loss)**

3. Sing King

- Average views per video = 0.12 million
- Product cost = $5
- Potential units sold per video = 0.12 million x 2% conversion rate = 2400 units sold
- Potential revenue per video = 2400 x $5 = $12,000
- Campaign cost (11-videos @ $5,000 each) = $55,000
- **Net profit = $12,000 - $55,000 = -$43,000 (potential loss)**


Best option from catgegory: Sing King


### SQL query
```SQL
-- 1. 
DECLARE @conversionRate FLOAT = 0.02;			-- The conversion rate @ 2%
DECLARE @productCost MONEY = 5.0;				-- The product cost @ $5
DECLARE @campaignCostPerVideo FLOAT = 5000.0;   -- The campaign cost per video @ $5,000
DECLARE @numberOfVideos INT = 11;               -- The number of videos (11)



-- 2.
WITH ChannelData AS (
	SELECT
		channel_name,
		total_views,
		total_videos,
		ROUND(CAST(total_views AS FLOAT)/total_videos, -4) AS rounded_avg_views_per_video 
	FROM 
		view_uk_youtubers_2024
)


-- 3.
SELECT
	channel_name,
	rounded_avg_views_per_video,
	(rounded_avg_views_per_video*@conversionRate) AS potential_units_sold_per_video,
	(rounded_avg_views_per_video*@conversionRate*@productCost) AS potential_revenue_per_video,
	(rounded_avg_views_per_video*@conversionRate*@productCost)-(@campaignCostPerVideo*@numberOfVideos) AS net_profit

FROM 
	ChannelData


-- 4.
WHERE 
	channel_name IN ('Boomerang UK' , 'More Emily' , 'Sing King')


-- 5.
ORDER BY 
	net_profit DESC
```
#### Output
![ROI of Youtubers with most uploaded videos](https://github.com/bayyangjie/Top_UK_Youtubers_2024/blob/main/assets/images/ROI_Youtubers%20with%20most%20videos%20uploaded.png?raw=true)


### 3. Youtubers with the most views

#### Calculation breakdown

Campaign idea = Influencer marketing 

a. DanTDM

- Average views per video = 5.37 million
- Product cost = $5
- Potential units sold per video = 5.37 million x 2% conversion rate = 107,400 units sold
- Potential revenue per video = 107,400 x $5 = $537,000
- Campaign cost (3-month contract) = $130,000
- **Net profit = $537,000 - $130,000 = $407,000**

b. Dan Rhodes

- Average views per video = 11.40 million
- Product cost = $5
- Potential units sold per video = 11.40 million x 2% conversion rate = 228,000 units sold
- Potential revenue per video = 228,000 x $5 = $1,140,000
- Campaign cost (3-month contract) = $130,000
- **Net profit = $1,140,000 - $130,000 = $1,010,0000**

c. Mister Max

- Average views per video = 13.85 million
- Product cost = $5
- Potential units sold per video = 13.85 million x 2% conversion rate = 277,000 units sold
- Potential revenue per video = 277,000 x $5 = $1,385,000
- Campaign cost (3-month contract) = $130,000
- **Net profit = $1,385,000 - $130,000 = $1,255,000**


Best option from category: Mister Max


### SQL query
```SQL
-- 1.
DECLARE @conversionRate FLOAT = 0.02;	 -- The conversion rate @ 2%
DECLARE @productCost MONEY = 5.0;		 -- The product cost @ $5
DECLARE @campaignCost FLOAT = 13000.0;   -- The campaign cost per video @ $13,000


-- 2.
/* Creating a CTE to only use the columns that are needed and to perform the rounding */
WITH ChannelData AS (
	SELECT
		channel_name,
		total_views,
		total_videos,
		ROUND(CAST(total_views AS FLOAT)/total_videos, -4) AS rounded_avg_views_per_video 
	FROM 
		view_uk_youtubers_2024
)


-- 3.
SELECT
	channel_name,
	rounded_avg_views_per_video,
	(rounded_avg_views_per_video*@conversionRate) AS potential_units_sold_per_video,
	(rounded_avg_views_per_video*@conversionRate*@productCost) AS potential_revenue_per_video,
	(rounded_avg_views_per_video*@conversionRate*@productCost)-(@campaignCost) AS net_profit

FROM 
	ChannelData


-- 4.
WHERE channel_name IN ('DanTDM' , 'Dan Rhodes' , 'Mister Max')


-- 5.
ORDER BY net_profit DESC;
```

#### Output
![ROI of Youtubers with most views](https://github.com/bayyangjie/Top_UK_Youtubers_2024/blob/main/assets/images/ROI_Youtubers%20with%20most%20views.png?raw=true)


## Discovery

What was discovered

1. NoCopyRightSounds, Dan Rhodes and DanTDM are the channels with the most subscribers in the UK
2. Boomerang UK, More Emily and Sing King are the channels with the most uploaded videos
3. DanTDM, DanRhodes and Mister Max are the channels with the most views
4. 
