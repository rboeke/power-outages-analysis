# Examining Power Outage Factors: When Will the Lights Go Out?
Rachel Boeke
boeker@umich.edu

Created for EECS 398 at the University of Michigan.

## Introduction
When will your next power outage be?
How can we predict (or even prevent) a power outage and its impact before it happens?
Examining some of the factors that play a role is one place to start.
Using [power outage data provided by researchers at Purdue University] (https://www.sciencedirect.com/science/article/pii/S2352340918307182#bib6), I wanted to answer the question:
* What are the factors that indicate a severe power outage may occur?
* In other words, what risk factors may an energy company want to look into when predicting the location and severity of its next major power outage?

##### About the Dataset:
* Number of observations/rows: 1534
* Number of columns: 55

##### Some of the relevant factors that may affect the severity of an outage include:
1. Location:
* U.S._STATE: The U.S. state where the outage occurred
* POSTAL.CODE: The two-letter postal abbreviation for the U.S. state where the outage occurred
* NERC.REGION: The North American Electric Reliability Corporation (NERC) regions involved in the outage event
2. Time:
* OUTAGE.START.DATE: This variable indicates the day of the year when the outage event started (as reported by the corresponding Utility in the region)
* OUTAGE.START.TIME: This variable indicates the time of the day when the outage event started (as reported by the corresponding Utility in the region)
3. Climate:
* CLIMATE.REGION: U.S. Climate regions as specified by National Centers for Environmental Information (nine climatically consistent regions in continental U.S.A.)
* ANOMALY.LEVEL: This represents the oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season. It is estimated as a 3-month running mean of ERSST.v4 SST anomalies in the Niño 3.4 region (5°N to 5°S, 120–170°W)
* CLIMATE.CATEGORY: This represents the climate episodes corresponding to the years. The categories—“Warm”, “Cold” or “Normal” episodes of the climate are based on a threshold of ± 0.5 °C for the Oceanic Niño Index (ONI)
4. Land-use characteristics
* PCT_LAND: Percentage of land area in the U.S. state as compared to the overall land area in the continental U.S. (in %)
* AREAPCT_URBAN: Percentage of the land area of the U.S. state represented by the land area of the urban areas (in %)
* PCT_WATER_TOT: Percentage of water area in the U.S. state as compared to the overall water area in the continental U.S. (in %)
7. Population:
* POPULATION: Population in the U.S. state in a year
* TOTAL.CUSTOMERS: Annual number of total customers served in the U.S. state

##### Relevant columns that characterize the severity of an outage include:
1. OUTAGE.DURATION: Duration of outage events (in minutes)
2. DEMAND.LOSS.MW: Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported]
3. CUSTOMERS.AFFECTED: Number of customers affected by the power outage event

Additional fields and field descriptions are provided [here] (https://www.sciencedirect.com/science/article/pii/S2352340918307182#bib6)

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning
To clean the data, I completed the following steps:
1. Combined OUTAGE.START.DATE and OUTAGE.START.TIME into one pd.Timestamp column, OUTAGE.START
2. Combined OUTAGE.RESTORATION.DATE and OUTAGE.RESTORATION.TIME into one pd.Timestamp column, OUTAGE.RESTORATION
3. Converted numeric fields from object to float data types
4. Replaced missing "NA" values with np.nan
5. Retained only relevant columns

The first 10 rows of the cleaned data are shown below:

'| U.S._STATE   | POSTAL.CODE   | NERC.REGION   |   MONTH | OUTAGE.START        | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   |   PCT_LAND |   AREAPCT_URBAN |   PCT_WATER_TOT |   RES.PRICE |   COM.PRICE |   IND.PRICE |   TOTAL.PRICE |   RES.SALES |   COM.SALES |   IND.SALES |   TOTAL.SALES |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   POPULATION |   TOTAL.CUSTOMERS |   YEAR | CAUSE.CATEGORY     |\n|:-------------|:--------------|:--------------|--------:|:--------------------|:-------------------|----------------:|:-------------------|-----------:|----------------:|----------------:|------------:|------------:|------------:|--------------:|------------:|------------:|------------:|--------------:|------------------:|-----------------:|---------------------:|-------------:|------------------:|-------:|:-------------------|\n| Minnesota    | MN            | MRO           |       7 | 2011-07-01 17:00:00 | East North Central |            -0.3 | normal             |    91.5927 |            2.14 |         8.40733 |       11.6  |        9.18 |        6.81 |          9.28 | 2.33292e+06 | 2.11477e+06 | 2.11329e+06 |   6.56252e+06 |              3060 |              nan |                70000 |  5.34812e+06 |       2.5957e+06  |   2011 | severe weather     |\n| Minnesota    | MN            | MRO           |       5 | 2014-05-11 18:38:00 | East North Central |            -0.1 | normal             |    91.5927 |            2.14 |         8.40733 |       12.12 |        9.71 |        6.49 |          9.28 | 1.58699e+06 | 1.80776e+06 | 1.88793e+06 |   5.28423e+06 |                 1 |              nan |                  nan |  5.45712e+06 |       2.64074e+06 |   2014 | intentional attack |\n| Minnesota    | MN            | MRO           |      10 | 2010-10-26 20:00:00 | East North Central |            -1.5 | cold               |    91.5927 |            2.14 |         8.40733 |       10.87 |        8.19 |        6.07 |          8.15 | 1.46729e+06 | 1.80168e+06 | 1.9513e+06  |   5.22212e+06 |              3000 |              nan |                70000 |  5.3109e+06  |       2.5869e+06  |   2010 | severe weather     |\n| Minnesota    | MN            | MRO           |       6 | 2012-06-19 04:30:00 | East North Central |            -0.1 | normal             |    91.5927 |            2.14 |         8.40733 |       11.79 |        9.25 |        6.71 |          9.19 | 1.85152e+06 | 1.94117e+06 | 1.99303e+06 |   5.78706e+06 |              2550 |              nan |                68200 |  5.38044e+06 |       2.60681e+06 |   2012 | severe weather     |\n| Minnesota    | MN            | MRO           |       7 | 2015-07-18 02:00:00 | East North Central |             1.2 | warm               |    91.5927 |            2.14 |         8.40733 |       13.07 |       10.16 |        7.74 |         10.43 | 2.02888e+06 | 2.16161e+06 | 1.77794e+06 |   5.97034e+06 |              1740 |              250 |               250000 |  5.48959e+06 |       2.67353e+06 |   2015 | severe weather     |'

### Univariate Analysis

I looked at the distribution of relevant variables. To start, I examined the severity metrics DEMAND.LOSS.MW, OUTAGE.DURATION, and CUSTOMERS.AFFECTED to see what might be considered a "severe" outage:


<iframe
  src="assets/file-name.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Framing a Prediction Problem


## Baseline Model


## Final Model


