# Mobility Trend Analysis during Covid-19 Pandemic

## Motivation

This project analyzes different trend data, such as mobility and stringency policies, as they relate to the ongoing COVID-19 Pandemic in the United States. The datasets were chosen given their availability and granularity of daily measurements. Our goal was to better understand whether there were relationships, specifically correlation, between mobility, stringency, COVID-19 severity measurements (cases, epidemiological growth, etc).

## Data Sources

- COVID-19 Data Repository by the Center for Systems Science and Engineering (CSSE) at Johns Hopkins University

- A [data repository](https://github.com/CSSEGISandData/COVID-19) for the 2019 Novel Coronavirus Visual Dashboard operated by the Johns Hopkins University Center for Systems Science and Engineering. The datasets were accessed via a Github URL via pd.read_csv(). The dataset is updated daily, although when merging with the other datasets it is limited to 02/15/202 - 04/02/2021. Important variables include location, cases, and date. Documentation is available at the link above. 

- Google Mobility Dataset
    [The Google Mobility dataset](https://www.google.com/covid19/mobility/) contains the daily percent changes from the baseline of different destinations by region. A baseline represents the median value of the data from Jan 3 to Feb 6, 2020, and each day of the week has different baselines. The shape of the dataset is 1,052,336 rows by 15 columns after the 2020 dataset and 2021 dataset were concatenated. The size of the datasets is 106.7MB in total. Important variables include date, region, and the mobility percent change from baseline of each destination. Documentation is available [here](https://support.google.com/covid19-mobility/answer/9825414?hl=en&ref_topic=9822927). 

- Oxford COVID-19 Government Response Tracker (State-Level)
   [The Oxford COVID-19 Government Response Tracker dataset](https://raw.githubusercontent.com/OxCGRT/USA-covid-policy/master/data/OxCGRT_US_latest.csv) includes the lockdown stringency index and lockdown policy index as well as the cumulative number of COVID-19 cases and deaths. The stringency index is calculated with sub-indices such as school closures, workplace closures, cancellation of public events. Sub-indices are ordinal variables and vary in their domain as well as their qualitative description. For instance, the school closure index is ranked between 0 and 3, 0 being no measures and 3 being closing all educational levels. The datasets were accessed via a Github URL via pd.read_csv(). The shape of the dataset is 26,208 rows by 72 columns. The dataset is updated daily, although when merging with the other datasets it is limited to 02/15/2020 - 04/02/2021. Important variables include date, region, confirmed cases, confirmed deaths, and stringency index. Documentation for interpretation is available [here](https://github.com/OxCGRT/covid-policy-tracker/blob/master/documentation/interpretation_guide.md), and methodology for calculating indices is available [here](https://github.com/OxCGRT/covid-policy-tracker/blob/master/documentation/index_methodology.md). 

## Data Manipulation

First, we concatenated the Google mobility datasets, as the mobility datasets were divided by year (i.e., 2020 and 2021). The datasets were in the same format, so we used pd.concat() to concatenate the 2020 and 2021 data. 

For the line plots of Figure 1, 2, and 3, we used 7-day rolling data to reduce the noisiness of the mobility data by using .rolling() in Pandas. For the choropleth maps of Figure 4 and 5, we used monthly average values of the mobility changes by using .groupby() in Pandas after extracting the month values from the date column by using .dt.to_period(). 

The COVID-19 cases dataset provided a simple running total of positive cases. The first step in our analysis was to isolate the number of new cases per day. This also allowed us to calculate a simple epidemiological growth factor by looking at the daily change in new cases. We observed that the data around cases was rather noisy, so we measured the 7 day simple moving average of new cases using the .rolling() function in Pandas. We also knew that COVID-19 has a period between exposure, symptom onset, and a positive test. Meaning reported cases actually lag transmission. In order to try and accurately measure when infection happened, we shifted new cases forward by 7, 14, and 21 days.  This was done using the .shift() functionality in Pandas. We also created another variable titled “mobility_index,” which was a simple, un-weighted average across all the mobility measurements. This ended up correlating nicely across the individual variables with the exception of residential, which was desired because the index should capture time outside of the house.  

## Analysis

### Mobility trend vs. Stringency index (National Level)

![mobility%](https://user-images.githubusercontent.com/58793983/162800937-76f17bdb-f2a4-4238-96b8-77c442922013.png)

We investigated the U.S. nation-level mobility data. Figure 1 indicates that the mobility in the retail and recreation areas, workplaces, transit stations decreased and remained generally lower than the baseline during the pandemic, whereas the mobility in the residential areas increased and remained slightly higher.

Interestingly, right after the national emergency was declared, there was a sudden increase in mobility at grocery stores and pharmacies, followed by a decrease to approximately 20 percent below baseline. The initial increase in mobility at grocery stores and pharmacies might be explained by the panic buying seen during the early stage of the pandemic.

The mobility trend in parks has the most distinctive fluctuation. The difference between the lowest percentage change to the highest percentage change is bigger than any other category. In Figure 2, we look more closely at the data. The mobility in parks was initially increasing, dropped with the increase of lockdown stringency index, then went up until August 2020, decreased until March 2021, and now is going back up again.

![fig1](https://user-images.githubusercontent.com/58793983/162817266-5e07df00-cf05-4238-b593-7827a1e86915.png)


We looked into different parameters and graphs to investigate whether this trend is related to COVID-19. A moderate negative correlation was observed between parks and daily cases and deaths (-0.48, -0.58, respectively). However, we can see in Figure 3 that the downward trend of mobility at parks started before the surge in COVID-19 cases.

![fig2](https://user-images.githubusercontent.com/58793983/162817310-ddc8a592-dfe3-45c2-af77-0a6fc8122b4e.png)

This suggests that COVID-19 is not wholly responsible for the large mobility fluctuations seen at parks. Given how the baselines are determined and the general shape of the trendline (i.e., below baseline in the fall/winter, and above baseline in the spring/summer), it is reasonable to speculate that the mobility at parks is correlated with weather. We were unable to obtain mobility data from previous years for comparison. However, by using state-level data, we may be able to find some insight as to whether the mobility fluctuations seen at parks are related to weather.

### Mobility trend vs. Stringency index (State Level)

The choropleth maps in Figure 4 display the changes in the monthly average mobility trend at the state level. We observe a significant nationwide decrease in the average mobility in the retail and recreation destinations for April 2020, following the increased lockdown stringency in the previous month. 

<img width="711" alt="Screen Shot 2022-04-11 at 12 40 04 PM" src="https://user-images.githubusercontent.com/58793983/162817341-166d4c99-0576-4b3f-931f-45f4c2c1678b.png">

The mobility at parks at the state level also shows a slightly different trend from some of the other destinations. The mobility trends are homogenous across the country at destinations such as residential areas and workplaces in general; however, this is not so true for parks. Where parks are concerned, northern regions of the country show bigger changes in mobility than southern regions (see Figure 5). 

We speculate that this could be related to how the baselines were set. The baselines of Google's mobility data were set in January and February of 2020. It is reasonable to assume that during the winter when the baseline values were set, the mobility at parks in the northern regions would have been lower than the mobility at parks in southern regions because of the cold weather. 

The difference in mobility percentage changes at parks between northern regions and southern regions indicates that the mobility at parks may be related to the weather when the baseline was set. Although more analysis would need to be done to make this claim, it is plausible that the large fluctuations in colder regions show that the mobility at parks followed the natural mobility fluctuation due to the weather.

### Mobility trend vs. lockdown policy

To explore the relationship between the policy indices of the Oxford COVID-19 Government Response dataset and mobility by destination, we created a heatmap of Pearson correlation coefficients (Figure 6). The destinations are on the x-axis and the policy indices are on the y-axis of the heatmap.

<img width="983" alt="Screen Shot 2022-04-11 at 12 40 58 PM" src="https://user-images.githubusercontent.com/58793983/162817359-5eed544c-5ffd-4285-ab9e-d2e8d78986a2.png">

We observe moderate correlations between the mobility and the lockdown policies. One of the higher negative correlations is seen between the stringency index and the mobility changes at retail and recreation places. Workplace closing has stronger correlations with the mobility of each destination except parks. 

The mobility at parks is not strongly correlated to any of the policies in general. This also suggests that mobility at parks may not really be affected by the COVID-19 pandemic. Additionally, any significant correlation was found between mobility and economic support, such as income support, or health related policies, such as testing policy. 
