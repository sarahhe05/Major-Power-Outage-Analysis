# Major Power Outage Risks Analysis
This is a project for DSC 80 at UCSD.

By: Sarah He, Hao Zhang

## Introduction
Power outages are significant events that disrupt lives, economies, and critical infrastructure. Understanding the characteristics of major power outages can help communities, policymakers, and utility companies better prepare for and mitigate their impact.

This project focuses on the question: **What are the characteristics of major power outages with higher severity, and what factors may indicate that a power outage would be severe?** A power outage is considered severe if it lasts over 24 hours (1440 minutes) and affects more than 50,000 people.

The dataset used for this analysis was obtained from Purdue University's LASCI research data repository and can be accessed [here](https://engineering.purdue.edu/LASCI/research-data/outages). The original dataset contains **1,534 rows** and **56 columns**. However, this analysis focuses on the following relevant columns:

| Column Name              | Description                                                                                      |
|--------------------------|--------------------------------------------------------------------------------------------------|
| `YEAR`                   | The year when the power outage occurred.                                                         |
| `MONTH`                  | The month when the power outage occurred.                                                        |
| `U.S._STATE`             | The U.S. state where the outage occurred.                                                       |
| `CLIMATE.REGION`         | The regional climate classification of the area affected by the outage (e.g., Northeast, Southwest).|
| `CLIMATE.CATEGORY`       | The general climate classification of the affected region (e.g., warm, normal).                  |
| `OUTAGE.START.DATE`      | The date when the outage started.                                                                |
| `OUTAGE.START.TIME`      | The time when the outage started.                                                                |
| `OUTAGE.RESTORATION.DATE`| The date when the outage was restored.                                                           |
| `OUTAGE.RESTORATION.TIME`| The time when the outage was restored.                                                           |
| `CAUSE.CATEGORY`         | The primary category of the cause of the outage (e.g., severe weather, intentional attack).      |
| `CAUSE.CATEGORY.DETAIL`  | A more detailed explanation of the specific cause of the outage.                                  |
| `OUTAGE.DURATION`        | The total duration of the outage in minutes.                                                     |
| `CUSTOMERS.AFFECTED`     | The number of customers directly affected by the outage.                                         |
| `TOTAL.CUSTOMERS`        | The total number of customers served in the U.S. state.                                          |
| `TOTAL.PRICE`            | The average monthly electricity price in the U.S. state (measured in cents per kilowatt-hour).    |
| `TOTAL.SALES`            | The total electricity consumption in the U.S. state (measured in megawatt-hours).                |
| `TOTAL.REALGSP`          | The total Real Gross State Product, representing the total economic output of the state (measured in chained 2009 U.S. dollars). |
| `PC.REALGSP.STATE`       | The per capita Real Gross State Product of the state (measured in chained 2009 U.S. dollars).    |
| `POPDEN_URBAN`           | The population density of urban areas in the region (measured in persons per square mile).       |
| `POPULATION`             | The total population in the U.S. state for the given year.                                        |

These columns are critical for analyzing the patterns and factors associated with severe power outages. This analysis aims to provide actionable insights that can help stakeholders better prepare for and respond to such events.

## Data Cleaning and Exploratory Data Analysis
### Data Cleaning
The following data cleaning steps were performed to prepare the dataset for analysis. These steps addressed missing values, date and time formatting issues, and redundant columns, ensuring that the data was consistent and usable for further analysis.

#### 1. Removing Rows with Missing Values
Certain columns in the dataset contained missing values, particularly related to the outage start and restoration times, as well as their corresponding dates. These missing values could potentially affect the analysis, so we decided to remove any rows where the relevant fields were missing. Specifically, rows with missing values in the following columns were removed:
- `OUTAGE.START.TIME`
- `OUTAGE.START.DATE`
- `OUTAGE.RESTORATION.DATE`
- `OUTAGE.RESTORATION.TIME`

This step resulted in the removal of only 58 rows, which is a very small fraction of the total dataset. Given that the dataset has over 1500 rows, this small amount of data loss is considered negligible, and we deemed it acceptable to proceed with dropping the missing values.

#### 2. Combining Date and Time Columns into a Single Datetime Column
The columns `OUTAGE.START.DATE` and `OUTAGE.START.TIME` provided the date and time of when the outage started. Similarly, `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.TIME` provided the restoration details. These were separate columns that needed to be combined into single datetime columns for consistency and ease of analysis.

The `OUTAGE.START.TIME` and `OUTAGE.RESTORATION.TIME` columns were converted into time-based objects to properly account for hours, minutes, and seconds. Then, the date and time components were combined into single datetime columns: `OUTAGE.START` and `OUTAGE.RESTORATION`.

#### 3. Dropping Redundant Columns
After combining the date and time into single columns, the original columns (`OUTAGE.START.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE`, and `OUTAGE.RESTORATION.TIME`) became redundant. These columns were dropped from the dataset to reduce clutter and maintain only the essential information.

#### 4. Result
After these cleaning steps, the dataset now contains consistent datetime columns (`OUTAGE.START` and `OUTAGE.RESTORATION`), and redundant columns were removed. The data is now structured and ready for further analysis to explore the characteristics of major power outages.

Below is a preview of the cleaned and filtered outage dataset after selecting only the most relevant columns and performing necessary data cleaning steps. 
{% raw %}
|    |   YEAR |   MONTH | U.S._STATE   | CLIMATE.REGION     | CLIMATE.CATEGORY   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED |   TOTAL.CUSTOMERS |   TOTAL.PRICE |   TOTAL.SALES |   TOTAL.REALGSP |   PC.REALGSP.STATE |   POPDEN_URBAN |   POPULATION | OUTAGE.START        | OUTAGE.RESTORATION   | Severe   |
|---:|-------:|--------:|:-------------|:-------------------|:-------------------|:-------------------|:------------------------|------------------:|---------------------:|------------------:|--------------:|--------------:|----------------:|-------------------:|---------------:|-------------:|:--------------------|:---------------------|:---------|
|  1 |   2011 |       7 | Minnesota    | East North Central | normal             | severe weather     | nan                     |              3060 |                70000 |       2.5957e+06  |          9.28 |       6562520 |          274182 |              51268 |           2279 |  5.34812e+06 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  | True     |
|  2 |   2014 |       5 | Minnesota    | East North Central | normal             | intentional attack | vandalism               |                 1 |                  nan |       2.64074e+06 |          9.28 |       5284231 |          291955 |              53499 |           2279 |  5.45712e+06 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  | False    |
|  3 |   2010 |      10 | Minnesota    | East North Central | cold               | severe weather     | heavy wind              |              3000 |                70000 |       2.5869e+06  |          8.15 |       5222116 |          267895 |              50447 |           2279 |  5.3109e+06  | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  | True     |
|  4 |   2012 |       6 | Minnesota    | East North Central | normal             | severe weather     | thunderstorm            |              2550 |                68200 |       2.60681e+06 |          9.19 |       5787064 |          277627 |              51598 |           2279 |  5.38044e+06 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  | True     |
|  5 |   2015 |       7 | Minnesota    | East North Central | warm               | severe weather     | nan                     |              1740 |               250000 |       2.67353e+06 |         10.43 |       5970339 |          292023 |              54431 |           2279 |  5.48959e+06 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  | True     |
{% endraw %}

### Univariate Analysis
In this analysis, we focus exclusively on severe power outages, defined as outages lasting longer than 24 hours and affecting over 50,000 people

#### Distribution of Severe Outage Durations
The Outage Duration Distribution histogram highlights the distribution of severe power outages by their duration. The majority of severe outages last under 5,000 minutes (approximately 3.5 days). However, in an extreme case, one outage lasts over 34,000 minutes (nearly 24 days).
<iframe
  src="assets/duration_distribution_fig.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

#### Distribution of Severe Outages by Region
The Severe Outages by Region bar chart displays the distribution of severe power outages across various climate regions, ordered from the highest to the lowest number of occurrences. The data reveals that the Northeast experiences the most severe outages, while the Southwest has the fewest.
<iframe
  src="assets/severe_region_fig.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

### Bivariate Analysis
#### Number of Customer by Cause Category
The Number of Customers Affected by Cause Category histogram provides insights into how different causes of power outages impact the number of customers. Some causes affect significantly larger portions of the population than others. For instance, natural disasters like hurricanes and storms are associated with more widespread disruptions, leading to a higher number of affected customers, while technical issues tend to affect fewer people.
<iframe
  src="assets/customer_cause_count_fig.html"
  width="700"
  height="450"
  frameborder="0"
></iframe>

### Aggregates
#### Mean Outage Duration by Region and Cause Category
The pivot table below shows the mean outage duration for each climate region and cause category in the dataset. It is created by grouping the data based on the climate region and cause of the outage, with the values representing the average outage duration in minutes for each combination.
{% raw %}
| CLIMATE.REGION     |   equipment failure |   intentional attack |   severe weather |   system operability disruption |
|:-------------------|--------------------:|---------------------:|-----------------:|--------------------------------:|
| Central            |                   0 |                    0 |          5306.14 |                         3137    |
| East North Central |                   0 |                    0 |          4732.47 |                         2610    |
| Northeast          |                   0 |                    0 |          5578.22 |                         1732    |
| Northwest          |                   0 |                    0 |          5594.45 |                            0    |
| South              |                   0 |                    0 |          6705.44 |                         2572.75 |
| Southeast          |                   0 |                    0 |          4486.18 |                            0    |
| Southwest          |                   0 |                    0 |          2544.2  |                            0    |
| West               |                1914 |                 3100 |          6635.85 |                            0    |
{% endraw %}

## Assessment of Missingness
## Hypothesis Testing
## Framing a Prediction Problem
## Baseline Model
## Final Model
## Fairness Analysis