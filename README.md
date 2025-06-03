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
In my exploratory data analysis, I first perform univariate analysis to examine the distribution of single variables. 

First, I wanted to see how the number of outages has changed over time.
<iframe
  src="assets/outage_over_time.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
I also wanted to see the distribution of major causes of power outages.
<iframe
  src="assets/major_causes.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
Then, I wanted to see the distribution of the number of outages by each U.S. state. 
<iframe
  src="assets/map1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis
I conducted many bivariate analyses, and the most significant results are shown below.

I examined the relationship between Outage Duration and Customers Affected, two metrics of the severity of a power outage. I expected there to be a positive correlation, since major outages likely affect a lot of customers and have a long duration, but there was variability within this. There are many outages that affected a lot of customers but were not as long, indicating that Customers Affected might be a better metric for measuring outage severity.
<iframe
  src="assets/duration_cust.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The plot below shows the relation between outage duration and cause category. It shows that some of the outages with the longest duration were due to a fuel supply emergency.
<iframe
  src="assets/duration_cause.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Grouping and Aggregates
I grouped by NERC Region and then performed an aggregate function mean() to get the average severity metrics for each region. The severity metrics are Outage Duration, Customers Affected, and Demand Loss. The first few rows of this DataFrame are shown below: 

| NERC.REGION   |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED |   DEMAND.LOSS.MW |
|:--------------|------------------:|---------------------:|-----------------:|
| ASCC          |           nan     |                14273 |           35     |
| ECAR          |          5603.31  |               256354 |         1314.48  |
| FRCC          |          4271.12  |               375007 |         1072.6   |
| FRCC, SERC    |           372     |                  nan |          nan     |
| HECO          |           895.333 |               126729 |          466.667 |

I also performed grouping with a pivot table, on Climate Region and Cause Category to see which regions experienced severe weather outages the most. The first few rows of this data frame are shown below:

| CLIMATE.REGION     |   equipment failure |   fuel supply emergency |   intentional attack |   islanding |   public appeal |   severe weather |   system operability disruption |
|:-------------------|--------------------:|------------------------:|---------------------:|------------:|----------------:|-----------------:|--------------------------------:|
| Central            |                   7 |                       4 |                   38 |           3 |               2 |              135 |                              11 |
| East North Central |                   3 |                       5 |                   20 |           1 |               2 |              104 |                               3 |
| Northeast          |                   5 |                      14 |                  135 |           1 |               4 |              176 |                              15 |
| Northwest          |                   2 |                       1 |                   89 |           5 |               2 |               29 |                               4 |
| South              |                  10 |                       7 |                   28 |           2 |              42 |              113 |                              27 |
