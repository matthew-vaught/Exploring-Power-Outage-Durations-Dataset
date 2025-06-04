# Exploring Power Outage Durations
Project for DSC80 at UCSD

By Matthew Vaught

# Introduction
In this project, I explored a dataset that contained information about major power outages that occured in the United States between January 2000 and July 2016. These power outages are defined as being either large enough to have impacted at least 50,000 people or creating an unexpected energy loss of at least 300 MW. This dataset can be found and accessed through Purdue's Laboratory for Advancing Sustainable Critical Infrastructure [(https://engineering.purdue.edu/LASCI/research-data/outages)](https://engineering.purdue.edu/LASCI/research-data/outages).

This dataset initially contained 1534 rows and 57 columns corresponding to 1534 unique major power outages and 57 different features related to each power outage, such as climate and geographical descriptions, economic and population statistics, and severity statistics.

This dataset is extremely useful in terms of mitigating the effects of future power outages and attempting to understand the characteristics of prior outages. The information provided in this dataset is more than sufficient to create a good understanding of the causes and effects of large power outages, in order to hopefully learn from these issues and create a safer, more reliable energy network. For my project in particular, I will be exploring the task of trying to predict the outage duration of a major power outage, given some information about the causes and characteristics of the outage. This is an extremely important question task to tackle because if we are able to predict the duration of a major power outage right as it happens, relief efforts and general preparation for the ensuing outage can be dramatically improved.

Although the original dataset contains 57 unique features, I will be selecting a smaller subset of these features to investigate in my analysis. The features I chose to use are as following:

|Column                    |Description                                                                                                 |
|---                       |---                                                                                                         |
|`'YEAR'`                  |Year in which the outage event occurred                                                                      |
|`'MONTH'`                 |Month (numeric) in which the outage event occurred                                                           |
|`'U.S._STATE'`            |Two‐letter U.S. state code where the outage event took place                                                  |
|`'NERC.REGION'`           |North American Electric Reliability Corporation region encompassing the affected area                         |
|`'CLIMATE.REGION'`        |U.S. climate region (as defined by NOAA’s nine‐region scheme) covering the state where the outage occurred     |
|`'OUTAGE.START.DATE'`      |Calendar date (YYYY‐MM‐DD) on which the outage began                                                         |
|`'OUTAGE.START.TIME'`      |Time of day (HH:MM:SS) when the outage event started                                                          |
|`'OUTAGE.RESTORATION.DATE'`|Calendar date (YYYY‐MM‐DD) when power was fully restored to all customers                                      |
|`'OUTAGE.RESTORATION.TIME'`|Time of day (HH:MM:SS) when power restoration was completed                                                   |
|`'CAUSE.CATEGORY'`        |Broad classification of what triggered the outage (e.g., Weather, Equipment Failure, Human Error, Cyberattack)|
|`'HURRICANE.NAMES'`       |Name of the hurricane responsible for the outage (if applicable; otherwise blank/NA)                           |
|`'OUTAGE.DURATION'`       |Total elapsed time of the outage (in minutes)                                                                 |
|`'DEMAND.LOSS.MW'`        |Amount of unplanned firm load loss during the event (in megawatts)                                            |
|`'CUSTOMERS.AFFECTED'`    |Number of end‐use customers that lost power during the outage                                                 |
|`'TOTAL.CUSTOMERS'`       |Annual total number of electricity customers served in the state at the time of the outage                     |

# Data Cleaning and Exploratory Data Analysis
The first step that I completed in this process was cleaning the data to prepare it for future analysis.
## Cleaning
1. I started by dropping unnecessary columns and only keeping the features that I will use in my analysis. These are: `YEAR`, `MONTH`, `U.S._STATE`, `NERC.REGION`, `CLIMATE.REGION`,  
`OUTAGE.START.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE`, `OUTAGE.RESTORATION.TIME`,  
`CAUSE.CATEGORY`, `HURRICANE.NAMES`, `OUTAGE.DURATION`, `DEMAND.LOSS.MW`, `CUSTOMERS.AFFECTED`, `TOTAL.CUSTOMERS`

2. Next, I set the datatype of the `DEMAND.LOSS.MW` column to float, converting it from string objects because I wanted to analyze these values in the future.

3. Next, I combined the `OUTAGE.START.DATE` and `OUTAGE.START.TIME` columns into one Timestamp object, stored in my new `OUTAGE.START` column. I did the same process for `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.TIME`, while dropping all of the old columns because everything I need is in `OUTAGE.START` and `OUTAGE.RESTORATON`.

4. Then, I checked the three columns that indicate outage severity, `OUTAGE.DURATION`, `CUSTOMERS.AFFECTED`, and `DEMAND.LOSS.MW` for values of 0, which likely indicate instance of missing data since major outages wouldn't have a duration of 0 minutes, 0 customers affected, or 0 MW of energy lost. I replaced the values in any of these instances with np.nan.

The first few rows of this cleaned DataFrame are shown below, with a portion of columns selected.

| OBS | YEAR | MONTH | U.S._STATE | NERC.REGION | CUSTOMERS.AFFECTED | TOTAL.CUSTOMERS | OUTAGE.START         | OUTAGE.RESTORATION        |
|-----|------|-------|------------|-------------|--------------------|-----------------|----------------------|---------------------------|
| 1   | 2011 | 7     | Minnesota  | MRO         | 70000.0            | 2595696         | 2011-07-01 17:00:00  | 2011-07-03 20:00:00       |
| 2   | 2014 | 5     | Minnesota  | MRO         | NaN                | 2640737         | 2014-05-11 18:38:00  | 2014-05-11 18:39:00       |
| 3   | 2010 | 10    | Minnesota  | MRO         | 70000.0            | 2586905         | 2010-10-26 20:00:00  | 2010-10-28 22:00:00       |
| 4   | 2012 | 6     | Minnesota  | MRO         | 68200.0            | 2606813         | 2012-06-19 04:30:00  | 2012-06-20 23:00:00       |
| 5   | 2015 | 7     | Minnesota  | MRO         | 250000.0           | 2673531         | 2015-07-18 02:00:00  | 2015-07-19 07:00:00       |


## Exploratory Data Analysis

### Univariate Analysis
I performed univariate analysis on the dataset to get a better idea of the characteristics of individual columns. 

First, I wanted to see what the distributions of my three severity metrics (outage duration, demand loss, number of customers affected) looked like. In order to do this, I binned the values of each column and created a histogram to visualize how many observations were in each bin. This first plot shows how most of the outage durations are relatively short, lasting less than 1000 minutes. 
<iframe
  src="assets/binned_outage_duration_hist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This next plot shows how the majority of the major power outages experienced a demand loss between 100-500 MW, with fewer outages having either really small or really large demand losses.
<iframe
  src="assets/binned_demand_loss_hist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This plot shows how the majority of the major power outages affected between 50,000-250,000 customers, with very few outages affecting less than 10,000 customers.
<iframe
  src="assets/binned_customers_affected_hist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I also wanted to hone in on outage duration in particular to see how the average outage duration has changed over time. This plot shows that generally, outage durations have decreased over the years.
<iframe
  src="assets/outage_duration_by_year_line_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis
Next, I conducted many bivariate analyses to get a better understanding of what features were correlated with each other, and the most significant results are shown below.

I first examined the relationship between the duration of the power outage and the number of customers served within the state that the power outage occured. In order to do this, I binned the `TOTAL.CUSTOMERS` column into 5 quantiles, then created a histogram with the average outage durations for each quantile. This plot shows that in states with either a very low or a very high number of customers served, the average outage duration is slightly lower.
<iframe
  src="assets/outage_duration_v_customers_served_hist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The next plot below is a box-and-whisker plot that shows the distribution of outage durations for each of the different values in the `CAUSE.CATEGORY` column. Here it can be seen that causes such as severe weather, public appeals, and fuel supply emergencies have the highest average outage durations.
<iframe
  src="assets/outage_duration_v_cause_category_boxandwhisk.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Grouping and Aggregates
I first created a pivot table indexed by `CLIMATE.REGION` which shows the average severity metrics for each climate region. The severity metrics are Outage Duration, Customers Affected, and Demand Loss. The first few rows of this DataFrame are shown below: 

| CLIMATE.REGION      | CUSTOMERS.AFFECTED | DEMAND.LOSS.MW | OUTAGE.DURATION |
|---------------------|--------------------|----------------|-----------------|
| Central             | 1.20e+06           | 11000.0        | 23773.0         |
| East North Central  | 2.10e+06           | 11000.0        | 108653.0        |
| Northeast           | 3.13e+06           | 22934.0        | 60480.0         |
| Northwest           | 7.00e+05           | 1600.0         | 22769.0         |
| South               | 2.50e+06           | 8087.0         | 28170.0         |

I also created a pivot table indexed by `CAUSE.CATEGORY` which again shows the average severity metrics, but this time for each cause category. The severity metrics are Outage Duration, Customers Affected, and Demand Loss. This entire DataFrame is shown below:

| CAUSE.CATEGORY                  | CUSTOMERS.AFFECTED | DEMAND.LOSS.MW | OUTAGE.DURATION |
|---------------------------------|--------------------|----------------|-----------------|
| equipment failure               | 105450.59          | 380.00         | 1850.56         |
| fuel supply emergency           | 1.00               | 634.17         | 13484.03        |
| intentional attack              | 18753.42           | 94.06          | 521.93          |
| islanding                       | 7232.72            | 441.89         | 200.55          |
| public appeal                   | 15999.40           | 2818.32        | 1468.45         |
| severe weather                  | 190971.94          | 633.16         | 3899.71         |
| system operability disruption   | 211066.02          | 928.90         | 747.09          |

# Assessment of Missingness

## NMAR Analysis
With many columns in this dataset containing missing values, one column that is likely NMAR is `DEMAND.LOSS.MW`. This is because the description in the data dictionary for this variable explicity warns about the potentially inaccurate recordings, stating that although the value is supposed to be measured as peak demand it is often inaccurately recorded as total demand. This indicates that the data collection for this variable in particle might not be entirely accurate. Also, logically it would make sense for this column to be NMAR because extremely high demand loss values could be less likely to be reported in fear of the negative publicity it would bring, and extremely low values could also be less likely to be reported since they might be considered negligible.

Additional data I could collect to determine if `DEMAND.LOSS.MW` is actually MAR is to collect the company responsible for reporting each outage, and then perform further analysis to see whether the missingness of the demand loss values is dependent on the company, because some companies might be more prone to innaccurately providing the data or even outright excluding the data if it is extreme on either end.

## Missingness Dependency
When testing for missingness dependency, I will be focusing on the column `OUTAGE.DURATION`. I will test this against two other columns, `NERC.REGION` and `MONTH` in order to see if the missingness of the outage duration values depends on either of these other two columns.

### NERC Region
First, I will examine the distribution of NERC Regions when the outage duration values are missing vs not missing.

**Null Hypothesis:** The distribution of the NERC Region column is the same when Outage Duration is missing vs not missing.

**Alternate Hypothesis:** The distribution of the NERC Region column is different when Outage Duration is missing vs not missing. 

Here is the distribution of the Nerc Region column when Outage Duration is missing vs not missing.
<iframe
  src="assets/nerc_missingness_barh.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I found an observed TVD of 0.145 which has a p value of 0.036. The empirical distribution of the TVDs is shown below. At this value, I decide to reject the null hypothesis in favor of the alternate hypothesis, indicating that the distribution of the NERC Region column is statistically different when Outage Duration is missing vs not missing. This suggests that the missingness of the outage duration values is dependent on the NERC Region column.
<iframe
  src="assets/nerc_missingness_pval.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Month
Next, I examined whether the missingness of Outage Duration values depends on the `MONTH` column.

**Null Hypothesis:** The distribution of the Month column is the same when Outage Duration is missing vs not missing.

**Alternate Hypothesis:** The distribution of the Month column is different when Outage Duration is missing vs not missing. 

Here is the distribution of the Month column when Outage Duration is missing vs not missing.
<iframe
  src="assets/month_missingness_barh.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I found an observed TVD of 0.13 which has a p value of 0.26. The empirical distribution of the TVDs is shown below. At this value, I fail to the null hypothesis in favor of the alternate hypothesis, indicating that the distribution of the Month column is not statistically different when Outage Duration is missing vs not missing.
<iframe
  src="assets/month_missingness_pval.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

# Hypothesis Testing
I will be testing whether the average outage duration for major outages caused by hurricanes is greater than the average outage duration for major outages not caused by hurricanes. The relevant columns for this test are `OUTAGE.DURATION` and `HURRICANE.NAMES`. Any values in the `HURRICANE.NAMES` column that are not missing indicate that the outage was caused by a hurricane, and all values that are missing indicate the outage was not caused by a hurricane.

**Null Hypothesis:** On average, the duration of outages caused by hurricanes is the same as the duration of outages not caused by hurricanes.

**Alternate Hypothesis:** On average, the duration of outages caused by hurricanes is greater than the duration of outages not caused by hurricanes.

**Test Statistic:** Difference in means. Specifically, the mean outage duration of hurricanes - mean outage duration of non hurricanes.

I performed a permutation test with 10,000 iterations in order to produce an empirical distribution of the test statisic under the null hypothesis. 

The p-value that I got was 0.0, so at a significance level of 0.05, we reject the null hypothesis. Because these results are statistically significant, we can conclude that the average outage duration of major outages caused by hurricanes is indeed greater than the average outage duration of major outages not caused by hurricanes.

The plot below shows the observed difference against the empirical distribution of differences from the permutation tests.
<iframe
  src="assets/hurricane_mean_diff_perm_test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
