This is a project for DSC 80 at UCSD.
\
By: Sarah He, Hao Zhang

## Introduction
Power outages are significant events that disrupt lives, economies, and critical infrastructure. Understanding the characteristics of major power outages can help communities, policymakers, and utility companies better prepare for and mitigate their impact.

This project focuses on the question: **What are the characteristics of major power outages with higher severity, and what factors may indicate that a power outage would be severe?** For our purposes, a power outage is considered severe if it lasts over 24 hours (1440 minutes) and affects more than 50,000 people.

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

|    |   YEAR |   MONTH | U.S._STATE   | CLIMATE.REGION     | CLIMATE.CATEGORY   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED |   TOTAL.CUSTOMERS |   TOTAL.PRICE |   TOTAL.SALES |   TOTAL.REALGSP |   PC.REALGSP.STATE |   POPDEN_URBAN |   POPULATION | OUTAGE.START        | OUTAGE.RESTORATION   | Severe   |
|---:|-------:|--------:|:-------------|:-------------------|:-------------------|:-------------------|:------------------------|------------------:|---------------------:|------------------:|--------------:|--------------:|----------------:|-------------------:|---------------:|-------------:|:--------------------|:---------------------|:---------|
|  1 |   2011 |       7 | Minnesota    | East North Central | normal             | severe weather     | nan                     |              3060 |                70000 |       2.5957e+06  |          9.28 |       6562520 |          274182 |              51268 |           2279 |  5.34812e+06 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  | True     |
|  2 |   2014 |       5 | Minnesota    | East North Central | normal             | intentional attack | vandalism               |                 1 |                  nan |       2.64074e+06 |          9.28 |       5284231 |          291955 |              53499 |           2279 |  5.45712e+06 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  | False    |
|  3 |   2010 |      10 | Minnesota    | East North Central | cold               | severe weather     | heavy wind              |              3000 |                70000 |       2.5869e+06  |          8.15 |       5222116 |          267895 |              50447 |           2279 |  5.3109e+06  | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  | True     |
|  4 |   2012 |       6 | Minnesota    | East North Central | normal             | severe weather     | thunderstorm            |              2550 |                68200 |       2.60681e+06 |          9.19 |       5787064 |          277627 |              51598 |           2279 |  5.38044e+06 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  | True     |
|  5 |   2015 |       7 | Minnesota    | East North Central | warm               | severe weather     | nan                     |              1740 |               250000 |       2.67353e+06 |         10.43 |       5970339 |          292023 |              54431 |           2279 |  5.48959e+06 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  | True     |

### Univariate Analysis
In this analysis, we focus exclusively on severe power outages, defined as outages lasting longer than 24 hours and affecting over 50,000 people

#### Distribution of Severe Outage Durations
The Outage Duration Distribution histogram highlights the distribution of severe power outages by their duration. The majority of severe outages last under 5,000 minutes (approximately 3.5 days). However, in an extreme case, one outage lasts over 34,000 minutes (nearly 24 days).
<iframe
  src="assets/duration_distribution_fig.html"
  width="700"
  height="450"
  frameborder="0"
></iframe>

#### Distribution of Severe Outages by Region
The Severe Outages by Region bar chart displays the distribution of severe power outages across various climate regions, ordered from the highest to the lowest number of occurrences. The data reveals that the Northeast experiences the most severe outages, while the Southwest has the fewest.
<iframe
  src="assets/severe_region_fig.html"
  width="700"
  height="450"
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

### Interesting Aggregates
#### Mean Outage Duration by Region and Cause Category
The pivot table below shows the mean outage duration for each climate region and cause category in the dataset. It is created by grouping the data based on the climate region and cause of the outage, with the values representing the average outage duration in minutes for each combination.

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


## Assessment of Missingness
### NMAR Analysis
In the subset of columns we selected, the following columns exhibit missing values: `CAUSE.CATEGORY.DETAIL`, `CUSTOMERS.AFFECTED`, `TOTAL.PRICE`, `TOTAL.SALES`, `CLIMATE.REGION`.

We will now address the nature of missingness for each of these columns individually:

- `CAUSE.CATEGORY.DETAIL`
   Upon review, it is clear that the missing values in this column are **NOT NMAR**. The missingness is attributable to the hierarchical structure of the `CAUSE.CATEGORY`. Some cause categories simply do not require or have a detailed breakdown, thus explaining the absence of data in these cases.

- `TOTAL.PRICE` and `TOTAL.SALES`
   A deeper inspection reveals that the missing values for both `TOTAL.PRICE` and `TOTAL.SALES` are concentrated within the month of July 2016. This temporal pattern suggests that the missingness is not due to random factors, but rather to a specific event or condition in that particular period. Given the consistency of the missing values within this timeframe, the data appears to follow a time-based pattern and **NOT NMAR**.

- `CLIMATE.REGION`
   A closer examination of the missing data for `CLIMATE.REGION` shows that all missing values are concentrated in entries related to Hawaii. This geographic pattern indicates that the missingness is location-specific, rather than being related to the value of the variable itself. As a result, the missingness in this column is more likely **NOT NMAR** and can be attributed to the specific region in question.

- `CUSTOMERS.AFFECTED`
   Initially, the missingness in the `CUSTOMERS.AFFECTED` column appeared to be **NMAR**, as there were no apparent patterns or relationships with other variables that could explain the missing data. However, further investigation through hypothesis testing will be conducted in the next phase. The goal is to determine whether the missingness is indeed dependent on the variable's values, thereby confirming whether it is **NMAR** or if it can be explained by other observed factors, making it **MAR**.

In summary, while most of the missing data in the selected columns can be explained by either time, category, or region, further testing will be performed on **CUSTOMERS.AFFECTED** to determine its missingness mechanism. The majority of the columns exhibit patterns that suggest the missingness is **MAR** rather than **NMAR**.

### Missingness Dependency

In this analysis, we further investigate the missingness in the `CUSTOMERS.AFFECTED` column by performing permutation tests to assess the potential dependencies between the missingness of this column and other variables in the dataset. Specifically, we focus on two columns: `CLIMATE.CATEGORY` and `U.S._STATE`. We conduct the tests using the standard significance level of 0.05.

#### `CLIMATE.CATEGORY` on the Missingness of `CUSTOMERS.AFFECTED`

**Hypotheses**
- **Null Hypothesis (H₀)**: The missingness of `CUSTOMERS.AFFECTED` is not dependent on the values of `CLIMATE.CATEGORY`. The distribution of `CLIMATE.CATEGORY` remains unchanged regardless of whether `CUSTOMERS.AFFECTED` values are missing or not.
- **Alternative Hypothesis (H₁)**: The missingness of `CUSTOMERS.AFFECTED` is dependent on the values of `CLIMATE.CATEGORY`. The distribution of `CLIMATE.CATEGORY` differs between rows where `CUSTOMERS.AFFECTED` is missing and rows where it is not missing.

Given that `CLIMATE.CATEGORY` is a categorical variable, we apply the **Total Variation Distance** to test the hypothesis. TVD quantifies the difference between two probability distributions, making it suitable for comparing categorical distributions in the context of missingness.

<iframe
  src="assets/climate_category_customer_missing_fig.html"
  width="700"
  height="450"
  frameborder="0"
></iframe>

After conducting the permutation test, we obtain a **p-value of 0.604**. This result leads us to **fail to reject the null hypothesis**. Therefore, we conclude that there is no statistically significant difference in the distribution of `CLIMATE.CATEGORY` based on whether `CUSTOMERS.AFFECTED` is missing or not. Hence, the missingness of `CUSTOMERS.AFFECTED` does not appear to be dependent on the values of `CLIMATE.CATEGORY`.

#### `U.S._STATE` on the Missingness of `CUSTOMERS.AFFECTED`

**Hypotheses**
- **Null Hypothesis (H₀)**: The missingness of `CUSTOMERS.AFFECTED` is not dependent on the values of `U.S._STATE`. The distribution of `U.S._STATE` remains the same regardless of whether `CUSTOMERS.AFFECTED` values are missing or not.
- **Alternative Hypothesis (H₁)**: The missingness of `CUSTOMERS.AFFECTED` is dependent on the values of `U.S._STATE`. The distribution of `U.S._STATE` differs between rows where `CUSTOMERS.AFFECTED` is missing and rows where it is not missing.

As `U.S._STATE` is also a categorical variable, we again apply **Total Variation Distance** to test the hypothesis and compare the distributions of `U.S._STATE` for missing and non-missing `CUSTOMERS.AFFECTED` values.

<iframe
  src="assets/us_state_customer_missing_fig.html"
  width="700"
  height="450"
  frameborder="0"
></iframe>

The permutation test yields a **p-value of 0.0**. Given that the p-value is significantly below the 0.05 significance threshold, we **reject the null hypothesis** in favor of the alternative hypothesis. This suggests that the distribution of `U.S._STATE` is significantly different between rows with missing `CUSTOMERS.AFFECTED` values and those without, indicating a dependency between the missingness of `CUSTOMERS.AFFECTED` and the values of `U.S._STATE`. Consequently, we classify the missingness of `CUSTOMERS.AFFECTED` as **MAR (Missing at Random)**.

#### Conclusion
Through the permutation tests, we found that the missingness of `CUSTOMERS.AFFECTED` is not dependent on `CLIMATE.CATEGORY`, as evidenced by the p-value of 0.604. However, the missingness is dependent on `U.S._STATE`, with a p-value of 0.0 indicating a significant relationship. This suggests that the missing values in `CUSTOMERS.AFFECTED` may depend on the state in the U.S. where the event occurred.

## Hypothesis Testing
We categorize the `CLIMATE.REGION` column into two groups: North (East North Central, Northwest, Northeast) and South (South, Southeast, Southwest). We aim to test if there is a significant difference in the duration of severe weather outages between the North and South regions.

The hypotheses are defined as follows:
- **Null Hypothesis (H₀)**: On average, the duration of severe weather outages in the North is the same as in the South.
- **Alternative Hypothesis (H₁)**: On average, the duration of severe weather outages in the North is greater than in the South.

We will use the **difference of means** as the test statistic, where:
\
Test Statistic = Average outage duration in the North - Average outage duration in the South

The significance level is set to the standard value of 0.05.

<iframe
  src="assets/north_south_fig.html"
  width="700"
  height="450"
  frameborder="0"
></iframe>

Over 10,000 simulations, the resulting p-value is **0.29**. Given that the p-value exceeds the significance level of 0.05, we **fail to reject the null hypothesis**. We do not have sufficient statistical evidence to claim that the average duration of severe weather outages differs between the North and South regions. The data suggests that the outage durations are similar on average across both regions.

## Framing a Prediction Problem
The goal of our prediction model is to determine whether a power outage will be classified as "Severe." A power outage is defined as severe if it lasts for over 24 hours and affects more than 50,000 people. This is a **binary classification problem**, where the model's output is either "Severe" or "Not Severe." Given the binary nature of the problem, we will use **logistic regression** as the model, which is a suitable choice for predicting binary outcomes.

### Evaluation Metrics
To assess the model's performance, we will use three key evaluation metrics: **accuracy**, **precision**, and **recall**. 

- **Accuracy** measures the overall percentage of correct predictions. It is useful for providing a general sense of how well the model is performing.
- **Precision** focuses on the proportion of positive predictions (Severe outages) that were correctly identified. This is important because we want to minimize false positives, which are instances where the model incorrectly predicts a severe outage.
- **Recall** measures the proportion of actual severe outages that were correctly predicted. This is critical as we want to ensure that most severe outages are identified by the model, reducing the number of false negatives.

By using these three metrics together, we can gain a balanced understanding of the model's performance, particularly since there might be an imbalance between severe and non-severe outages.

### Features Used for Prediction

Specifically, the features that will be used to predict whether an outage is severe are:
- `YEAR`
- `MONTH`
- `U.S._STATE`
- `CLIMATE.CATEGORY`
- `CAUSE.CATEGORY`
- `TOTAL.CUSTOMERS`
- `POPDEN_URBAN`
- `POPULATION`
- `TOTAL.REALGSP`
- `PC.REALGSP.STATE`
- `OUTAGE.START`
- `CLIMATE.REGION`
- `TOTAL.PRICE`
- `TOTAL.SALES`

These features were chosen because they are available at the start of the outage and provide key context for predicting severity. For instance, `CLIMATE.CATEGORY` and `CAUSE.CATEGORY` help identify factors like storms or equipment failure, while `TOTAL.CUSTOMERS` and `POPULATION` indicate the potential scale and impact. The **time of prediction** will be at the start of the outage. By using these features with logistic regression, the model aims to predict whether an outage will be severe, aiding timely decision-making to mitigate its impact.

## Baseline Model
Our baseline model aims to predict the severity of power outages using the selected features from the dataset. The model incorporates a mix of categorical and numerical features to capture diverse information about the outages. Below is the breakdown of feature types used:

**Categorical (Nominal) Features**  
- `YEAR` 
- `MONTH` 
- `U.S._STATE`  
- `CLIMATE.CATEGORY`  
- `CAUSE.CATEGORY`  
- `CLIMATE.REGION`  

These features were encoded using one-hot encoding to transform them into a format suitable for the logistic regression model.

**Temporal Feature**  
- `OUTAGE.START`  

This feature was processed to extract a meaningful component, hour of day, which might correlate with outage severity.

**Numerical Features**  
- `TOTAL.CUSTOMERS`  
- `POPULATION`  
- `TOTAL.REALGSP`  
- `TOTAL.PRICE`  
- `TOTAL.SALES`  
- `PC.REALGSP.STATE`  
- `POPDEN_URBAN`  

### Model Performance
The baseline model was evaluated using logistic regression, and its performance was assessed using a train-test split to ensure that results reflect the model's ability to generalize to unseen data. The metrics used to evaluate performance were **accuracy**, **precision**, and **recall**, which help measure overall correctness and the ability to identify severe outages effectively.

- **Accuracy:** 0.69
- **Precision:** 0.50
- **Recall:** 0.07

Although the accuracy seems acceptable, the low precision and recall indicate that the model struggles to identify severe outages effectively. This suggests that the baseline model is insufficient for practical use in its current form. However, these results provide a starting point for improvement, and we aim to enhance performance through better feature engineering and hyperparameter tuning.

## Final Model

### Feature Engineering
The existing numerical features were standardized. Standardization is a crucial preprocessing step because it ensures that the features are on the same scale, preventing any one feature from dominating the model due to its larger scale. 

### Feature Addition: Demand Pressure (Customer-to-Population Ratio)
We added a new feature to capture the pressure on the power infrastructure by highlighting areas with a higher customer demand relative to their population size. A higher ratio indicates a region where power infrastructure may be under more strain, which could lead to more severe outages.

This feature calculates the ratio of customers to the population size as: 
\
`demand_pressure` = `TOTAL.CUSTOMERS` / `POPULATION`

### Hyperparameter Tuning
To fine-tune the model, we used **Grid Search Cross-Validation (CV)** to systematically evaluate combinations of hyperparameters and identify the best configuration.

After performing Grid Search CV, the following hyperparameters produced the best performance for the logistic regression model:
- **C**: 10 (controls regularization strength)
- **class_weight**: 'balanced' (addresses class imbalance by assigning inverse proportional weights to the classes)
- **max_iter**: 500 (ensures convergence with sufficient iterations)
- **penalty**: 'l2' (applies L2 regularization to reduce overfitting)
- **solver**: 'saga' (efficient solver for large datasets with L2 penalty)

### Threshold Adjustment
To further enhance performance, we optimized the model’s decision threshold. Instead of using the default threshold of 0.5, we adjusted it to find a value that balanced **precision** and **recall** effectively. This adjustment was made using the precision-recall curve, ensuring that the model could achieve a better trade-off between identifying severe outages and minimizing false positives.

### Performance Improvement
Compared to the baseline model, the final model shows significant improvement across all key metrics.

**Baseline Model Performance**
- Testing Accuracy: 0.69
- Precision: 0.50
- Recall: 0.07

**Final Model Performance**
- Accuracy: 0.8527
- Precision: 0.7640
- Recall: 0.7556

The combination of feature engineering and hyperparameter tuning has led to an improvement in the model’s performance over the baseline, as it better accounts for the regional and infrastructural factors influencing power outage severity. Together, these changes resulted in a more accurate and reliable model for predicting severe power outages, enabling decision-makers to take timely action to mitigate the impact of these events.

## Fairness Analysis

We run a permutation test to assess the fairness of our model on different `CLIMATE.CATEGORY`
- **Group X**: Rows with `CLIMATE.CATEGORY` = "cold"
- **Group Y**: Rows with `CLIMATE.CATEGORY` = "normal"

**Test Statistic and Evaluation Metric**: Absolute difference in model accuracy between the two climate categories

**Hypotheses**
- **Null Hypothesis (H₀)**: There is no significant difference in model performance between 'normal' and 'cold' climate groups.
- **Alternative Hypothesis (H₁)**: There is a statistically significant difference in model performance between 'normal' and 'cold' climate groups, which would indicate potential model unfairness.

**Significance Level**: 0.05

<iframe
  src="assets/fairness_fig.html"
  width="700"
  height="450"
  frameborder="0"
></iframe>

The permutation test revealed an observed accuracy difference of 0.016 between "cold" and "normal" climate categories, with a p-value of 0.575. This high p-value suggests the performance variation is not statistically significant and likely attributable to random chance. We therefore fail to reject the null hypothesis, concluding that the model demonstrates consistent performance across climate categories, with no substantial evidence of unfairness or meaningful bias in its predictive accuracy.

## Conclusion
In this project, we aimed to explore the characteristics of major power outages with higher severity and identify factors that could indicate whether a power outage would be severe. Through steps of Exploratory Data Analysis, feature engineering, model training, and hypothesis testing, we developed a logistic regression model that provides reasonable predictions without significant bias between climate categories. Environmental and infrastructural factors, such as climate conditions, infrastructure type, and historical outage data, were found to correlate with outage severity. By identifying the key factors influencing outage severity, we gained valuable insights that could inform future preparedness and response strategies, helping to mitigate the impact of severe power outages.