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

