

```python
# Dependencies
import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime
from census import Census
import seaborn as sns
import numpy as np
from scipy.stats import linregress
import requests

import gmaps
import gmaps.geojson_geometries
import gmaps.datasets
import plotly.plotly as py
import plotly.graph_objs as go

# Census API Key
from config import api_key
c = Census(api_key, year = 2013)

```


```python
# Store filepath in a variable
gun_file = "Resources/gun-violence-data_01-2013_03-2018.csv"
```


```python
# Read our data file with the pandas library
gun_file_df = pd.read_csv(gun_file)
gun_file_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>incident_id</th>
      <th>date</th>
      <th>state</th>
      <th>city_or_county</th>
      <th>address</th>
      <th>n_killed</th>
      <th>n_injured</th>
      <th>incident_url</th>
      <th>source_url</th>
      <th>incident_url_fields_missing</th>
      <th>...</th>
      <th>participant_age</th>
      <th>participant_age_group</th>
      <th>participant_gender</th>
      <th>participant_name</th>
      <th>participant_relationship</th>
      <th>participant_status</th>
      <th>participant_type</th>
      <th>sources</th>
      <th>state_house_district</th>
      <th>state_senate_district</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>461105</td>
      <td>1/1/13</td>
      <td>Pennsylvania</td>
      <td>Mckeesport</td>
      <td>1506 Versailles Avenue and Coursin Street</td>
      <td>0</td>
      <td>4</td>
      <td>http://www.gunviolencearchive.org/incident/461105</td>
      <td>http://www.post-gazette.com/local/south/2013/0...</td>
      <td>False</td>
      <td>...</td>
      <td>0::20</td>
      <td>0::Adult 18+||1::Adult 18+||2::Adult 18+||3::A...</td>
      <td>0::Male||1::Male||3::Male||4::Female</td>
      <td>0::Julian Sims</td>
      <td>NaN</td>
      <td>0::Arrested||1::Injured||2::Injured||3::Injure...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://pittsburgh.cbslocal.com/2013/01/01/4-pe...</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>460726</td>
      <td>1/1/13</td>
      <td>California</td>
      <td>Hawthorne</td>
      <td>13500 block of Cerise Avenue</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/460726</td>
      <td>http://www.dailybulletin.com/article/zz/201301...</td>
      <td>False</td>
      <td>...</td>
      <td>0::20</td>
      <td>0::Adult 18+||1::Adult 18+||2::Adult 18+||3::A...</td>
      <td>0::Male</td>
      <td>0::Bernard Gillis</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://losangeles.cbslocal.com/2013/01/01/man-...</td>
      <td>62.0</td>
      <td>35.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>478855</td>
      <td>1/1/13</td>
      <td>Ohio</td>
      <td>Lorain</td>
      <td>1776 East 28th Street</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/478855</td>
      <td>http://chronicle.northcoastnow.com/2013/02/14/...</td>
      <td>False</td>
      <td>...</td>
      <td>0::25||1::31||2::33||3::34||4::33</td>
      <td>0::Adult 18+||1::Adult 18+||2::Adult 18+||3::A...</td>
      <td>0::Male||1::Male||2::Male||3::Male||4::Male</td>
      <td>0::Damien Bell||1::Desmen Noble||2::Herman Sea...</td>
      <td>NaN</td>
      <td>0::Injured, Unharmed, Arrested||1::Unharmed, A...</td>
      <td>0::Subject-Suspect||1::Subject-Suspect||2::Vic...</td>
      <td>http://www.morningjournal.com/general-news/201...</td>
      <td>56.0</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>478925</td>
      <td>1/5/13</td>
      <td>Colorado</td>
      <td>Aurora</td>
      <td>16000 block of East Ithaca Place</td>
      <td>4</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/478925</td>
      <td>http://www.dailydemocrat.com/20130106/aurora-s...</td>
      <td>False</td>
      <td>...</td>
      <td>0::29||1::33||2::56||3::33</td>
      <td>0::Adult 18+||1::Adult 18+||2::Adult 18+||3::A...</td>
      <td>0::Female||1::Male||2::Male||3::Male</td>
      <td>0::Stacie Philbrook||1::Christopher Ratliffe||...</td>
      <td>NaN</td>
      <td>0::Killed||1::Killed||2::Killed||3::Killed</td>
      <td>0::Victim||1::Victim||2::Victim||3::Subject-Su...</td>
      <td>http://denver.cbslocal.com/2013/01/06/officer-...</td>
      <td>40.0</td>
      <td>28.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>478959</td>
      <td>1/7/13</td>
      <td>North Carolina</td>
      <td>Greensboro</td>
      <td>307 Mourning Dove Terrace</td>
      <td>2</td>
      <td>2</td>
      <td>http://www.gunviolencearchive.org/incident/478959</td>
      <td>http://www.journalnow.com/news/local/article_d...</td>
      <td>False</td>
      <td>...</td>
      <td>0::18||1::46||2::14||3::47</td>
      <td>0::Adult 18+||1::Adult 18+||2::Teen 12-17||3::...</td>
      <td>0::Female||1::Male||2::Male||3::Female</td>
      <td>0::Danielle Imani Jameison||1::Maurice Eugene ...</td>
      <td>3::Family</td>
      <td>0::Injured||1::Injured||2::Killed||3::Killed</td>
      <td>0::Victim||1::Victim||2::Victim||3::Subject-Su...</td>
      <td>http://myfox8.com/2013/01/08/update-mother-sho...</td>
      <td>62.0</td>
      <td>27.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 29 columns</p>
</div>




```python
# Create a new series for just the year
gun_file_df["year"] = pd.DatetimeIndex(gun_file_df["date"]).year
gun_file_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>incident_id</th>
      <th>date</th>
      <th>state</th>
      <th>city_or_county</th>
      <th>address</th>
      <th>n_killed</th>
      <th>n_injured</th>
      <th>incident_url</th>
      <th>source_url</th>
      <th>incident_url_fields_missing</th>
      <th>...</th>
      <th>participant_age_group</th>
      <th>participant_gender</th>
      <th>participant_name</th>
      <th>participant_relationship</th>
      <th>participant_status</th>
      <th>participant_type</th>
      <th>sources</th>
      <th>state_house_district</th>
      <th>state_senate_district</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>461105</td>
      <td>1/1/13</td>
      <td>Pennsylvania</td>
      <td>Mckeesport</td>
      <td>1506 Versailles Avenue and Coursin Street</td>
      <td>0</td>
      <td>4</td>
      <td>http://www.gunviolencearchive.org/incident/461105</td>
      <td>http://www.post-gazette.com/local/south/2013/0...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Adult 18+||1::Adult 18+||2::Adult 18+||3::A...</td>
      <td>0::Male||1::Male||3::Male||4::Female</td>
      <td>0::Julian Sims</td>
      <td>NaN</td>
      <td>0::Arrested||1::Injured||2::Injured||3::Injure...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://pittsburgh.cbslocal.com/2013/01/01/4-pe...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>1</th>
      <td>460726</td>
      <td>1/1/13</td>
      <td>California</td>
      <td>Hawthorne</td>
      <td>13500 block of Cerise Avenue</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/460726</td>
      <td>http://www.dailybulletin.com/article/zz/201301...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Adult 18+||1::Adult 18+||2::Adult 18+||3::A...</td>
      <td>0::Male</td>
      <td>0::Bernard Gillis</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://losangeles.cbslocal.com/2013/01/01/man-...</td>
      <td>62.0</td>
      <td>35.0</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>2</th>
      <td>478855</td>
      <td>1/1/13</td>
      <td>Ohio</td>
      <td>Lorain</td>
      <td>1776 East 28th Street</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/478855</td>
      <td>http://chronicle.northcoastnow.com/2013/02/14/...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Adult 18+||1::Adult 18+||2::Adult 18+||3::A...</td>
      <td>0::Male||1::Male||2::Male||3::Male||4::Male</td>
      <td>0::Damien Bell||1::Desmen Noble||2::Herman Sea...</td>
      <td>NaN</td>
      <td>0::Injured, Unharmed, Arrested||1::Unharmed, A...</td>
      <td>0::Subject-Suspect||1::Subject-Suspect||2::Vic...</td>
      <td>http://www.morningjournal.com/general-news/201...</td>
      <td>56.0</td>
      <td>13.0</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>3</th>
      <td>478925</td>
      <td>1/5/13</td>
      <td>Colorado</td>
      <td>Aurora</td>
      <td>16000 block of East Ithaca Place</td>
      <td>4</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/478925</td>
      <td>http://www.dailydemocrat.com/20130106/aurora-s...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Adult 18+||1::Adult 18+||2::Adult 18+||3::A...</td>
      <td>0::Female||1::Male||2::Male||3::Male</td>
      <td>0::Stacie Philbrook||1::Christopher Ratliffe||...</td>
      <td>NaN</td>
      <td>0::Killed||1::Killed||2::Killed||3::Killed</td>
      <td>0::Victim||1::Victim||2::Victim||3::Subject-Su...</td>
      <td>http://denver.cbslocal.com/2013/01/06/officer-...</td>
      <td>40.0</td>
      <td>28.0</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>4</th>
      <td>478959</td>
      <td>1/7/13</td>
      <td>North Carolina</td>
      <td>Greensboro</td>
      <td>307 Mourning Dove Terrace</td>
      <td>2</td>
      <td>2</td>
      <td>http://www.gunviolencearchive.org/incident/478959</td>
      <td>http://www.journalnow.com/news/local/article_d...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Adult 18+||1::Adult 18+||2::Teen 12-17||3::...</td>
      <td>0::Female||1::Male||2::Male||3::Female</td>
      <td>0::Danielle Imani Jameison||1::Maurice Eugene ...</td>
      <td>3::Family</td>
      <td>0::Injured||1::Injured||2::Killed||3::Killed</td>
      <td>0::Victim||1::Victim||2::Victim||3::Subject-Su...</td>
      <td>http://myfox8.com/2013/01/08/update-mother-sho...</td>
      <td>62.0</td>
      <td>27.0</td>
      <td>2013</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 30 columns</p>
</div>




```python
# Group dataframe by state
grouped_states = gun_file_df.groupby(["state"])
number_of_incidents = grouped_states.count()
incidents = number_of_incidents["incident_id"]
incidents.head()
```




    state
    Alabama        5471
    Alaska         1349
    Arizona        2328
    Arkansas       2842
    California    16306
    Name: incident_id, dtype: int64




```python
# Find the total number killed by state
sum_of_deaths = grouped_states["n_killed"].sum()
sum_of_deaths.head()
```




    state
    Alabama       1880
    Alaska         267
    Arizona       1094
    Arkansas       773
    California    5562
    Name: n_killed, dtype: int64




```python
# Create a new dataframe
state_summary = pd.DataFrame({"Number of Incidents" : incidents,
                             "Number of Deaths" : sum_of_deaths})
#state_summary = state_summary.reset_index(drop=True)
state_summary.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
    </tr>
    <tr>
      <th>state</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Alabama</th>
      <td>1880</td>
      <td>5471</td>
    </tr>
    <tr>
      <th>Alaska</th>
      <td>267</td>
      <td>1349</td>
    </tr>
    <tr>
      <th>Arizona</th>
      <td>1094</td>
      <td>2328</td>
    </tr>
    <tr>
      <th>Arkansas</th>
      <td>773</td>
      <td>2842</td>
    </tr>
    <tr>
      <th>California</th>
      <td>5562</td>
      <td>16306</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Add state as a column
state_summary["State_Name"] = state_summary.index
state_summary.head()

# Reset index
state_summary = state_summary.reset_index(drop=True)
state_summary.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
      <th>State_Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1880</td>
      <td>5471</td>
      <td>Alabama</td>
    </tr>
    <tr>
      <th>1</th>
      <td>267</td>
      <td>1349</td>
      <td>Alaska</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1094</td>
      <td>2328</td>
      <td>Arizona</td>
    </tr>
    <tr>
      <th>3</th>
      <td>773</td>
      <td>2842</td>
      <td>Arkansas</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5562</td>
      <td>16306</td>
      <td>California</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Look at gun violence over time to show there is a need to study this
# Group dataframe by year

grouped_years = gun_file_df.groupby(["year"])
incidents_in_years = grouped_years.count()
incidents_years = incidents_in_years["incident_id"]
incidents_years


```




    year
    2013      278
    2014    51854
    2015    53579
    2016    58763
    2017    61401
    2018    13802
    Name: incident_id, dtype: int64




```python
# Find the total number killed by year
sum_of_deaths_year = grouped_years["n_killed"].sum()
sum_of_deaths_year
```




    year
    2013      317
    2014    12557
    2015    13484
    2016    15066
    2017    15511
    2018     3533
    Name: n_killed, dtype: int64




```python
# Create a new dataframe with years
year_df = pd.DataFrame({
                        "Number of Incidents" : incidents_years,
                        "Number of Deaths": sum_of_deaths_year})
year_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
    </tr>
    <tr>
      <th>year</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013</th>
      <td>317</td>
      <td>278</td>
    </tr>
    <tr>
      <th>2014</th>
      <td>12557</td>
      <td>51854</td>
    </tr>
    <tr>
      <th>2015</th>
      <td>13484</td>
      <td>53579</td>
    </tr>
    <tr>
      <th>2016</th>
      <td>15066</td>
      <td>58763</td>
    </tr>
    <tr>
      <th>2017</th>
      <td>15511</td>
      <td>61401</td>
    </tr>
    <tr>
      <th>2018</th>
      <td>3533</td>
      <td>13802</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Add year as a column
year_df["Year"] = year_df.index
year_df

# Reset index
year_df = year_df.reset_index(drop=True)
year_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
      <th>Year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>317</td>
      <td>278</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>1</th>
      <td>12557</td>
      <td>51854</td>
      <td>2014</td>
    </tr>
    <tr>
      <th>2</th>
      <td>13484</td>
      <td>53579</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15066</td>
      <td>58763</td>
      <td>2016</td>
    </tr>
    <tr>
      <th>4</th>
      <td>15511</td>
      <td>61401</td>
      <td>2017</td>
    </tr>
    <tr>
      <th>5</th>
      <td>3533</td>
      <td>13802</td>
      <td>2018</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Drop 2013 and 2018 for the purpose of the line graph
only_few_years = year_df.drop([0,5])
only_few_years
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
      <th>Year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>12557</td>
      <td>51854</td>
      <td>2014</td>
    </tr>
    <tr>
      <th>2</th>
      <td>13484</td>
      <td>53579</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>3</th>
      <td>15066</td>
      <td>58763</td>
      <td>2016</td>
    </tr>
    <tr>
      <th>4</th>
      <td>15511</td>
      <td>61401</td>
      <td>2017</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Create a line plot for number of incidents by year
x_axis = only_few_years["Year"]
y_axis_incidents = only_few_years["Number of Incidents"]


# Format the graph
plt.xticks(np.arange(min(x_axis), max(x_axis)+2, 1.0))
plt.xlabel("Year")
plt.ylabel("Number of Gun Violence Incidents")
plt.title("Gun Violence Incidents per Year")
plt.plot(x_axis, y_axis_incidents)

# New x to include 2018
new_x = ["2014", "2015", "2016", "2017", "2018"]

# Use regression line to predict the number of incidents in 2018
(slope, intercept, _, _, _) = linregress(x_axis, y_axis_incidents)
fit = slope * x_axis + intercept

# 2018
x = 2018
y = slope * x + intercept
plt.plot(x, y, 'red', marker = 'o')

plt.plot(x_axis, fit, 'r')
plt.grid(True)

sns.set_style("whitegrid")
plt.savefig("GunIncidentsperYear.png")

```


![png](output_13_0.png)



```python
# Create a line plot for deaths by year
# Include 2018 in
x_axis = only_few_years["Year"]
y_axis_deaths = only_few_years["Number of Deaths"]

# Format the graph
plt.xticks(np.arange(min(x_axis), max(x_axis) + 1, 1.0))
plt.xlabel("Year")
plt.ylabel("Number of Gun Violence Deaths")
plt.title("Gun Violence Deaths per Year")
plt.plot(x_axis, y_axis_deaths)
sns.set_style("whitegrid")

# Use regression line to predict the number of incidents in 2018
# x_axis that includes 2018
# We can extrapolate our current 2018 data 
(slope, intercept, _, _, _) = linregress(x_axis, y_axis_deaths)
fit = slope * x_axis + intercept
plt.plot(x_axis, fit, 'r')

# 2018
x = 2018
y = slope * x + intercept
plt.plot(x, y, 'red', marker = 'o')

# Show Graph
plt.grid(True)

# Save 
plt.savefig("GunViolenceDeathsperYear.png")

```


![png](output_14_0.png)



```python
gmaps.geojson_geometries.list_geometries()
```




    dict_keys(['countries', 'countries-high-resolution', 'england-counties', 'us-states', 'us-counties', 'india-states', 'brazil-states'])




```python
gmaps.datasets.list_datasets()
```




    dict_keys(['taxi_rides', 'earthquakes', 'acled_africa', 'acled_africa_by_year', 'london_congestion_zone', 'nuclear_plants', 'starbucks_kfc_uk', 'gini'])




```python
# Run Census Search to retrieve data on all states
# Note the addition of "B23025_005E" for unemployment count
census_data = c.acs5.get(("NAME", "B19013_001E", "B01003_001E", "B01002_001E",
                          "B19301_001E",
                          "B17001_002E",
                          "B23025_005E"), {'for': 'state:*'})

# Convert to DataFrame
census_pd = pd.DataFrame(census_data)

# Column Reordering
census_pd = census_pd.rename(columns={"B01003_001E": "Population",
                                      "B01002_001E": "Median Age",
                                      "B19013_001E": "Household Income",
                                      "B19301_001E": "Per Capita Income",
                                      "B17001_002E": "Poverty Count",
                                      "B23025_005E": "Unemployment Count",
                                      "NAME": "State_Name"})

# Add in Poverty Rate (Poverty Count / Population)
census_pd["Poverty Rate"] = 100 * \
    census_pd["Poverty Count"].astype(
        int) / census_pd["Population"].astype(int)

# Add in Employment Rate (Employment Count / Population)
census_pd["Unemployment Rate"] = 100 * \
    census_pd["Unemployment Count"].astype(
        int) / census_pd["Population"].astype(int)

# Final DataFrame
census_pd = census_pd[["State_Name", "Population", "Median Age", "Household Income",
                       "Per Capita Income", "Poverty Count", "Poverty Rate", "Unemployment Rate"]]

census_pd.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>Population</th>
      <th>Median Age</th>
      <th>Household Income</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
      <th>Unemployment Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>4799277.0</td>
      <td>38.1</td>
      <td>43253.0</td>
      <td>23680.0</td>
      <td>870631.0</td>
      <td>18.140878</td>
      <td>5.040968</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>720316.0</td>
      <td>33.6</td>
      <td>70760.0</td>
      <td>32651.0</td>
      <td>69514.0</td>
      <td>9.650487</td>
      <td>4.572854</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>6479703.0</td>
      <td>36.3</td>
      <td>49774.0</td>
      <td>25358.0</td>
      <td>1131901.0</td>
      <td>17.468409</td>
      <td>4.882323</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>2933369.0</td>
      <td>37.5</td>
      <td>40768.0</td>
      <td>22170.0</td>
      <td>547328.0</td>
      <td>18.658682</td>
      <td>4.132961</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>37659181.0</td>
      <td>35.4</td>
      <td>61094.0</td>
      <td>29527.0</td>
      <td>5885417.0</td>
      <td>15.628107</td>
      <td>5.758662</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Merge data frames on state (state_summary) and (census_pd)
merge_summary_census = pd.merge(state_summary, census_pd, on = "State_Name")
merge_summary_census.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
      <th>State_Name</th>
      <th>Population</th>
      <th>Median Age</th>
      <th>Household Income</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
      <th>Unemployment Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1880</td>
      <td>5471</td>
      <td>Alabama</td>
      <td>4799277.0</td>
      <td>38.1</td>
      <td>43253.0</td>
      <td>23680.0</td>
      <td>870631.0</td>
      <td>18.140878</td>
      <td>5.040968</td>
    </tr>
    <tr>
      <th>1</th>
      <td>267</td>
      <td>1349</td>
      <td>Alaska</td>
      <td>720316.0</td>
      <td>33.6</td>
      <td>70760.0</td>
      <td>32651.0</td>
      <td>69514.0</td>
      <td>9.650487</td>
      <td>4.572854</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1094</td>
      <td>2328</td>
      <td>Arizona</td>
      <td>6479703.0</td>
      <td>36.3</td>
      <td>49774.0</td>
      <td>25358.0</td>
      <td>1131901.0</td>
      <td>17.468409</td>
      <td>4.882323</td>
    </tr>
    <tr>
      <th>3</th>
      <td>773</td>
      <td>2842</td>
      <td>Arkansas</td>
      <td>2933369.0</td>
      <td>37.5</td>
      <td>40768.0</td>
      <td>22170.0</td>
      <td>547328.0</td>
      <td>18.658682</td>
      <td>4.132961</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5562</td>
      <td>16306</td>
      <td>California</td>
      <td>37659181.0</td>
      <td>35.4</td>
      <td>61094.0</td>
      <td>29527.0</td>
      <td>5885417.0</td>
      <td>15.628107</td>
      <td>5.758662</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Save as a csv
# Note to avoid any issues later, use encoding="utf-8"
census_pd.to_csv("census_data_states.csv", encoding="utf-8", index=False)
```


```python
# Store filepath in a variable
gun_law = "Resources/Gun Law Strictness.xlsx"
gun_school = "Resources/Total School Expenditures by State.xlsx"
gun_mental = "Resources/Mental Health Expenditure by State.xlsx"
gun_raw = "Resources/raw_data.xlsx"
state_geocode = "Resources/us_states_geo_center.xlsx"
#gun_violence="Resources/gun-violence-data.csv"
```


```python
# Read our data file with the pandas library
gun_law_df = pd.read_excel(gun_law)
gun_school_df = pd.read_excel(gun_school)
gun_mental_df = pd.read_excel(gun_mental)
gun_raw_df = pd.read_excel(gun_raw)
state_geo = pd.read_excel(state_geocode)
#gun_violence_df = pd.read_csv(gun_violence)

gun_mental_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>state</th>
      <th>tota_expenditure</th>
      <th>expenditure_per_capita</th>
      <th>per_capita_rank</th>
      <th>poverty_per_capita_expenditure</th>
      <th>poverty_per_capita_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>373100000</td>
      <td>78.19</td>
      <td>37</td>
      <td>327.57</td>
      <td>40</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>214200700</td>
      <td>310.01</td>
      <td>3</td>
      <td>1785.01</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>1414300000</td>
      <td>221.27</td>
      <td>7</td>
      <td>827.56</td>
      <td>15</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>122468795</td>
      <td>42.02</td>
      <td>48</td>
      <td>169.39</td>
      <td>50</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>5674396088</td>
      <td>152.60</td>
      <td>15</td>
      <td>645.55</td>
      <td>22</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Rename state to State_Name
gun_mental_df.rename(columns={'state' : 'State_Name'}, inplace = True)
gun_mental_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>tota_expenditure</th>
      <th>expenditure_per_capita</th>
      <th>per_capita_rank</th>
      <th>poverty_per_capita_expenditure</th>
      <th>poverty_per_capita_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>373100000</td>
      <td>78.19</td>
      <td>37</td>
      <td>327.57</td>
      <td>40</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>214200700</td>
      <td>310.01</td>
      <td>3</td>
      <td>1785.01</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>1414300000</td>
      <td>221.27</td>
      <td>7</td>
      <td>827.56</td>
      <td>15</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>122468795</td>
      <td>42.02</td>
      <td>48</td>
      <td>169.39</td>
      <td>50</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>5674396088</td>
      <td>152.60</td>
      <td>15</td>
      <td>645.55</td>
      <td>22</td>
    </tr>
  </tbody>
</table>
</div>




```python
gun_law_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>gun law strengh (rank)</th>
      <th>state</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>California</td>
      <td>A</td>
      <td>43</td>
      <td>7.9</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>New Jersey</td>
      <td>A-</td>
      <td>45</td>
      <td>5.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Connecticut</td>
      <td>A-</td>
      <td>46</td>
      <td>4.6</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Massachusetts</td>
      <td>A-</td>
      <td>50</td>
      <td>3.4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>New York</td>
      <td>A-</td>
      <td>48</td>
      <td>4.4</td>
    </tr>
  </tbody>
</table>
</div>




```python
gun_file_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>incident_id</th>
      <th>date</th>
      <th>state</th>
      <th>city_or_county</th>
      <th>address</th>
      <th>n_killed</th>
      <th>n_injured</th>
      <th>incident_url</th>
      <th>source_url</th>
      <th>incident_url_fields_missing</th>
      <th>...</th>
      <th>participant_age_group</th>
      <th>participant_gender</th>
      <th>participant_name</th>
      <th>participant_relationship</th>
      <th>participant_status</th>
      <th>participant_type</th>
      <th>sources</th>
      <th>state_house_district</th>
      <th>state_senate_district</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>461105</td>
      <td>1/1/13</td>
      <td>Pennsylvania</td>
      <td>Mckeesport</td>
      <td>1506 Versailles Avenue and Coursin Street</td>
      <td>0</td>
      <td>4</td>
      <td>http://www.gunviolencearchive.org/incident/461105</td>
      <td>http://www.post-gazette.com/local/south/2013/0...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Adult 18+||1::Adult 18+||2::Adult 18+||3::A...</td>
      <td>0::Male||1::Male||3::Male||4::Female</td>
      <td>0::Julian Sims</td>
      <td>NaN</td>
      <td>0::Arrested||1::Injured||2::Injured||3::Injure...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://pittsburgh.cbslocal.com/2013/01/01/4-pe...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>1</th>
      <td>460726</td>
      <td>1/1/13</td>
      <td>California</td>
      <td>Hawthorne</td>
      <td>13500 block of Cerise Avenue</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/460726</td>
      <td>http://www.dailybulletin.com/article/zz/201301...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Adult 18+||1::Adult 18+||2::Adult 18+||3::A...</td>
      <td>0::Male</td>
      <td>0::Bernard Gillis</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://losangeles.cbslocal.com/2013/01/01/man-...</td>
      <td>62.0</td>
      <td>35.0</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>2</th>
      <td>478855</td>
      <td>1/1/13</td>
      <td>Ohio</td>
      <td>Lorain</td>
      <td>1776 East 28th Street</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/478855</td>
      <td>http://chronicle.northcoastnow.com/2013/02/14/...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Adult 18+||1::Adult 18+||2::Adult 18+||3::A...</td>
      <td>0::Male||1::Male||2::Male||3::Male||4::Male</td>
      <td>0::Damien Bell||1::Desmen Noble||2::Herman Sea...</td>
      <td>NaN</td>
      <td>0::Injured, Unharmed, Arrested||1::Unharmed, A...</td>
      <td>0::Subject-Suspect||1::Subject-Suspect||2::Vic...</td>
      <td>http://www.morningjournal.com/general-news/201...</td>
      <td>56.0</td>
      <td>13.0</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>3</th>
      <td>478925</td>
      <td>1/5/13</td>
      <td>Colorado</td>
      <td>Aurora</td>
      <td>16000 block of East Ithaca Place</td>
      <td>4</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/478925</td>
      <td>http://www.dailydemocrat.com/20130106/aurora-s...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Adult 18+||1::Adult 18+||2::Adult 18+||3::A...</td>
      <td>0::Female||1::Male||2::Male||3::Male</td>
      <td>0::Stacie Philbrook||1::Christopher Ratliffe||...</td>
      <td>NaN</td>
      <td>0::Killed||1::Killed||2::Killed||3::Killed</td>
      <td>0::Victim||1::Victim||2::Victim||3::Subject-Su...</td>
      <td>http://denver.cbslocal.com/2013/01/06/officer-...</td>
      <td>40.0</td>
      <td>28.0</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>4</th>
      <td>478959</td>
      <td>1/7/13</td>
      <td>North Carolina</td>
      <td>Greensboro</td>
      <td>307 Mourning Dove Terrace</td>
      <td>2</td>
      <td>2</td>
      <td>http://www.gunviolencearchive.org/incident/478959</td>
      <td>http://www.journalnow.com/news/local/article_d...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Adult 18+||1::Adult 18+||2::Teen 12-17||3::...</td>
      <td>0::Female||1::Male||2::Male||3::Female</td>
      <td>0::Danielle Imani Jameison||1::Maurice Eugene ...</td>
      <td>3::Family</td>
      <td>0::Injured||1::Injured||2::Killed||3::Killed</td>
      <td>0::Victim||1::Victim||2::Victim||3::Subject-Su...</td>
      <td>http://myfox8.com/2013/01/08/update-mother-sho...</td>
      <td>62.0</td>
      <td>27.0</td>
      <td>2013</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 30 columns</p>
</div>




```python
gun_file_df["month"] = pd.DatetimeIndex(gun_file_df["date"]).month
gun_file_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>incident_id</th>
      <th>date</th>
      <th>state</th>
      <th>city_or_county</th>
      <th>address</th>
      <th>n_killed</th>
      <th>n_injured</th>
      <th>incident_url</th>
      <th>source_url</th>
      <th>incident_url_fields_missing</th>
      <th>...</th>
      <th>participant_gender</th>
      <th>participant_name</th>
      <th>participant_relationship</th>
      <th>participant_status</th>
      <th>participant_type</th>
      <th>sources</th>
      <th>state_house_district</th>
      <th>state_senate_district</th>
      <th>year</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>461105</td>
      <td>1/1/13</td>
      <td>Pennsylvania</td>
      <td>Mckeesport</td>
      <td>1506 Versailles Avenue and Coursin Street</td>
      <td>0</td>
      <td>4</td>
      <td>http://www.gunviolencearchive.org/incident/461105</td>
      <td>http://www.post-gazette.com/local/south/2013/0...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||3::Male||4::Female</td>
      <td>0::Julian Sims</td>
      <td>NaN</td>
      <td>0::Arrested||1::Injured||2::Injured||3::Injure...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://pittsburgh.cbslocal.com/2013/01/01/4-pe...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>460726</td>
      <td>1/1/13</td>
      <td>California</td>
      <td>Hawthorne</td>
      <td>13500 block of Cerise Avenue</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/460726</td>
      <td>http://www.dailybulletin.com/article/zz/201301...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>0::Bernard Gillis</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://losangeles.cbslocal.com/2013/01/01/man-...</td>
      <td>62.0</td>
      <td>35.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>478855</td>
      <td>1/1/13</td>
      <td>Ohio</td>
      <td>Lorain</td>
      <td>1776 East 28th Street</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/478855</td>
      <td>http://chronicle.northcoastnow.com/2013/02/14/...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Male||4::Male</td>
      <td>0::Damien Bell||1::Desmen Noble||2::Herman Sea...</td>
      <td>NaN</td>
      <td>0::Injured, Unharmed, Arrested||1::Unharmed, A...</td>
      <td>0::Subject-Suspect||1::Subject-Suspect||2::Vic...</td>
      <td>http://www.morningjournal.com/general-news/201...</td>
      <td>56.0</td>
      <td>13.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>478925</td>
      <td>1/5/13</td>
      <td>Colorado</td>
      <td>Aurora</td>
      <td>16000 block of East Ithaca Place</td>
      <td>4</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/478925</td>
      <td>http://www.dailydemocrat.com/20130106/aurora-s...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Female||1::Male||2::Male||3::Male</td>
      <td>0::Stacie Philbrook||1::Christopher Ratliffe||...</td>
      <td>NaN</td>
      <td>0::Killed||1::Killed||2::Killed||3::Killed</td>
      <td>0::Victim||1::Victim||2::Victim||3::Subject-Su...</td>
      <td>http://denver.cbslocal.com/2013/01/06/officer-...</td>
      <td>40.0</td>
      <td>28.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>478959</td>
      <td>1/7/13</td>
      <td>North Carolina</td>
      <td>Greensboro</td>
      <td>307 Mourning Dove Terrace</td>
      <td>2</td>
      <td>2</td>
      <td>http://www.gunviolencearchive.org/incident/478959</td>
      <td>http://www.journalnow.com/news/local/article_d...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Female||1::Male||2::Male||3::Female</td>
      <td>0::Danielle Imani Jameison||1::Maurice Eugene ...</td>
      <td>3::Family</td>
      <td>0::Injured||1::Injured||2::Killed||3::Killed</td>
      <td>0::Victim||1::Victim||2::Victim||3::Subject-Su...</td>
      <td>http://myfox8.com/2013/01/08/update-mother-sho...</td>
      <td>62.0</td>
      <td>27.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>478948</td>
      <td>1/7/13</td>
      <td>Oklahoma</td>
      <td>Tulsa</td>
      <td>6000 block of South Owasso</td>
      <td>4</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/478948</td>
      <td>http://usnews.nbcnews.com/_news/2013/01/07/163...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Female||1::Female||2::Female||3::Female||4:...</td>
      <td>0::Rebeika Powell||1::Kayetie Melchor||2::Mist...</td>
      <td>NaN</td>
      <td>0::Killed||1::Killed||2::Killed||3::Killed||4:...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.kjrh.com/news/local-news/4-found-sh...</td>
      <td>72.0</td>
      <td>11.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>6</th>
      <td>479363</td>
      <td>1/19/13</td>
      <td>New Mexico</td>
      <td>Albuquerque</td>
      <td>2806 Long Lane</td>
      <td>5</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/479363</td>
      <td>http://hinterlandgazette.com/2013/01/pastor-gr...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Female||2::Male||3::Female||4::Fem...</td>
      <td>0::Greg Griego||1::Sara Griego||2::Zephania Gr...</td>
      <td>5::Family</td>
      <td>0::Killed||1::Killed||2::Killed||3::Killed||4:...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.cbsnews.com/news/nehemiah-gringo-ca...</td>
      <td>10.0</td>
      <td>14.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>7</th>
      <td>479374</td>
      <td>1/21/13</td>
      <td>Louisiana</td>
      <td>New Orleans</td>
      <td>LaSalle Street and Martin Luther King Jr. Boul...</td>
      <td>0</td>
      <td>5</td>
      <td>http://www.gunviolencearchive.org/incident/479374</td>
      <td>http://www.nola.com/crime/index.ssf/2013/01/no...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Male||4::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Injured||1::Injured||2::Injured||3::Injured...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.huffingtonpost.com/2013/01/21/new-o...</td>
      <td>93.0</td>
      <td>5.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>8</th>
      <td>479389</td>
      <td>1/21/13</td>
      <td>California</td>
      <td>Brentwood</td>
      <td>1100 block of Breton Drive</td>
      <td>0</td>
      <td>4</td>
      <td>http://www.gunviolencearchive.org/incident/479389</td>
      <td>http://sanfrancisco.cbslocal.com/2013/01/22/4-...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Male||4::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Injured||1::Injured||2::Injured||3::Injured...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.contracostatimes.com/ci_22426767/br...</td>
      <td>11.0</td>
      <td>7.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9</th>
      <td>492151</td>
      <td>1/23/13</td>
      <td>Maryland</td>
      <td>Baltimore</td>
      <td>1500 block of W. Fayette St.</td>
      <td>1</td>
      <td>6</td>
      <td>http://www.gunviolencearchive.org/incident/492151</td>
      <td>http://www.abc2news.com/news/crime-checker/bal...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>0::Deshaun Jones</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured|...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://articles.baltimoresun.com/2013-08-25/ne...</td>
      <td>NaN</td>
      <td>44.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>10</th>
      <td>491674</td>
      <td>1/23/13</td>
      <td>Tennessee</td>
      <td>Chattanooga</td>
      <td>1501 Dodds Ave</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/491674</td>
      <td>http://www.wrcbtv.com/story/22664154/one-dead-...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Male</td>
      <td>0::Demetrius Davis</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.wrcbtv.com/story/22664154/one-dead-...</td>
      <td>28.0</td>
      <td>10.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>11</th>
      <td>479413</td>
      <td>1/25/13</td>
      <td>Missouri</td>
      <td>Saint Louis</td>
      <td>W Florissant Ave and Riverview Blvd</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/479413</td>
      <td>http://stlouis.cbslocal.com/2013/01/25/one-dea...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>0::Terry Robinson Jr.</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://fox2now.com/2013/01/25/two-shot-in-nort...</td>
      <td>76.0</td>
      <td>4.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>12</th>
      <td>479561</td>
      <td>1/26/13</td>
      <td>Louisiana</td>
      <td>Charenton</td>
      <td>1000 block of Flat Town Road</td>
      <td>2</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/479561</td>
      <td>http://www.huffingtonpost.com/2013/01/27/wilbe...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Male||4::Male</td>
      <td>0::Ofc.||1::Dep.||2::Dep.||3::Eddie Lyons||4::...</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Killed||...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://theadvocate.com/home/5018249-125/3-law-...</td>
      <td>50.0</td>
      <td>21.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>13</th>
      <td>479554</td>
      <td>1/26/13</td>
      <td>District of Columbia</td>
      <td>Washington</td>
      <td>2403 Benning Road Northeast</td>
      <td>0</td>
      <td>5</td>
      <td>http://www.gunviolencearchive.org/incident/479554</td>
      <td>http://www.washingtontimes.com/news/2013/jan/2...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Female||1::Female||2::Male||3::Male||4::Mal...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Injured||1::Injured||2::Injured||3::Injured...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.washingtonexaminer.com/d.c.-nightcl...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>14</th>
      <td>479460</td>
      <td>1/26/13</td>
      <td>Ohio</td>
      <td>Springfield</td>
      <td>601 West Main Street</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/479460</td>
      <td>http://www.whio.com//news/news/crime-law/arres...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Male||4::Male</td>
      <td>0::Ernest Edwards||1::Anthony Clark||2::Joshua...</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured|...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.daytondailynews.com/news/news/1-dea...</td>
      <td>79.0</td>
      <td>10.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>15</th>
      <td>479573</td>
      <td>2/2/13</td>
      <td>Tennessee</td>
      <td>Memphis</td>
      <td>2514 Mount Moriah</td>
      <td>0</td>
      <td>5</td>
      <td>http://www.gunviolencearchive.org/incident/479573</td>
      <td>https://www.highbeam.com/doc/1P2-34349822.html</td>
      <td>False</td>
      <td>...</td>
      <td>0::Female||1::Female||2::Female||3::Female||4:...</td>
      <td>4::Mary||5::Sundra Payne</td>
      <td>NaN</td>
      <td>0::Injured||1::Injured||2::Injured||3::Injured...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://wreg.com/2013/02/02/five-hurt-in-memphi...</td>
      <td>84.0</td>
      <td>33.0</td>
      <td>2013</td>
      <td>2</td>
    </tr>
    <tr>
      <th>16</th>
      <td>479580</td>
      <td>2/3/13</td>
      <td>California</td>
      <td>Yuba (county)</td>
      <td>5800 block of Poplar Avenue</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/479580</td>
      <td>http://sacramento.cbslocal.com/2013/02/04/1-de...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Female||4::Male||5::Male|...</td>
      <td>0::Teng Yang||1::Tou Yang||2::Xong Yang||3::Ya...</td>
      <td>4::Drive by - Random victims||5::Drive by - Ra...</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured|...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://news.asiantown.net/r/28306/no-death-pen...</td>
      <td>3.0</td>
      <td>4.0</td>
      <td>2013</td>
      <td>2</td>
    </tr>
    <tr>
      <th>17</th>
      <td>479592</td>
      <td>2/7/13</td>
      <td>Illinois</td>
      <td>Chicago</td>
      <td>2500 block of East 75th Street</td>
      <td>0</td>
      <td>4</td>
      <td>http://www.gunviolencearchive.org/incident/479592</td>
      <td>http://chicago.cbslocal.com/2013/02/07/four-wo...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Injured||1::Injured||2::Injured||3::Injured...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://crimeinchicago.blogspot.com/2013/02/4-m...</td>
      <td>25.0</td>
      <td>13.0</td>
      <td>2013</td>
      <td>2</td>
    </tr>
    <tr>
      <th>18</th>
      <td>479603</td>
      <td>2/9/13</td>
      <td>Louisiana</td>
      <td>New Orleans</td>
      <td>400 block of Bourbon Street</td>
      <td>0</td>
      <td>4</td>
      <td>http://www.gunviolencearchive.org/incident/479603</td>
      <td>http://www.nola.com/crime/index.ssf/2013/04/su...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Female||2::Female||3::Male||4::Mal...</td>
      <td>4::Malcolm "London" Hall||5::Brandon Brown||6:...</td>
      <td>NaN</td>
      <td>0::Injured||1::Injured||2::Injured||3::Injured...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.cbsnews.com/news/mardi-gras-shootin...</td>
      <td>93.0</td>
      <td>4.0</td>
      <td>2013</td>
      <td>2</td>
    </tr>
    <tr>
      <th>19</th>
      <td>480311</td>
      <td>2/11/13</td>
      <td>California</td>
      <td>Vallejo</td>
      <td>800 block of Humboldt Street</td>
      <td>1</td>
      <td>4</td>
      <td>http://www.gunviolencearchive.org/incident/480311</td>
      <td>http://archive.news10.net/news/article/229997/...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Female||4::Female</td>
      <td>0::Oscar Garcia</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured|...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.timesheraldonline.com/article/ZZ/20...</td>
      <td>14.0</td>
      <td>3.0</td>
      <td>2013</td>
      <td>2</td>
    </tr>
    <tr>
      <th>20</th>
      <td>480327</td>
      <td>2/11/13</td>
      <td>Delaware</td>
      <td>Wilmington</td>
      <td>500 North King Street</td>
      <td>3</td>
      <td>2</td>
      <td>http://www.gunviolencearchive.org/incident/480327</td>
      <td>http://www.philly.com/philly/news/Police_ID_vi...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Female||1::Female||2::Male||3::Male||4::Male</td>
      <td>0::Laura Elizabeth "Beth" Mulford||1::Christin...</td>
      <td>NaN</td>
      <td>0::Killed||1::Killed||2::Injured||3::Injured||...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.usatoday.com/story/news/nation/2013...</td>
      <td>2.0</td>
      <td>3.0</td>
      <td>2013</td>
      <td>2</td>
    </tr>
    <tr>
      <th>21</th>
      <td>480344</td>
      <td>2/12/13</td>
      <td>Utah</td>
      <td>Midvale</td>
      <td>8286 Adams Street and 450 West Street</td>
      <td>4</td>
      <td>1</td>
      <td>http://www.gunviolencearchive.org/incident/480344</td>
      <td>NaN</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Female||4::Male|...</td>
      <td>0::Omar Paul Jarman||1::Shontay N. Young||2::D...</td>
      <td>NaN</td>
      <td>0::Killed||1::Killed||2::Killed||3::Injured||4...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://fox13now.com/2014/03/28/man-sentenced-f...</td>
      <td>44.0</td>
      <td>8.0</td>
      <td>2013</td>
      <td>2</td>
    </tr>
    <tr>
      <th>22</th>
      <td>480358</td>
      <td>2/19/13</td>
      <td>California</td>
      <td>Orange (county)</td>
      <td>Katella Avenue</td>
      <td>4</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/480358</td>
      <td>http://www.dailymail.co.uk/news/article-228117...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Female||4::Male||5::Male||6::Male</td>
      <td>0::Courtney Aoki||4::Melvin Edwards||5::Jeremy...</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured|...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.nydailynews.com/news/national/2-sou...</td>
      <td>72.0</td>
      <td>34.0</td>
      <td>2013</td>
      <td>2</td>
    </tr>
    <tr>
      <th>23</th>
      <td>480383</td>
      <td>2/21/13</td>
      <td>Oklahoma</td>
      <td>Tulsa</td>
      <td>1200 block of North 89th East Avenue</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/480383</td>
      <td>http://www.krmg.com/news/news/local/more-detai...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Female||4::Male</td>
      <td>0::Chaz Fain||4::Mark Hopkins</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured|...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.kjrh.com/news/local-news/suspect-in...</td>
      <td>77.0</td>
      <td>34.0</td>
      <td>2013</td>
      <td>2</td>
    </tr>
    <tr>
      <th>24</th>
      <td>480401</td>
      <td>2/22/13</td>
      <td>Michigan</td>
      <td>Grand Rapids</td>
      <td>1447 Grandville Ave. SW</td>
      <td>0</td>
      <td>4</td>
      <td>http://www.gunviolencearchive.org/incident/480401</td>
      <td>http://www.mlive.com/news/grand-rapids/index.s...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Male||4::Female</td>
      <td>0::Marcus Antoine Smith</td>
      <td>NaN</td>
      <td>0::Unharmed, Arrested||1::Injured||2::Injured|...</td>
      <td>0::Subject-Suspect||1::Victim||2::Victim||3::V...</td>
      <td>http://www.mlive.com/news/grand-rapids/index.s...</td>
      <td>76.0</td>
      <td>29.0</td>
      <td>2013</td>
      <td>2</td>
    </tr>
    <tr>
      <th>25</th>
      <td>480407</td>
      <td>2/23/13</td>
      <td>California</td>
      <td>Lancaster</td>
      <td>43145 Business Center Parkway</td>
      <td>0</td>
      <td>4</td>
      <td>http://www.gunviolencearchive.org/incident/480407</td>
      <td>http://latimesblogs.latimes.com/lanow/2013/02/...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Injured||1::Injured||2::Injured||3::Injured...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://losangeles.cbslocal.com/2013/02/23/4-wo...</td>
      <td>36.0</td>
      <td>21.0</td>
      <td>2013</td>
      <td>2</td>
    </tr>
    <tr>
      <th>26</th>
      <td>480443</td>
      <td>2/24/13</td>
      <td>Georgia</td>
      <td>Macon</td>
      <td>2800 block of Mercer University Drive</td>
      <td>0</td>
      <td>8</td>
      <td>http://www.gunviolencearchive.org/incident/480443</td>
      <td>http://www.macon.com/news/local/crime/article3...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Male||4::Male||5...</td>
      <td>8::Frank Fletcher, Jr.</td>
      <td>NaN</td>
      <td>0::Injured||1::Injured||2::Injured||3::Injured...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.macon.com/news/local/crime/article3...</td>
      <td>142.0</td>
      <td>26.0</td>
      <td>2013</td>
      <td>2</td>
    </tr>
    <tr>
      <th>27</th>
      <td>481186</td>
      <td>3/2/13</td>
      <td>Louisiana</td>
      <td>Shreveport</td>
      <td>7000 block of Burlingame Boulevard</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/481186</td>
      <td>http://www.shreveportla.gov/DocumentCenter/Vie...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Male||4::Male||5...</td>
      <td>0::Chauncey "Trey" Boulevard, Jr.||1::Jumarco ...</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured|...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.ksla.com/story/21442631/1-dead-3-in...</td>
      <td>3.0</td>
      <td>39.0</td>
      <td>2013</td>
      <td>3</td>
    </tr>
    <tr>
      <th>28</th>
      <td>481198</td>
      <td>3/3/13</td>
      <td>Georgia</td>
      <td>Moultrie</td>
      <td>224 Second Street Northwest</td>
      <td>2</td>
      <td>2</td>
      <td>http://www.gunviolencearchive.org/incident/481198</td>
      <td>http://www.moultrieobserver.com/news/jury-says...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Female||3::Male||4::Male|...</td>
      <td>0::James L. Key||1::Eric Debruce||2::Margaret ...</td>
      <td>NaN</td>
      <td>0::Killed||1::Killed||2::Injured||3::Injured||...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://www.moultrieobserver.com/news/local_new...</td>
      <td>172.0</td>
      <td>11.0</td>
      <td>2013</td>
      <td>3</td>
    </tr>
    <tr>
      <th>29</th>
      <td>481208</td>
      <td>3/3/13</td>
      <td>Michigan</td>
      <td>Saginaw (county)</td>
      <td>4030 Dixie Hwy</td>
      <td>0</td>
      <td>4</td>
      <td>http://www.gunviolencearchive.org/incident/481208</td>
      <td>http://www.mlive.com/news/saginaw/index.ssf/20...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Female||3::Female</td>
      <td>1::Edgar G. Jones</td>
      <td>NaN</td>
      <td>0::Injured||1::Injured||2::Injured||3::Injured</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim</td>
      <td>http://www.wfxg.com/story/21447002/four-people...</td>
      <td>95.0</td>
      <td>32.0</td>
      <td>2013</td>
      <td>3</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>239647</th>
      <td>1081936</td>
      <td>3/31/18</td>
      <td>Maine</td>
      <td>Bangor</td>
      <td>Ohio St</td>
      <td>0</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>http://www.newscentermaine.com/article/news/lo...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>0::Garrett Field</td>
      <td>NaN</td>
      <td>0::Unharmed, Arrested</td>
      <td>0::Subject-Suspect</td>
      <td>http://www.newscentermaine.com/article/news/lo...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239648</th>
      <td>1081946</td>
      <td>3/31/18</td>
      <td>District of Columbia</td>
      <td>Washington</td>
      <td>600 block of Alabama Ave SE</td>
      <td>0</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://twitter.com/DCPoliceDept/status/980251...</td>
      <td>False</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>https://twitter.com/DCPoliceDept/status/980251...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239649</th>
      <td>1081949</td>
      <td>3/31/18</td>
      <td>Nevada</td>
      <td>Las Vegas</td>
      <td>Bonanza Rd and Valley View Blvd</td>
      <td>0</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://www.ktnv.com/news/crime/3-injured-afte...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Unharmed, Arrested</td>
      <td>0::Subject-Suspect</td>
      <td>https://www.ktnv.com/news/crime/3-injured-afte...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239650</th>
      <td>1082266</td>
      <td>3/31/18</td>
      <td>California</td>
      <td>Palmdale</td>
      <td>37900 block of 47th St E</td>
      <td>1</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://local.nixle.com/alert/6489381/?sub_id=...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male</td>
      <td>0::Israel Castaneda</td>
      <td>NaN</td>
      <td>0::Killed||1::Unharmed</td>
      <td>0::Victim||1::Subject-Suspect</td>
      <td>http://ktla.com/2018/04/01/l-a-county-sheriffs...</td>
      <td>36.0</td>
      <td>21.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239651</th>
      <td>1082483</td>
      <td>3/31/18</td>
      <td>Texas</td>
      <td>Wichita Falls</td>
      <td>2300 block of 8th St</td>
      <td>0</td>
      <td>1</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>http://www.newschannel6now.com/story/37853540/...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Unharmed||1::Injured</td>
      <td>0::Victim||1::Subject-Suspect</td>
      <td>http://www.newschannel6now.com/story/37853540/...</td>
      <td>69.0</td>
      <td>30.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239652</th>
      <td>1082486</td>
      <td>3/31/18</td>
      <td>Texas</td>
      <td>Dallas</td>
      <td>1200 block of Whispering Trail</td>
      <td>0</td>
      <td>1</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://www.dallasnews.com/news/crime/2018/03/...</td>
      <td>False</td>
      <td>...</td>
      <td>1::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Unharmed||1::Injured</td>
      <td>0::Victim||1::Subject-Suspect</td>
      <td>https://www.dallasnews.com/news/crime/2018/03/...</td>
      <td>109.0</td>
      <td>23.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239653</th>
      <td>1083121</td>
      <td>3/31/18</td>
      <td>Nevada</td>
      <td>Reno</td>
      <td>1400 E Peckham Ln</td>
      <td>1</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://lasvegassun.com/news/2018/apr/02/burgl...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>0::Nicholas Sedano</td>
      <td>NaN</td>
      <td>0::Killed</td>
      <td>0::Subject-Suspect</td>
      <td>https://www.rgj.com/story/news/crime/2018/04/0...</td>
      <td>26.0</td>
      <td>16.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239654</th>
      <td>1081947</td>
      <td>3/31/18</td>
      <td>Nevada</td>
      <td>Reno</td>
      <td>Wedekind Rd</td>
      <td>1</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>http://mynews4.com/news/local/police-28-year-o...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>0::Julio Ramos Rubalcaba</td>
      <td>NaN</td>
      <td>0::Killed</td>
      <td>0::Victim</td>
      <td>https://www.rgj.com/story/news/crime/2018/03/3...</td>
      <td>30.0</td>
      <td>13.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239655</th>
      <td>1082089</td>
      <td>3/31/18</td>
      <td>California</td>
      <td>San Diego</td>
      <td>8660 Miramar Rd</td>
      <td>0</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://www.nbcsandiego.com/news/local/Suspect...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Unharmed</td>
      <td>0::Subject-Suspect</td>
      <td>http://www.cbs8.com/story/37852105/man-fires-s...</td>
      <td>77.0</td>
      <td>39.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239656</th>
      <td>1081901</td>
      <td>3/31/18</td>
      <td>New York</td>
      <td>Rochester</td>
      <td>100 block of Warren Ave</td>
      <td>1</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://twitter.com/mcfw/status/98013667377747...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Female</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Killed</td>
      <td>0::Victim</td>
      <td>https://twitter.com/mcfw/status/98013667377747...</td>
      <td>136.0</td>
      <td>56.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239657</th>
      <td>1082394</td>
      <td>3/31/18</td>
      <td>California</td>
      <td>Shafter</td>
      <td>300 block of Atlantic Ave</td>
      <td>0</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>http://www.bakersfield.com/news/breaking/none-...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>0::John Wells</td>
      <td>NaN</td>
      <td>0::Arrested</td>
      <td>0::Subject-Suspect</td>
      <td>http://www.bakersfield.com/news/breaking/none-...</td>
      <td>32.0</td>
      <td>14.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239658</th>
      <td>1082392</td>
      <td>3/31/18</td>
      <td>California</td>
      <td>Oakland</td>
      <td>1700 block of 23rd Ave</td>
      <td>1</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://www.eastbaytimes.com/2018/04/01/man-fa...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Killed</td>
      <td>0::Victim</td>
      <td>https://www.eastbaytimes.com/2018/04/01/man-fa...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239659</th>
      <td>1082057</td>
      <td>3/31/18</td>
      <td>Florida</td>
      <td>Orlando</td>
      <td>South Kirkman Road and Raleigh Street</td>
      <td>0</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>http://www.orlandosentinel.com/news/breaking-n...</td>
      <td>False</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Injured||1::Injured||2::Injured</td>
      <td>0::Victim||1::Victim||2::Victim</td>
      <td>http://www.orlandosentinel.com/news/breaking-n...</td>
      <td>46.0</td>
      <td>11.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239660</th>
      <td>1082091</td>
      <td>3/31/18</td>
      <td>California</td>
      <td>Stockton</td>
      <td>1700 block of Harbor St</td>
      <td>2</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>http://www.recordnet.com/news/20180401/victims...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male</td>
      <td>0::Richard "Richie" Gonzalez||1::Francisco "Pa...</td>
      <td>NaN</td>
      <td>0::Killed||1::Killed</td>
      <td>0::Victim||1::Victim</td>
      <td>http://www.recordnet.com/news/20180331/two-sho...</td>
      <td>13.0</td>
      <td>5.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239661</th>
      <td>1081719</td>
      <td>3/31/18</td>
      <td>North Carolina</td>
      <td>Kings Mountain</td>
      <td>US 74</td>
      <td>0</td>
      <td>4</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://www.wsoctv.com/news/local/4-shot-in-dr...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Injured||1::Injured||2::Injured||3::Injured</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim</td>
      <td>http://www.wbtv.com/story/37851749/four-people...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239662</th>
      <td>1082388</td>
      <td>3/31/18</td>
      <td>Minnesota</td>
      <td>Saint Paul</td>
      <td>1000 block of York Ave</td>
      <td>0</td>
      <td>2</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://www.twincities.com/2018/04/01/drive-by...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Injured||1::Injured</td>
      <td>0::Victim||1::Victim</td>
      <td>http://www.kare11.com/article/news/2-injured-i...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239663</th>
      <td>1082197</td>
      <td>3/31/18</td>
      <td>Oklahoma</td>
      <td>Guthrie</td>
      <td>500 block of Walnut St</td>
      <td>1</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>http://okcfox.com/news/local/one-person-killed...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>0::Jason Birt</td>
      <td>NaN</td>
      <td>0::Killed</td>
      <td>0::Subject-Suspect</td>
      <td>http://www.news9.com/story/37853411/officials-...</td>
      <td>31.0</td>
      <td>20.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239664</th>
      <td>1082023</td>
      <td>3/31/18</td>
      <td>Missouri</td>
      <td>Festus</td>
      <td>4200 block of Hillsboro Hematite Rd</td>
      <td>0</td>
      <td>1</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>http://www.kmov.com/story/37854762/woman-63-sh...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Female||1::Male</td>
      <td>1::Garrett Noll</td>
      <td>NaN</td>
      <td>0::Injured||1::Unharmed, Arrested</td>
      <td>0::Victim||1::Subject-Suspect</td>
      <td>http://www.ksdk.com/article/news/local/police-...</td>
      <td>114.0</td>
      <td>3.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239665</th>
      <td>1082226</td>
      <td>3/31/18</td>
      <td>Missouri</td>
      <td>Saint Clair</td>
      <td>1100 Park Dr</td>
      <td>0</td>
      <td>1</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>http://fox2now.com/2018/04/01/franklin-county-...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Injured||1::Unharmed, Arrested</td>
      <td>0::Victim||1::Subject-Suspect</td>
      <td>http://fox2now.com/2018/04/01/franklin-county-...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239666</th>
      <td>1081894</td>
      <td>3/31/18</td>
      <td>Missouri</td>
      <td>Saint Louis</td>
      <td>3100 block of California St</td>
      <td>1</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>http://www.stltoday.com/news/local/crime-and-c...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male</td>
      <td>0::Jermon Perry</td>
      <td>1::Family</td>
      <td>0::Killed||1::Unharmed</td>
      <td>0::Victim||1::Subject-Suspect</td>
      <td>http://fox2now.com/2018/03/31/7-year-old-dies-...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239667</th>
      <td>1082234</td>
      <td>3/31/18</td>
      <td>Tennessee</td>
      <td>Memphis</td>
      <td>2900 block of Wingate</td>
      <td>0</td>
      <td>1</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>http://wreg.com/2018/03/31/one-person-in-criti...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Female</td>
      <td>1::Nicole McKinney</td>
      <td>NaN</td>
      <td>0::Injured||1::Arrested</td>
      <td>0::Victim||1::Subject-Suspect</td>
      <td>http://wreg.com/2018/03/31/one-person-in-criti...</td>
      <td>90.0</td>
      <td>30.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239668</th>
      <td>1081742</td>
      <td>3/31/18</td>
      <td>Michigan</td>
      <td>Detroit</td>
      <td>I-96</td>
      <td>0</td>
      <td>1</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://www.freep.com/story/news/local/michiga...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Injured</td>
      <td>0::Victim</td>
      <td>https://www.freep.com/story/news/local/michiga...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239669</th>
      <td>1082990</td>
      <td>3/31/18</td>
      <td>Wisconsin</td>
      <td>Madison</td>
      <td>Hayes Rd</td>
      <td>0</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://www.channel3000.com/news/crime/couple-...</td>
      <td>False</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>http://host.madison.com/wsj/news/local/crime/g...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239670</th>
      <td>1081752</td>
      <td>3/31/18</td>
      <td>Illinois</td>
      <td>Chicago</td>
      <td>1 block of N Paulina St</td>
      <td>0</td>
      <td>1</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://chicago.suntimes.com/news/man-36-wound...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Injured||1::Unharmed</td>
      <td>0::Victim||1::Subject-Suspect</td>
      <td>https://chicago.suntimes.com/news/man-36-wound...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239671</th>
      <td>1082061</td>
      <td>3/31/18</td>
      <td>Washington</td>
      <td>Spokane (Spokane Valley)</td>
      <td>12600 block of N Willow Crest Ln</td>
      <td>0</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://www.kxly.com/news/domestic-violence-su...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>0::Sean M. Gummow</td>
      <td>NaN</td>
      <td>0::Unharmed, Arrested</td>
      <td>0::Subject-Suspect</td>
      <td>https://www.kxly.com/news/domestic-violence-su...</td>
      <td>4.0</td>
      <td>4.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239672</th>
      <td>1083142</td>
      <td>3/31/18</td>
      <td>Louisiana</td>
      <td>Rayne</td>
      <td>North Riceland Road and Highway 90</td>
      <td>0</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>http://www.klfy.com/news/local/rayne-woman-cha...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Female</td>
      <td>0::Jhkeya Tezeno</td>
      <td>NaN</td>
      <td>0::Unharmed, Arrested</td>
      <td>0::Subject-Suspect</td>
      <td>http://www.klfy.com/news/local/rayne-woman-cha...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239673</th>
      <td>1083139</td>
      <td>3/31/18</td>
      <td>Louisiana</td>
      <td>Natchitoches</td>
      <td>247 Keyser Ave</td>
      <td>1</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>http://www.ksla.com/story/37854648/man-wanted-...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male</td>
      <td>0::Jamal Haskett||1::Jaquarious Tyjuan Ardison</td>
      <td>NaN</td>
      <td>0::Killed||1::Unharmed, Arrested</td>
      <td>0::Victim||1::Subject-Suspect</td>
      <td>http://www.ksla.com/story/37854648/man-wanted-...</td>
      <td>23.0</td>
      <td>31.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239674</th>
      <td>1083151</td>
      <td>3/31/18</td>
      <td>Louisiana</td>
      <td>Gretna</td>
      <td>1300 block of Cook Street</td>
      <td>0</td>
      <td>1</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>http://www.nola.com/crime/index.ssf/2018/04/sh...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0::Injured</td>
      <td>0::Victim</td>
      <td>http://www.nola.com/crime/index.ssf/2018/04/sh...</td>
      <td>85.0</td>
      <td>7.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239675</th>
      <td>1082514</td>
      <td>3/31/18</td>
      <td>Texas</td>
      <td>Houston</td>
      <td>12630 Ashford Point Dr</td>
      <td>1</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://www.chron.com/news/houston-texas/houst...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>0::Leroy Ellis</td>
      <td>NaN</td>
      <td>0::Killed</td>
      <td>0::Victim</td>
      <td>http://www.khou.com/article/news/hpd-investiga...</td>
      <td>149.0</td>
      <td>17.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
    <tr>
      <th>239676</th>
      <td>1081940</td>
      <td>3/31/18</td>
      <td>Maine</td>
      <td>Norridgewock</td>
      <td>434 Skowhegan Rd</td>
      <td>2</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/108...</td>
      <td>https://www.centralmaine.com/2018/03/31/police...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Female||1::Male</td>
      <td>0::Marie Lancaster Hale||1::William Hale</td>
      <td>1::Significant others - current or former</td>
      <td>0::Killed||1::Killed</td>
      <td>0::Victim||1::Subject-Suspect</td>
      <td>https://www.centralmaine.com/2018/03/31/police...</td>
      <td>111.0</td>
      <td>3.0</td>
      <td>2018</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
<p>239677 rows × 31 columns</p>
</div>




```python
gun_violence_dfgun_viole ["year"] = pd.DatetimeIndex(gun_file_df["date"]).year
gun_violence_df.head()
```


```python
gun_violence_df["month"] = pd.DatetimeIndex(gun_violence_df["date"]).month
gun_violence_df
```


```python
#Keep all data except the ones referent to the 2013 year, since this year has incomplete information. Then, reset index.#Keep all 
gun_violence_df = gun_violence_df[gun_violence_df.year != 2013]
gun_violence_df.head()
```


```python
gun_file_df  = gun_file_df.reset_index(drop=True)
gun_file_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>incident_id</th>
      <th>date</th>
      <th>state</th>
      <th>city_or_county</th>
      <th>address</th>
      <th>n_killed</th>
      <th>n_injured</th>
      <th>incident_url</th>
      <th>source_url</th>
      <th>incident_url_fields_missing</th>
      <th>...</th>
      <th>participant_gender</th>
      <th>participant_name</th>
      <th>participant_relationship</th>
      <th>participant_status</th>
      <th>participant_type</th>
      <th>sources</th>
      <th>state_house_district</th>
      <th>state_senate_district</th>
      <th>year</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>461105</td>
      <td>1/1/13</td>
      <td>Pennsylvania</td>
      <td>Mckeesport</td>
      <td>1506 Versailles Avenue and Coursin Street</td>
      <td>0</td>
      <td>4</td>
      <td>http://www.gunviolencearchive.org/incident/461105</td>
      <td>http://www.post-gazette.com/local/south/2013/0...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||3::Male||4::Female</td>
      <td>0::Julian Sims</td>
      <td>NaN</td>
      <td>0::Arrested||1::Injured||2::Injured||3::Injure...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://pittsburgh.cbslocal.com/2013/01/01/4-pe...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>460726</td>
      <td>1/1/13</td>
      <td>California</td>
      <td>Hawthorne</td>
      <td>13500 block of Cerise Avenue</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/460726</td>
      <td>http://www.dailybulletin.com/article/zz/201301...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male</td>
      <td>0::Bernard Gillis</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://losangeles.cbslocal.com/2013/01/01/man-...</td>
      <td>62.0</td>
      <td>35.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>478855</td>
      <td>1/1/13</td>
      <td>Ohio</td>
      <td>Lorain</td>
      <td>1776 East 28th Street</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/478855</td>
      <td>http://chronicle.northcoastnow.com/2013/02/14/...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Male||1::Male||2::Male||3::Male||4::Male</td>
      <td>0::Damien Bell||1::Desmen Noble||2::Herman Sea...</td>
      <td>NaN</td>
      <td>0::Injured, Unharmed, Arrested||1::Unharmed, A...</td>
      <td>0::Subject-Suspect||1::Subject-Suspect||2::Vic...</td>
      <td>http://www.morningjournal.com/general-news/201...</td>
      <td>56.0</td>
      <td>13.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>478925</td>
      <td>1/5/13</td>
      <td>Colorado</td>
      <td>Aurora</td>
      <td>16000 block of East Ithaca Place</td>
      <td>4</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/478925</td>
      <td>http://www.dailydemocrat.com/20130106/aurora-s...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Female||1::Male||2::Male||3::Male</td>
      <td>0::Stacie Philbrook||1::Christopher Ratliffe||...</td>
      <td>NaN</td>
      <td>0::Killed||1::Killed||2::Killed||3::Killed</td>
      <td>0::Victim||1::Victim||2::Victim||3::Subject-Su...</td>
      <td>http://denver.cbslocal.com/2013/01/06/officer-...</td>
      <td>40.0</td>
      <td>28.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>478959</td>
      <td>1/7/13</td>
      <td>North Carolina</td>
      <td>Greensboro</td>
      <td>307 Mourning Dove Terrace</td>
      <td>2</td>
      <td>2</td>
      <td>http://www.gunviolencearchive.org/incident/478959</td>
      <td>http://www.journalnow.com/news/local/article_d...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Female||1::Male||2::Male||3::Female</td>
      <td>0::Danielle Imani Jameison||1::Maurice Eugene ...</td>
      <td>3::Family</td>
      <td>0::Injured||1::Injured||2::Killed||3::Killed</td>
      <td>0::Victim||1::Victim||2::Victim||3::Subject-Su...</td>
      <td>http://myfox8.com/2013/01/08/update-mother-sho...</td>
      <td>62.0</td>
      <td>27.0</td>
      <td>2013</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 31 columns</p>
</div>




```python
gun_file_df.columns
```




    Index(['incident_id', 'date', 'state', 'city_or_county', 'address', 'n_killed',
           'n_injured', 'incident_url', 'source_url',
           'incident_url_fields_missing', 'congressional_district', 'gun_stolen',
           'gun_type', 'incident_characteristics', 'latitude',
           'location_description', 'longitude', 'n_guns_involved', 'notes',
           'participant_age', 'participant_age_group', 'participant_gender',
           'participant_name', 'participant_relationship', 'participant_status',
           'participant_type', 'sources', 'state_house_district',
           'state_senate_district', 'year', 'month'],
          dtype='object')




```python
len(gun_file_df)
```




    239677




```python
len(gun_file_df['month'])
```




    239677




```python
gun_file_df.to_csv("gun_file_df.csv")
```


```python
#Create a loop to change months into seasons
#summer: 6, 7, 8
#fall: 9, 10, 11
#winter: 12, 1, 2
#spring: 3, 4, 5

season=[]

for x in gun_file_df["month"]:
    if x == 12 or x == 1 or x == 2:
        season.append("winter")
    elif x == 3 or x == 4 or x == 5:
        season.append("spring")
    elif x == 6 or x == 7 or x == 8:
        season.append("summer")
    else: 
        season.append("fall")
```


```python
# Create a column from the list and add to gun_law_df
gun_file_df["Season"] = season
gun_file_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>incident_id</th>
      <th>date</th>
      <th>state</th>
      <th>city_or_county</th>
      <th>address</th>
      <th>n_killed</th>
      <th>n_injured</th>
      <th>incident_url</th>
      <th>source_url</th>
      <th>incident_url_fields_missing</th>
      <th>...</th>
      <th>participant_name</th>
      <th>participant_relationship</th>
      <th>participant_status</th>
      <th>participant_type</th>
      <th>sources</th>
      <th>state_house_district</th>
      <th>state_senate_district</th>
      <th>year</th>
      <th>month</th>
      <th>Season</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>461105</td>
      <td>1/1/13</td>
      <td>Pennsylvania</td>
      <td>Mckeesport</td>
      <td>1506 Versailles Avenue and Coursin Street</td>
      <td>0</td>
      <td>4</td>
      <td>http://www.gunviolencearchive.org/incident/461105</td>
      <td>http://www.post-gazette.com/local/south/2013/0...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Julian Sims</td>
      <td>NaN</td>
      <td>0::Arrested||1::Injured||2::Injured||3::Injure...</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://pittsburgh.cbslocal.com/2013/01/01/4-pe...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2013</td>
      <td>1</td>
      <td>winter</td>
    </tr>
    <tr>
      <th>1</th>
      <td>460726</td>
      <td>1/1/13</td>
      <td>California</td>
      <td>Hawthorne</td>
      <td>13500 block of Cerise Avenue</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/460726</td>
      <td>http://www.dailybulletin.com/article/zz/201301...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Bernard Gillis</td>
      <td>NaN</td>
      <td>0::Killed||1::Injured||2::Injured||3::Injured</td>
      <td>0::Victim||1::Victim||2::Victim||3::Victim||4:...</td>
      <td>http://losangeles.cbslocal.com/2013/01/01/man-...</td>
      <td>62.0</td>
      <td>35.0</td>
      <td>2013</td>
      <td>1</td>
      <td>winter</td>
    </tr>
    <tr>
      <th>2</th>
      <td>478855</td>
      <td>1/1/13</td>
      <td>Ohio</td>
      <td>Lorain</td>
      <td>1776 East 28th Street</td>
      <td>1</td>
      <td>3</td>
      <td>http://www.gunviolencearchive.org/incident/478855</td>
      <td>http://chronicle.northcoastnow.com/2013/02/14/...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Damien Bell||1::Desmen Noble||2::Herman Sea...</td>
      <td>NaN</td>
      <td>0::Injured, Unharmed, Arrested||1::Unharmed, A...</td>
      <td>0::Subject-Suspect||1::Subject-Suspect||2::Vic...</td>
      <td>http://www.morningjournal.com/general-news/201...</td>
      <td>56.0</td>
      <td>13.0</td>
      <td>2013</td>
      <td>1</td>
      <td>winter</td>
    </tr>
    <tr>
      <th>3</th>
      <td>478925</td>
      <td>1/5/13</td>
      <td>Colorado</td>
      <td>Aurora</td>
      <td>16000 block of East Ithaca Place</td>
      <td>4</td>
      <td>0</td>
      <td>http://www.gunviolencearchive.org/incident/478925</td>
      <td>http://www.dailydemocrat.com/20130106/aurora-s...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Stacie Philbrook||1::Christopher Ratliffe||...</td>
      <td>NaN</td>
      <td>0::Killed||1::Killed||2::Killed||3::Killed</td>
      <td>0::Victim||1::Victim||2::Victim||3::Subject-Su...</td>
      <td>http://denver.cbslocal.com/2013/01/06/officer-...</td>
      <td>40.0</td>
      <td>28.0</td>
      <td>2013</td>
      <td>1</td>
      <td>winter</td>
    </tr>
    <tr>
      <th>4</th>
      <td>478959</td>
      <td>1/7/13</td>
      <td>North Carolina</td>
      <td>Greensboro</td>
      <td>307 Mourning Dove Terrace</td>
      <td>2</td>
      <td>2</td>
      <td>http://www.gunviolencearchive.org/incident/478959</td>
      <td>http://www.journalnow.com/news/local/article_d...</td>
      <td>False</td>
      <td>...</td>
      <td>0::Danielle Imani Jameison||1::Maurice Eugene ...</td>
      <td>3::Family</td>
      <td>0::Injured||1::Injured||2::Killed||3::Killed</td>
      <td>0::Victim||1::Victim||2::Victim||3::Subject-Su...</td>
      <td>http://myfox8.com/2013/01/08/update-mother-sho...</td>
      <td>62.0</td>
      <td>27.0</td>
      <td>2013</td>
      <td>1</td>
      <td>winter</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 32 columns</p>
</div>




```python
#Drop 2018 to plot Season ( since 2018 season are in progress)
gun_file_season = gun_file_df[gun_file_df.year != 2018]
len(gun_file_season)
```




    225875




```python
#Incidents Group by season:#Incident 
grouped_season = gun_file_season.groupby(["Season"])
number_of_incidents = grouped_season.count()
season_incidents = number_of_incidents["incident_id"]
season_incidents
```




    Season
    fall      57520
    spring    56446
    summer    60921
    winter    50988
    Name: incident_id, dtype: int64




```python
#Incidents Group by season:
season_deaths = grouped_season["n_killed"].sum()
season_deaths
```




    Season
    fall      14418
    spring    13736
    summer    15289
    winter    13492
    Name: n_killed, dtype: int64




```python
# Create a new dataframe
season_summary = pd.DataFrame({"Season Incidents" : season_incidents,
                             "Season Deaths" : season_deaths
                             })
season_summary
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Season Deaths</th>
      <th>Season Incidents</th>
    </tr>
    <tr>
      <th>Season</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>fall</th>
      <td>14418</td>
      <td>57520</td>
    </tr>
    <tr>
      <th>spring</th>
      <td>13736</td>
      <td>56446</td>
    </tr>
    <tr>
      <th>summer</th>
      <td>15289</td>
      <td>60921</td>
    </tr>
    <tr>
      <th>winter</th>
      <td>13492</td>
      <td>50988</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Add season as a column
season_summary["Season"] = season_summary.index
season_summary.head()

# Reset index
season_summary = season_summary.reset_index(drop=True)
season_summary.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Season Deaths</th>
      <th>Season Incidents</th>
      <th>Season</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>14418</td>
      <td>57520</td>
      <td>fall</td>
    </tr>
    <tr>
      <th>1</th>
      <td>13736</td>
      <td>56446</td>
      <td>spring</td>
    </tr>
    <tr>
      <th>2</th>
      <td>15289</td>
      <td>60921</td>
      <td>summer</td>
    </tr>
    <tr>
      <th>3</th>
      <td>13492</td>
      <td>50988</td>
      <td>winter</td>
    </tr>
  </tbody>
</table>
</div>




```python
trace1 =  go.Bar(
         x=season_summary ['Season'],
    y=season_summary ['Season Deaths'],
    name='Season Deaths'
)
trace2 = go.Bar(
    x=season_summary ['Season'],
    y=season_summary ['Season Incidents'],
    name='Season Incidents'
)

data = [trace1, trace2]
layout = go.Layout(
    title='Firearm Incidents / Deaths per Season in the U.S. 2014-2017',
    barmode='group'
)


fig = go.Figure(data=data, layout=layout)
py.iplot(fig, filename='grouped-bar')
```

    Aw, snap! We don't have an account for ''. Want to try again? You can authenticate with your email address or username. Sign in is not case sensitive.
    
    Don't have an account? plot.ly
    
    Questions? accounts@plot.ly



    ---------------------------------------------------------------------------

    PlotlyError                               Traceback (most recent call last)

    <ipython-input-156-ec968f2e4700> in <module>()
         18 
         19 fig = go.Figure(data=data, layout=layout)
    ---> 20 py.iplot(fig, filename='grouped-bar')
    

    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/plotly/plotly/plotly.py in iplot(figure_or_data, **plot_options)
        162         embed_options['height'] = str(embed_options['height']) + 'px'
        163 
    --> 164     return tools.embed(url, **embed_options)
        165 
        166 


    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/plotly/tools.py in embed(file_owner_or_url, file_id, width, height)
        388         else:
        389             url = file_owner_or_url
    --> 390         return PlotlyDisplay(url, width, height)
        391     else:
        392         if (get_config_defaults()['plotly_domain']


    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/plotly/tools.py in __init__(self, url, width, height)
       1432         def __init__(self, url, width, height):
       1433             self.resource = url
    -> 1434             self.embed_code = get_embed(url, width=width, height=height)
       1435             super(PlotlyDisplay, self).__init__(data=self.embed_code)
       1436 


    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/plotly/tools.py in get_embed(file_owner_or_url, file_id, width, height)
        293                 "'{1}'."
        294                 "\nRun help on this function for more information."
    --> 295                 "".format(url, plotly_rest_url))
        296         urlsplit = six.moves.urllib.parse.urlparse(url)
        297         file_owner = urlsplit.path.split('/')[1].split('~')[1]


    PlotlyError: Because you didn't supply a 'file_id' in the call, we're assuming you're trying to snag a figure from a url. You supplied the url, '', we expected it to start with 'https://plot.ly'.
    Run help on this function for more information.



```python
# Create a for loop to just pull the letter with + or -
just_letter = []

for row in gun_law_df["grade (2017)"]:
    if row == "A" or row ==  "A+" or row == "A-":
        just_letter.append("A")
    elif row == "B" or row == "B+" or row == "B-":
        just_letter.append("B")
    elif row == "C" or row == "C+" or row == "C-":
        just_letter.append("C")
    elif row == "D" or row == "D+" or row == "D-":
        just_letter.append("D")
    else:
        just_letter.append("F")

# Create a column from the list and add to gun_law_df
gun_law_df["Letter_Grade"] = just_letter
gun_law_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>gun law strengh (rank)</th>
      <th>state</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
      <th>Letter_Grade</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>California</td>
      <td>A</td>
      <td>43</td>
      <td>7.9</td>
      <td>A</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>New Jersey</td>
      <td>A-</td>
      <td>45</td>
      <td>5.5</td>
      <td>A</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Connecticut</td>
      <td>A-</td>
      <td>46</td>
      <td>4.6</td>
      <td>A</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Massachusetts</td>
      <td>A-</td>
      <td>50</td>
      <td>3.4</td>
      <td>A</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>New York</td>
      <td>A-</td>
      <td>48</td>
      <td>4.4</td>
      <td>A</td>
    </tr>
  </tbody>
</table>
</div>




```python
state_geo.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>Location</th>
      <th>lat (N)</th>
      <th>lng (W)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>12.1 mi (19.5 km) southwest of Clanton</td>
      <td>32.7794</td>
      <td>-86.8287</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>77.9 mi (125.4 km) northwest of Denali</td>
      <td>64.0685</td>
      <td>152.2782</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>49.7 mi (80.0 km) east-southeast of Prescott</td>
      <td>34.2744</td>
      <td>-111.6602</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>14.2 mi (22.9 km) northwest of Little Rock</td>
      <td>34.8938</td>
      <td>-92.4426</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>36 mi (58 km) northeast of Madera,</td>
      <td>37.1841</td>
      <td>-119.4696</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Rename state to State_Name
gun_law_df.rename(columns={'state' : 'State_Name'}, inplace = True)
gun_law_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>gun law strengh (rank)</th>
      <th>State_Name</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
      <th>Letter_Grade</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>California</td>
      <td>A</td>
      <td>43</td>
      <td>7.9</td>
      <td>A</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>New Jersey</td>
      <td>A-</td>
      <td>45</td>
      <td>5.5</td>
      <td>A</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Connecticut</td>
      <td>A-</td>
      <td>46</td>
      <td>4.6</td>
      <td>A</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Massachusetts</td>
      <td>A-</td>
      <td>50</td>
      <td>3.4</td>
      <td>A</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>New York</td>
      <td>A-</td>
      <td>48</td>
      <td>4.4</td>
      <td>A</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Merge merge_summary_census
merge_census_law = pd.merge(merge_summary_census, gun_law_df, on = "State_Name")
merge_census_law.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
      <th>State_Name</th>
      <th>Population</th>
      <th>Median Age</th>
      <th>Household Income</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
      <th>Unemployment Rate</th>
      <th>gun law strengh (rank)</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
      <th>Letter_Grade</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1880</td>
      <td>5471</td>
      <td>Alabama</td>
      <td>4799277.0</td>
      <td>38.1</td>
      <td>43253.0</td>
      <td>23680.0</td>
      <td>870631.0</td>
      <td>18.140878</td>
      <td>5.040968</td>
      <td>36</td>
      <td>F</td>
      <td>2</td>
      <td>21.4</td>
      <td>F</td>
    </tr>
    <tr>
      <th>1</th>
      <td>267</td>
      <td>1349</td>
      <td>Alaska</td>
      <td>720316.0</td>
      <td>33.6</td>
      <td>70760.0</td>
      <td>32651.0</td>
      <td>69514.0</td>
      <td>9.650487</td>
      <td>4.572854</td>
      <td>44</td>
      <td>F</td>
      <td>1</td>
      <td>23.0</td>
      <td>F</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1094</td>
      <td>2328</td>
      <td>Arizona</td>
      <td>6479703.0</td>
      <td>36.3</td>
      <td>49774.0</td>
      <td>25358.0</td>
      <td>1131901.0</td>
      <td>17.468409</td>
      <td>4.882323</td>
      <td>47</td>
      <td>F</td>
      <td>16</td>
      <td>15.2</td>
      <td>F</td>
    </tr>
    <tr>
      <th>3</th>
      <td>773</td>
      <td>2842</td>
      <td>Arkansas</td>
      <td>2933369.0</td>
      <td>37.5</td>
      <td>40768.0</td>
      <td>22170.0</td>
      <td>547328.0</td>
      <td>18.658682</td>
      <td>4.132961</td>
      <td>39</td>
      <td>F</td>
      <td>9</td>
      <td>17.7</td>
      <td>F</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5562</td>
      <td>16306</td>
      <td>California</td>
      <td>37659181.0</td>
      <td>35.4</td>
      <td>61094.0</td>
      <td>29527.0</td>
      <td>5885417.0</td>
      <td>15.628107</td>
      <td>5.758662</td>
      <td>1</td>
      <td>A</td>
      <td>43</td>
      <td>7.9</td>
      <td>A</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Rename state to State_Name
gun_school_df.rename(columns={'state' : 'State_Name'}, inplace = True)
gun_school_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>total_spending</th>
      <th>total_per_pupil</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>6826622000</td>
      <td>9128</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>2646225000</td>
      <td>20172</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>7205417000</td>
      <td>7489</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>4683434000</td>
      <td>9694</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>66208111000</td>
      <td>10467</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Rename state to State_Name
gun_raw_df.rename(columns={'state' : 'State_Name'}, inplace = True)
gun_raw_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>year</th>
      <th>lawtotal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>2017</td>
      <td>10</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>2017</td>
      <td>4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>2017</td>
      <td>11</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>2017</td>
      <td>11</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>2017</td>
      <td>106</td>
    </tr>
  </tbody>
</table>
</div>




```python
merge1 = gun_raw_df.merge(gun_school_df, how='outer', on='State_Name')
merge1.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>year</th>
      <th>lawtotal</th>
      <th>total_spending</th>
      <th>total_per_pupil</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>2017.0</td>
      <td>10.0</td>
      <td>6826622000</td>
      <td>9128</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>2017.0</td>
      <td>4.0</td>
      <td>2646225000</td>
      <td>20172</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>7205417000</td>
      <td>7489</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>4683434000</td>
      <td>9694</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>2017.0</td>
      <td>106.0</td>
      <td>66208111000</td>
      <td>10467</td>
    </tr>
  </tbody>
</table>
</div>




```python
merge2 = merge1.merge(gun_law_df, how='outer', on='State_Name')
merge2.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>year</th>
      <th>lawtotal</th>
      <th>total_spending</th>
      <th>total_per_pupil</th>
      <th>gun law strengh (rank)</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
      <th>Letter_Grade</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>2017.0</td>
      <td>10.0</td>
      <td>6826622000</td>
      <td>9128</td>
      <td>36.0</td>
      <td>F</td>
      <td>2.0</td>
      <td>21.4</td>
      <td>F</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>2017.0</td>
      <td>4.0</td>
      <td>2646225000</td>
      <td>20172</td>
      <td>44.0</td>
      <td>F</td>
      <td>1.0</td>
      <td>23.0</td>
      <td>F</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>7205417000</td>
      <td>7489</td>
      <td>47.0</td>
      <td>F</td>
      <td>16.0</td>
      <td>15.2</td>
      <td>F</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>4683434000</td>
      <td>9694</td>
      <td>39.0</td>
      <td>F</td>
      <td>9.0</td>
      <td>17.7</td>
      <td>F</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>2017.0</td>
      <td>106.0</td>
      <td>66208111000</td>
      <td>10467</td>
      <td>1.0</td>
      <td>A</td>
      <td>43.0</td>
      <td>7.9</td>
      <td>A</td>
    </tr>
  </tbody>
</table>
</div>




```python
gun_dataframe = merge2.merge(gun_mental_df, how = "outer", on = "State_Name")
gun_dataframe.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>year</th>
      <th>lawtotal</th>
      <th>total_spending</th>
      <th>total_per_pupil</th>
      <th>gun law strengh (rank)</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
      <th>Letter_Grade</th>
      <th>tota_expenditure</th>
      <th>expenditure_per_capita</th>
      <th>per_capita_rank</th>
      <th>poverty_per_capita_expenditure</th>
      <th>poverty_per_capita_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>2017.0</td>
      <td>10.0</td>
      <td>6826622000</td>
      <td>9128</td>
      <td>36.0</td>
      <td>F</td>
      <td>2.0</td>
      <td>21.4</td>
      <td>F</td>
      <td>373100000</td>
      <td>78.19</td>
      <td>37</td>
      <td>327.57</td>
      <td>40</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>2017.0</td>
      <td>4.0</td>
      <td>2646225000</td>
      <td>20172</td>
      <td>44.0</td>
      <td>F</td>
      <td>1.0</td>
      <td>23.0</td>
      <td>F</td>
      <td>214200700</td>
      <td>310.01</td>
      <td>3</td>
      <td>1785.01</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>7205417000</td>
      <td>7489</td>
      <td>47.0</td>
      <td>F</td>
      <td>16.0</td>
      <td>15.2</td>
      <td>F</td>
      <td>1414300000</td>
      <td>221.27</td>
      <td>7</td>
      <td>827.56</td>
      <td>15</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>4683434000</td>
      <td>9694</td>
      <td>39.0</td>
      <td>F</td>
      <td>9.0</td>
      <td>17.7</td>
      <td>F</td>
      <td>122468795</td>
      <td>42.02</td>
      <td>48</td>
      <td>169.39</td>
      <td>50</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>2017.0</td>
      <td>106.0</td>
      <td>66208111000</td>
      <td>10467</td>
      <td>1.0</td>
      <td>A</td>
      <td>43.0</td>
      <td>7.9</td>
      <td>A</td>
      <td>5674396088</td>
      <td>152.60</td>
      <td>15</td>
      <td>645.55</td>
      <td>22</td>
    </tr>
  </tbody>
</table>
</div>




```python
gun_census_df = gun_dataframe.merge(census_pd, how='outer', on='State_Name')
gun_census_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>year</th>
      <th>lawtotal</th>
      <th>total_spending</th>
      <th>total_per_pupil</th>
      <th>gun law strengh (rank)</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
      <th>Letter_Grade</th>
      <th>...</th>
      <th>per_capita_rank</th>
      <th>poverty_per_capita_expenditure</th>
      <th>poverty_per_capita_rank</th>
      <th>Population</th>
      <th>Median Age</th>
      <th>Household Income</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
      <th>Unemployment Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>2017.0</td>
      <td>10.0</td>
      <td>6.826622e+09</td>
      <td>9128.0</td>
      <td>36.0</td>
      <td>F</td>
      <td>2.0</td>
      <td>21.4</td>
      <td>F</td>
      <td>...</td>
      <td>37.0</td>
      <td>327.57</td>
      <td>40.0</td>
      <td>4799277.0</td>
      <td>38.1</td>
      <td>43253.0</td>
      <td>23680.0</td>
      <td>870631.0</td>
      <td>18.140878</td>
      <td>5.040968</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>2017.0</td>
      <td>4.0</td>
      <td>2.646225e+09</td>
      <td>20172.0</td>
      <td>44.0</td>
      <td>F</td>
      <td>1.0</td>
      <td>23.0</td>
      <td>F</td>
      <td>...</td>
      <td>3.0</td>
      <td>1785.01</td>
      <td>2.0</td>
      <td>720316.0</td>
      <td>33.6</td>
      <td>70760.0</td>
      <td>32651.0</td>
      <td>69514.0</td>
      <td>9.650487</td>
      <td>4.572854</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>7.205417e+09</td>
      <td>7489.0</td>
      <td>47.0</td>
      <td>F</td>
      <td>16.0</td>
      <td>15.2</td>
      <td>F</td>
      <td>...</td>
      <td>7.0</td>
      <td>827.56</td>
      <td>15.0</td>
      <td>6479703.0</td>
      <td>36.3</td>
      <td>49774.0</td>
      <td>25358.0</td>
      <td>1131901.0</td>
      <td>17.468409</td>
      <td>4.882323</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>4.683434e+09</td>
      <td>9694.0</td>
      <td>39.0</td>
      <td>F</td>
      <td>9.0</td>
      <td>17.7</td>
      <td>F</td>
      <td>...</td>
      <td>48.0</td>
      <td>169.39</td>
      <td>50.0</td>
      <td>2933369.0</td>
      <td>37.5</td>
      <td>40768.0</td>
      <td>22170.0</td>
      <td>547328.0</td>
      <td>18.658682</td>
      <td>4.132961</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>2017.0</td>
      <td>106.0</td>
      <td>6.620811e+10</td>
      <td>10467.0</td>
      <td>1.0</td>
      <td>A</td>
      <td>43.0</td>
      <td>7.9</td>
      <td>A</td>
      <td>...</td>
      <td>15.0</td>
      <td>645.55</td>
      <td>22.0</td>
      <td>37659181.0</td>
      <td>35.4</td>
      <td>61094.0</td>
      <td>29527.0</td>
      <td>5885417.0</td>
      <td>15.628107</td>
      <td>5.758662</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 22 columns</p>
</div>




```python
gun_census = gun_census_df.dropna(axis=0, how='any', thresh=None, subset=None, inplace=False)
gun_census.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>year</th>
      <th>lawtotal</th>
      <th>total_spending</th>
      <th>total_per_pupil</th>
      <th>gun law strengh (rank)</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
      <th>Letter_Grade</th>
      <th>...</th>
      <th>per_capita_rank</th>
      <th>poverty_per_capita_expenditure</th>
      <th>poverty_per_capita_rank</th>
      <th>Population</th>
      <th>Median Age</th>
      <th>Household Income</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
      <th>Unemployment Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>2017.0</td>
      <td>10.0</td>
      <td>6.826622e+09</td>
      <td>9128.0</td>
      <td>36.0</td>
      <td>F</td>
      <td>2.0</td>
      <td>21.4</td>
      <td>F</td>
      <td>...</td>
      <td>37.0</td>
      <td>327.57</td>
      <td>40.0</td>
      <td>4799277.0</td>
      <td>38.1</td>
      <td>43253.0</td>
      <td>23680.0</td>
      <td>870631.0</td>
      <td>18.140878</td>
      <td>5.040968</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>2017.0</td>
      <td>4.0</td>
      <td>2.646225e+09</td>
      <td>20172.0</td>
      <td>44.0</td>
      <td>F</td>
      <td>1.0</td>
      <td>23.0</td>
      <td>F</td>
      <td>...</td>
      <td>3.0</td>
      <td>1785.01</td>
      <td>2.0</td>
      <td>720316.0</td>
      <td>33.6</td>
      <td>70760.0</td>
      <td>32651.0</td>
      <td>69514.0</td>
      <td>9.650487</td>
      <td>4.572854</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>7.205417e+09</td>
      <td>7489.0</td>
      <td>47.0</td>
      <td>F</td>
      <td>16.0</td>
      <td>15.2</td>
      <td>F</td>
      <td>...</td>
      <td>7.0</td>
      <td>827.56</td>
      <td>15.0</td>
      <td>6479703.0</td>
      <td>36.3</td>
      <td>49774.0</td>
      <td>25358.0</td>
      <td>1131901.0</td>
      <td>17.468409</td>
      <td>4.882323</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>4.683434e+09</td>
      <td>9694.0</td>
      <td>39.0</td>
      <td>F</td>
      <td>9.0</td>
      <td>17.7</td>
      <td>F</td>
      <td>...</td>
      <td>48.0</td>
      <td>169.39</td>
      <td>50.0</td>
      <td>2933369.0</td>
      <td>37.5</td>
      <td>40768.0</td>
      <td>22170.0</td>
      <td>547328.0</td>
      <td>18.658682</td>
      <td>4.132961</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>2017.0</td>
      <td>106.0</td>
      <td>6.620811e+10</td>
      <td>10467.0</td>
      <td>1.0</td>
      <td>A</td>
      <td>43.0</td>
      <td>7.9</td>
      <td>A</td>
      <td>...</td>
      <td>15.0</td>
      <td>645.55</td>
      <td>22.0</td>
      <td>37659181.0</td>
      <td>35.4</td>
      <td>61094.0</td>
      <td>29527.0</td>
      <td>5885417.0</td>
      <td>15.628107</td>
      <td>5.758662</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 22 columns</p>
</div>




```python
# Merge gun_census with state_summary on State_Name
gun_census_deaths = gun_census.merge(state_summary, how = 'outer', on = 'State_Name')
gun_census_deaths.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>year</th>
      <th>lawtotal</th>
      <th>total_spending</th>
      <th>total_per_pupil</th>
      <th>gun law strengh (rank)</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
      <th>Letter_Grade</th>
      <th>...</th>
      <th>poverty_per_capita_rank</th>
      <th>Population</th>
      <th>Median Age</th>
      <th>Household Income</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
      <th>Unemployment Rate</th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>2017.0</td>
      <td>10.0</td>
      <td>6.826622e+09</td>
      <td>9128.0</td>
      <td>36.0</td>
      <td>F</td>
      <td>2.0</td>
      <td>21.4</td>
      <td>F</td>
      <td>...</td>
      <td>40.0</td>
      <td>4799277.0</td>
      <td>38.1</td>
      <td>43253.0</td>
      <td>23680.0</td>
      <td>870631.0</td>
      <td>18.140878</td>
      <td>5.040968</td>
      <td>1880</td>
      <td>5471</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>2017.0</td>
      <td>4.0</td>
      <td>2.646225e+09</td>
      <td>20172.0</td>
      <td>44.0</td>
      <td>F</td>
      <td>1.0</td>
      <td>23.0</td>
      <td>F</td>
      <td>...</td>
      <td>2.0</td>
      <td>720316.0</td>
      <td>33.6</td>
      <td>70760.0</td>
      <td>32651.0</td>
      <td>69514.0</td>
      <td>9.650487</td>
      <td>4.572854</td>
      <td>267</td>
      <td>1349</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>7.205417e+09</td>
      <td>7489.0</td>
      <td>47.0</td>
      <td>F</td>
      <td>16.0</td>
      <td>15.2</td>
      <td>F</td>
      <td>...</td>
      <td>15.0</td>
      <td>6479703.0</td>
      <td>36.3</td>
      <td>49774.0</td>
      <td>25358.0</td>
      <td>1131901.0</td>
      <td>17.468409</td>
      <td>4.882323</td>
      <td>1094</td>
      <td>2328</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>4.683434e+09</td>
      <td>9694.0</td>
      <td>39.0</td>
      <td>F</td>
      <td>9.0</td>
      <td>17.7</td>
      <td>F</td>
      <td>...</td>
      <td>50.0</td>
      <td>2933369.0</td>
      <td>37.5</td>
      <td>40768.0</td>
      <td>22170.0</td>
      <td>547328.0</td>
      <td>18.658682</td>
      <td>4.132961</td>
      <td>773</td>
      <td>2842</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>2017.0</td>
      <td>106.0</td>
      <td>6.620811e+10</td>
      <td>10467.0</td>
      <td>1.0</td>
      <td>A</td>
      <td>43.0</td>
      <td>7.9</td>
      <td>A</td>
      <td>...</td>
      <td>22.0</td>
      <td>37659181.0</td>
      <td>35.4</td>
      <td>61094.0</td>
      <td>29527.0</td>
      <td>5885417.0</td>
      <td>15.628107</td>
      <td>5.758662</td>
      <td>5562</td>
      <td>16306</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 24 columns</p>
</div>




```python
# Drop DC because we don't have a lot of data for it
gun_census_deaths.drop([50])
gun_census_deaths["Pop in 100k"] = gun_census_deaths["Population"]/100000
gun_census_deaths["Norm Incidents"] = gun_census_deaths["Number of Incidents"]/gun_census_deaths["Pop in 100k"]
```


```python
#add info on mental health spending per state

gun_dataframe = merge2.merge(gun_mental_df, how='outer', on='State_Name')


gun_dataframe.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>year</th>
      <th>lawtotal</th>
      <th>total_spending</th>
      <th>total_per_pupil</th>
      <th>gun law strengh (rank)</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
      <th>Letter_Grade</th>
      <th>tota_expenditure</th>
      <th>expenditure_per_capita</th>
      <th>per_capita_rank</th>
      <th>poverty_per_capita_expenditure</th>
      <th>poverty_per_capita_rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>2017.0</td>
      <td>10.0</td>
      <td>6826622000</td>
      <td>9128</td>
      <td>36.0</td>
      <td>F</td>
      <td>2.0</td>
      <td>21.4</td>
      <td>F</td>
      <td>373100000</td>
      <td>78.19</td>
      <td>37</td>
      <td>327.57</td>
      <td>40</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>2017.0</td>
      <td>4.0</td>
      <td>2646225000</td>
      <td>20172</td>
      <td>44.0</td>
      <td>F</td>
      <td>1.0</td>
      <td>23.0</td>
      <td>F</td>
      <td>214200700</td>
      <td>310.01</td>
      <td>3</td>
      <td>1785.01</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>7205417000</td>
      <td>7489</td>
      <td>47.0</td>
      <td>F</td>
      <td>16.0</td>
      <td>15.2</td>
      <td>F</td>
      <td>1414300000</td>
      <td>221.27</td>
      <td>7</td>
      <td>827.56</td>
      <td>15</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>4683434000</td>
      <td>9694</td>
      <td>39.0</td>
      <td>F</td>
      <td>9.0</td>
      <td>17.7</td>
      <td>F</td>
      <td>122468795</td>
      <td>42.02</td>
      <td>48</td>
      <td>169.39</td>
      <td>50</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>2017.0</td>
      <td>106.0</td>
      <td>66208111000</td>
      <td>10467</td>
      <td>1.0</td>
      <td>A</td>
      <td>43.0</td>
      <td>7.9</td>
      <td>A</td>
      <td>5674396088</td>
      <td>152.60</td>
      <td>15</td>
      <td>645.55</td>
      <td>22</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Add Census information
gundata_census_df = gun_dataframe.merge(census_pd, how='outer', on='State_Name')
gundata_census_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>year</th>
      <th>lawtotal</th>
      <th>total_spending</th>
      <th>total_per_pupil</th>
      <th>gun law strengh (rank)</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
      <th>Letter_Grade</th>
      <th>...</th>
      <th>per_capita_rank</th>
      <th>poverty_per_capita_expenditure</th>
      <th>poverty_per_capita_rank</th>
      <th>Population</th>
      <th>Median Age</th>
      <th>Household Income</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
      <th>Unemployment Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>2017.0</td>
      <td>10.0</td>
      <td>6.826622e+09</td>
      <td>9128.0</td>
      <td>36.0</td>
      <td>F</td>
      <td>2.0</td>
      <td>21.4</td>
      <td>F</td>
      <td>...</td>
      <td>37.0</td>
      <td>327.57</td>
      <td>40.0</td>
      <td>4799277.0</td>
      <td>38.1</td>
      <td>43253.0</td>
      <td>23680.0</td>
      <td>870631.0</td>
      <td>18.140878</td>
      <td>5.040968</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>2017.0</td>
      <td>4.0</td>
      <td>2.646225e+09</td>
      <td>20172.0</td>
      <td>44.0</td>
      <td>F</td>
      <td>1.0</td>
      <td>23.0</td>
      <td>F</td>
      <td>...</td>
      <td>3.0</td>
      <td>1785.01</td>
      <td>2.0</td>
      <td>720316.0</td>
      <td>33.6</td>
      <td>70760.0</td>
      <td>32651.0</td>
      <td>69514.0</td>
      <td>9.650487</td>
      <td>4.572854</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>7.205417e+09</td>
      <td>7489.0</td>
      <td>47.0</td>
      <td>F</td>
      <td>16.0</td>
      <td>15.2</td>
      <td>F</td>
      <td>...</td>
      <td>7.0</td>
      <td>827.56</td>
      <td>15.0</td>
      <td>6479703.0</td>
      <td>36.3</td>
      <td>49774.0</td>
      <td>25358.0</td>
      <td>1131901.0</td>
      <td>17.468409</td>
      <td>4.882323</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>4.683434e+09</td>
      <td>9694.0</td>
      <td>39.0</td>
      <td>F</td>
      <td>9.0</td>
      <td>17.7</td>
      <td>F</td>
      <td>...</td>
      <td>48.0</td>
      <td>169.39</td>
      <td>50.0</td>
      <td>2933369.0</td>
      <td>37.5</td>
      <td>40768.0</td>
      <td>22170.0</td>
      <td>547328.0</td>
      <td>18.658682</td>
      <td>4.132961</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>2017.0</td>
      <td>106.0</td>
      <td>6.620811e+10</td>
      <td>10467.0</td>
      <td>1.0</td>
      <td>A</td>
      <td>43.0</td>
      <td>7.9</td>
      <td>A</td>
      <td>...</td>
      <td>15.0</td>
      <td>645.55</td>
      <td>22.0</td>
      <td>37659181.0</td>
      <td>35.4</td>
      <td>61094.0</td>
      <td>29527.0</td>
      <td>5885417.0</td>
      <td>15.628107</td>
      <td>5.758662</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 22 columns</p>
</div>




```python
#Add lat and long for each state geo center location 
gun_census = gundata_census_df.merge(state_geo, how='outer', on='State_Name')
gun_census.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>year</th>
      <th>lawtotal</th>
      <th>total_spending</th>
      <th>total_per_pupil</th>
      <th>gun law strengh (rank)</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
      <th>Letter_Grade</th>
      <th>...</th>
      <th>Population</th>
      <th>Median Age</th>
      <th>Household Income</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
      <th>Unemployment Rate</th>
      <th>Location</th>
      <th>lat (N)</th>
      <th>lng (W)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>2017.0</td>
      <td>10.0</td>
      <td>6.826622e+09</td>
      <td>9128.0</td>
      <td>36.0</td>
      <td>F</td>
      <td>2.0</td>
      <td>21.4</td>
      <td>F</td>
      <td>...</td>
      <td>4799277.0</td>
      <td>38.1</td>
      <td>43253.0</td>
      <td>23680.0</td>
      <td>870631.0</td>
      <td>18.140878</td>
      <td>5.040968</td>
      <td>12.1 mi (19.5 km) southwest of Clanton</td>
      <td>32.7794</td>
      <td>-86.8287</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>2017.0</td>
      <td>4.0</td>
      <td>2.646225e+09</td>
      <td>20172.0</td>
      <td>44.0</td>
      <td>F</td>
      <td>1.0</td>
      <td>23.0</td>
      <td>F</td>
      <td>...</td>
      <td>720316.0</td>
      <td>33.6</td>
      <td>70760.0</td>
      <td>32651.0</td>
      <td>69514.0</td>
      <td>9.650487</td>
      <td>4.572854</td>
      <td>77.9 mi (125.4 km) northwest of Denali</td>
      <td>64.0685</td>
      <td>152.2782</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>7.205417e+09</td>
      <td>7489.0</td>
      <td>47.0</td>
      <td>F</td>
      <td>16.0</td>
      <td>15.2</td>
      <td>F</td>
      <td>...</td>
      <td>6479703.0</td>
      <td>36.3</td>
      <td>49774.0</td>
      <td>25358.0</td>
      <td>1131901.0</td>
      <td>17.468409</td>
      <td>4.882323</td>
      <td>49.7 mi (80.0 km) east-southeast of Prescott</td>
      <td>34.2744</td>
      <td>-111.6602</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>4.683434e+09</td>
      <td>9694.0</td>
      <td>39.0</td>
      <td>F</td>
      <td>9.0</td>
      <td>17.7</td>
      <td>F</td>
      <td>...</td>
      <td>2933369.0</td>
      <td>37.5</td>
      <td>40768.0</td>
      <td>22170.0</td>
      <td>547328.0</td>
      <td>18.658682</td>
      <td>4.132961</td>
      <td>14.2 mi (22.9 km) northwest of Little Rock</td>
      <td>34.8938</td>
      <td>-92.4426</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>2017.0</td>
      <td>106.0</td>
      <td>6.620811e+10</td>
      <td>10467.0</td>
      <td>1.0</td>
      <td>A</td>
      <td>43.0</td>
      <td>7.9</td>
      <td>A</td>
      <td>...</td>
      <td>37659181.0</td>
      <td>35.4</td>
      <td>61094.0</td>
      <td>29527.0</td>
      <td>5885417.0</td>
      <td>15.628107</td>
      <td>5.758662</td>
      <td>36 mi (58 km) northeast of Madera,</td>
      <td>37.1841</td>
      <td>-119.4696</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 25 columns</p>
</div>




```python
gun_final = gun_census.merge(state_summary, how='outer', on='State_Name')
gun_final
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>year</th>
      <th>lawtotal</th>
      <th>total_spending</th>
      <th>total_per_pupil</th>
      <th>gun law strengh (rank)</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
      <th>Letter_Grade</th>
      <th>...</th>
      <th>Household Income</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
      <th>Unemployment Rate</th>
      <th>Location</th>
      <th>lat (N)</th>
      <th>lng (W)</th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>2017.0</td>
      <td>10.0</td>
      <td>6.826622e+09</td>
      <td>9128.0</td>
      <td>36.0</td>
      <td>F</td>
      <td>2.0</td>
      <td>21.4</td>
      <td>F</td>
      <td>...</td>
      <td>43253.0</td>
      <td>23680.0</td>
      <td>870631.0</td>
      <td>18.140878</td>
      <td>5.040968</td>
      <td>12.1 mi (19.5 km) southwest of Clanton</td>
      <td>32.7794</td>
      <td>-86.8287</td>
      <td>1880.0</td>
      <td>5471.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>2017.0</td>
      <td>4.0</td>
      <td>2.646225e+09</td>
      <td>20172.0</td>
      <td>44.0</td>
      <td>F</td>
      <td>1.0</td>
      <td>23.0</td>
      <td>F</td>
      <td>...</td>
      <td>70760.0</td>
      <td>32651.0</td>
      <td>69514.0</td>
      <td>9.650487</td>
      <td>4.572854</td>
      <td>77.9 mi (125.4 km) northwest of Denali</td>
      <td>64.0685</td>
      <td>152.2782</td>
      <td>267.0</td>
      <td>1349.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>7.205417e+09</td>
      <td>7489.0</td>
      <td>47.0</td>
      <td>F</td>
      <td>16.0</td>
      <td>15.2</td>
      <td>F</td>
      <td>...</td>
      <td>49774.0</td>
      <td>25358.0</td>
      <td>1131901.0</td>
      <td>17.468409</td>
      <td>4.882323</td>
      <td>49.7 mi (80.0 km) east-southeast of Prescott</td>
      <td>34.2744</td>
      <td>-111.6602</td>
      <td>1094.0</td>
      <td>2328.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>4.683434e+09</td>
      <td>9694.0</td>
      <td>39.0</td>
      <td>F</td>
      <td>9.0</td>
      <td>17.7</td>
      <td>F</td>
      <td>...</td>
      <td>40768.0</td>
      <td>22170.0</td>
      <td>547328.0</td>
      <td>18.658682</td>
      <td>4.132961</td>
      <td>14.2 mi (22.9 km) northwest of Little Rock</td>
      <td>34.8938</td>
      <td>-92.4426</td>
      <td>773.0</td>
      <td>2842.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>2017.0</td>
      <td>106.0</td>
      <td>6.620811e+10</td>
      <td>10467.0</td>
      <td>1.0</td>
      <td>A</td>
      <td>43.0</td>
      <td>7.9</td>
      <td>A</td>
      <td>...</td>
      <td>61094.0</td>
      <td>29527.0</td>
      <td>5885417.0</td>
      <td>15.628107</td>
      <td>5.758662</td>
      <td>36 mi (58 km) northeast of Madera,</td>
      <td>37.1841</td>
      <td>-119.4696</td>
      <td>5562.0</td>
      <td>16306.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Colorado</td>
      <td>2017.0</td>
      <td>30.0</td>
      <td>8.146683e+09</td>
      <td>9245.0</td>
      <td>15.0</td>
      <td>C</td>
      <td>20.0</td>
      <td>14.3</td>
      <td>C</td>
      <td>...</td>
      <td>58433.0</td>
      <td>31109.0</td>
      <td>660874.0</td>
      <td>12.909387</td>
      <td>4.526785</td>
      <td>29.2 mi (47.0 km) northwest of Pikes Peak,</td>
      <td>38.9972</td>
      <td>-105.5478</td>
      <td>796.0</td>
      <td>3201.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Connecticut</td>
      <td>2017.0</td>
      <td>89.0</td>
      <td>9.603117e+09</td>
      <td>18377.0</td>
      <td>3.0</td>
      <td>A-</td>
      <td>46.0</td>
      <td>4.6</td>
      <td>A</td>
      <td>...</td>
      <td>69461.0</td>
      <td>37892.0</td>
      <td>354348.0</td>
      <td>9.888153</td>
      <td>5.321774</td>
      <td>1.1 mi (1.8 km) east of East Berlin</td>
      <td>41.6219</td>
      <td>-72.7273</td>
      <td>341.0</td>
      <td>3067.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Delaware</td>
      <td>2017.0</td>
      <td>40.0</td>
      <td>1.779087e+09</td>
      <td>14120.0</td>
      <td>11.0</td>
      <td>B</td>
      <td>37.0</td>
      <td>10.9</td>
      <td>B</td>
      <td>...</td>
      <td>59878.0</td>
      <td>29819.0</td>
      <td>103633.0</td>
      <td>11.407723</td>
      <td>4.535988</td>
      <td>11.7 mi (18.8 km) south of Dover</td>
      <td>38.9896</td>
      <td>-75.5050</td>
      <td>217.0</td>
      <td>1685.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Florida</td>
      <td>2017.0</td>
      <td>21.0</td>
      <td>2.493343e+10</td>
      <td>8881.0</td>
      <td>26.0</td>
      <td>F</td>
      <td>26.0</td>
      <td>12.6</td>
      <td>F</td>
      <td>...</td>
      <td>46956.0</td>
      <td>26236.0</td>
      <td>3052807.0</td>
      <td>15.990687</td>
      <td>5.741025</td>
      <td>5.8 mi (9.3 km) northwest of Brooksville</td>
      <td>28.6305</td>
      <td>-82.4497</td>
      <td>3909.0</td>
      <td>15029.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Georgia</td>
      <td>2017.0</td>
      <td>6.0</td>
      <td>1.640183e+10</td>
      <td>9427.0</td>
      <td>32.0</td>
      <td>F</td>
      <td>17.0</td>
      <td>14.9</td>
      <td>F</td>
      <td>...</td>
      <td>49179.0</td>
      <td>25182.0</td>
      <td>1736680.0</td>
      <td>17.702408</td>
      <td>5.553301</td>
      <td>17.7 mi (28.5 km) southeast of Macon</td>
      <td>32.6415</td>
      <td>-83.4426</td>
      <td>2456.0</td>
      <td>8925.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Hawaii</td>
      <td>2017.0</td>
      <td>78.0</td>
      <td>2.359203e+09</td>
      <td>12855.0</td>
      <td>7.0</td>
      <td>A-</td>
      <td>47.0</td>
      <td>4.5</td>
      <td>A</td>
      <td>...</td>
      <td>67402.0</td>
      <td>29305.0</td>
      <td>149531.0</td>
      <td>10.864726</td>
      <td>3.541965</td>
      <td>27.7 mi (44.6 km) off-shore south of Wailea-Ma...</td>
      <td>20.2927</td>
      <td>-156.3737</td>
      <td>63.0</td>
      <td>289.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Idaho</td>
      <td>2017.0</td>
      <td>4.0</td>
      <td>1.902503e+09</td>
      <td>6923.0</td>
      <td>46.0</td>
      <td>F</td>
      <td>19.0</td>
      <td>14.6</td>
      <td>F</td>
      <td>...</td>
      <td>46767.0</td>
      <td>22568.0</td>
      <td>240298.0</td>
      <td>15.176422</td>
      <td>4.243434</td>
      <td>21.4 mi (34.4 km) west-southwest of Challis</td>
      <td>44.3509</td>
      <td>-114.6130</td>
      <td>166.0</td>
      <td>661.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Illinois</td>
      <td>2017.0</td>
      <td>64.0</td>
      <td>2.858494e+10</td>
      <td>13755.0</td>
      <td>8.0</td>
      <td>B+</td>
      <td>34.0</td>
      <td>11.6</td>
      <td>B</td>
      <td>...</td>
      <td>56797.0</td>
      <td>29666.0</td>
      <td>1772333.0</td>
      <td>13.794027</td>
      <td>5.452878</td>
      <td>28.8 mi (46.3 km) northeast of Springfield</td>
      <td>40.0417</td>
      <td>-89.1965</td>
      <td>3409.0</td>
      <td>17556.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Indiana</td>
      <td>2017.0</td>
      <td>12.0</td>
      <td>9.797058e+09</td>
      <td>9687.0</td>
      <td>23.0</td>
      <td>D-</td>
      <td>18.0</td>
      <td>14.9</td>
      <td>D</td>
      <td>...</td>
      <td>48248.0</td>
      <td>24635.0</td>
      <td>969881.0</td>
      <td>14.887209</td>
      <td>4.842406</td>
      <td>10.8 mi (17.4 km) northwest of Indianapolis</td>
      <td>39.8942</td>
      <td>-86.2816</td>
      <td>1608.0</td>
      <td>5852.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Iowa</td>
      <td>2017.0</td>
      <td>24.0</td>
      <td>5.558398e+09</td>
      <td>10944.0</td>
      <td>18.0</td>
      <td>C-</td>
      <td>39.0</td>
      <td>9.2</td>
      <td>C</td>
      <td>...</td>
      <td>51843.0</td>
      <td>27027.0</td>
      <td>367414.0</td>
      <td>11.996984</td>
      <td>3.099146</td>
      <td>7 mi (11 km) northeast of Ames</td>
      <td>42.0751</td>
      <td>-93.4960</td>
      <td>273.0</td>
      <td>2517.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Kansas</td>
      <td>2017.0</td>
      <td>7.0</td>
      <td>4.995292e+09</td>
      <td>10040.0</td>
      <td>48.0</td>
      <td>F</td>
      <td>23.0</td>
      <td>13.3</td>
      <td>F</td>
      <td>...</td>
      <td>51332.0</td>
      <td>26929.0</td>
      <td>382820.0</td>
      <td>13.347480</td>
      <td>3.637974</td>
      <td>22.6 mi (36.4 km) northeast of Great Bend</td>
      <td>38.4937</td>
      <td>-98.3804</td>
      <td>542.0</td>
      <td>2145.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Kentucky</td>
      <td>2017.0</td>
      <td>7.0</td>
      <td>6.693668e+09</td>
      <td>9630.0</td>
      <td>42.0</td>
      <td>F</td>
      <td>13.0</td>
      <td>17.5</td>
      <td>F</td>
      <td>...</td>
      <td>43036.0</td>
      <td>23462.0</td>
      <td>796202.0</td>
      <td>18.255932</td>
      <td>4.631313</td>
      <td>3.4 mi (5.5 km) southwest of Lebanon</td>
      <td>37.5347</td>
      <td>-85.3021</td>
      <td>1066.0</td>
      <td>4157.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Louisiana</td>
      <td>2017.0</td>
      <td>12.0</td>
      <td>7.291286e+09</td>
      <td>11010.0</td>
      <td>43.0</td>
      <td>F</td>
      <td>3.0</td>
      <td>21.2</td>
      <td>F</td>
      <td>...</td>
      <td>44874.0</td>
      <td>24442.0</td>
      <td>846780.0</td>
      <td>18.537345</td>
      <td>4.206816</td>
      <td>5.8 mi (9.3 km) southeast of Marksville</td>
      <td>31.0689</td>
      <td>-91.9968</td>
      <td>2179.0</td>
      <td>8103.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Maine</td>
      <td>2017.0</td>
      <td>12.0</td>
      <td>2.426820e+09</td>
      <td>13257.0</td>
      <td>35.0</td>
      <td>F</td>
      <td>42.0</td>
      <td>8.2</td>
      <td>F</td>
      <td>...</td>
      <td>48453.0</td>
      <td>26824.0</td>
      <td>175624.0</td>
      <td>13.221513</td>
      <td>4.055198</td>
      <td>12.6 mi (20.3 km) northwest of Dover-Foxcroft</td>
      <td>45.3695</td>
      <td>-69.2428</td>
      <td>112.0</td>
      <td>907.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Maryland</td>
      <td>2017.0</td>
      <td>64.0</td>
      <td>1.243054e+10</td>
      <td>14192.0</td>
      <td>6.0</td>
      <td>A-</td>
      <td>32.0</td>
      <td>11.8</td>
      <td>A</td>
      <td>...</td>
      <td>73538.0</td>
      <td>36354.0</td>
      <td>558329.0</td>
      <td>9.569770</td>
      <td>4.466124</td>
      <td>12.6 mi (20.3 km) northwest of Davidsonville</td>
      <td>39.0550</td>
      <td>-76.7909</td>
      <td>1691.0</td>
      <td>5798.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Massachusetts</td>
      <td>2017.0</td>
      <td>100.0</td>
      <td>1.531580e+10</td>
      <td>15592.0</td>
      <td>4.0</td>
      <td>A-</td>
      <td>50.0</td>
      <td>3.4</td>
      <td>A</td>
      <td>...</td>
      <td>66866.0</td>
      <td>35763.0</td>
      <td>725351.0</td>
      <td>10.981751</td>
      <td>4.886104</td>
      <td>Irving St. and Wellington St., Worcester</td>
      <td>42.2596</td>
      <td>-71.8083</td>
      <td>472.0</td>
      <td>5981.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Michigan</td>
      <td>2017.0</td>
      <td>21.0</td>
      <td>1.571227e+10</td>
      <td>11482.0</td>
      <td>16.0</td>
      <td>C</td>
      <td>27.0</td>
      <td>12.2</td>
      <td>C</td>
      <td>...</td>
      <td>48411.0</td>
      <td>25681.0</td>
      <td>1625982.0</td>
      <td>16.447161</td>
      <td>6.235718</td>
      <td>6.6 mi (10.6 km) west of Cadillac</td>
      <td>44.3467</td>
      <td>-85.4102</td>
      <td>1588.0</td>
      <td>6136.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Minnesota</td>
      <td>2017.0</td>
      <td>41.0</td>
      <td>1.008513e+10</td>
      <td>11949.0</td>
      <td>12.0</td>
      <td>C+</td>
      <td>44.0</td>
      <td>7.6</td>
      <td>C</td>
      <td>...</td>
      <td>59836.0</td>
      <td>30913.0</td>
      <td>598391.0</td>
      <td>11.189605</td>
      <td>3.942133</td>
      <td>10 mi (16 km) southwest of Brainerd</td>
      <td>46.2807</td>
      <td>-94.3053</td>
      <td>461.0</td>
      <td>2408.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Mississippi</td>
      <td>2017.0</td>
      <td>5.0</td>
      <td>4.161329e+09</td>
      <td>8456.0</td>
      <td>50.0</td>
      <td>F</td>
      <td>4.0</td>
      <td>19.8</td>
      <td>F</td>
      <td>...</td>
      <td>39031.0</td>
      <td>20618.0</td>
      <td>653321.0</td>
      <td>21.946560</td>
      <td>5.010225</td>
      <td>7.7 mi (12.4 km) west-northwest of Carthage</td>
      <td>32.7364</td>
      <td>-89.6678</td>
      <td>1176.0</td>
      <td>3599.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Missouri</td>
      <td>2017.0</td>
      <td>6.0</td>
      <td>9.269713e+09</td>
      <td>10147.0</td>
      <td>48.0</td>
      <td>F</td>
      <td>7.0</td>
      <td>18.8</td>
      <td>F</td>
      <td>...</td>
      <td>47380.0</td>
      <td>25649.0</td>
      <td>900929.0</td>
      <td>14.997531</td>
      <td>4.455400</td>
      <td>21 mi (34 km) southwest of Jefferson City</td>
      <td>38.3566</td>
      <td>-92.4580</td>
      <td>2136.0</td>
      <td>6631.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Montana</td>
      <td>2017.0</td>
      <td>4.0</td>
      <td>1.602608e+09</td>
      <td>11028.0</td>
      <td>37.0</td>
      <td>F</td>
      <td>6.0</td>
      <td>19.0</td>
      <td>F</td>
      <td>...</td>
      <td>46230.0</td>
      <td>25373.0</td>
      <td>148528.0</td>
      <td>14.874308</td>
      <td>3.775660</td>
      <td>10.1 mi (16.3 km) west of Lewistown</td>
      <td>47.0527</td>
      <td>-109.6333</td>
      <td>154.0</td>
      <td>638.0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Nebraska</td>
      <td>2017.0</td>
      <td>22.0</td>
      <td>3.732390e+09</td>
      <td>11946.0</td>
      <td>19.0</td>
      <td>D</td>
      <td>40.0</td>
      <td>9.1</td>
      <td>D</td>
      <td>...</td>
      <td>51672.0</td>
      <td>26899.0</td>
      <td>228789.0</td>
      <td>12.423213</td>
      <td>3.123600</td>
      <td>12.3 mi (19.8 km) northwest of Broken Bow</td>
      <td>41.5378</td>
      <td>-99.7951</td>
      <td>225.0</td>
      <td>1651.0</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Nevada</td>
      <td>2017.0</td>
      <td>21.0</td>
      <td>3.804316e+09</td>
      <td>8615.0</td>
      <td>22.0</td>
      <td>D</td>
      <td>15.0</td>
      <td>16.7</td>
      <td>D</td>
      <td>...</td>
      <td>52800.0</td>
      <td>26589.0</td>
      <td>403314.0</td>
      <td>14.773049</td>
      <td>6.415303</td>
      <td>26 mi (42 km) southeast of Austin</td>
      <td>39.3289</td>
      <td>-116.6312</td>
      <td>799.0</td>
      <td>1952.0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>New Hampshire</td>
      <td>2017.0</td>
      <td>9.0</td>
      <td>2.711114e+09</td>
      <td>14697.0</td>
      <td>31.0</td>
      <td>F</td>
      <td>38.0</td>
      <td>9.3</td>
      <td>F</td>
      <td>...</td>
      <td>64916.0</td>
      <td>33134.0</td>
      <td>110859.0</td>
      <td>8.403687</td>
      <td>3.916854</td>
      <td>2.6 mi (4.2 km) east of Ashland</td>
      <td>43.6805</td>
      <td>-71.5811</td>
      <td>88.0</td>
      <td>964.0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>New Jersey</td>
      <td>2017.0</td>
      <td>75.0</td>
      <td>2.602534e+10</td>
      <td>18235.0</td>
      <td>2.0</td>
      <td>A-</td>
      <td>45.0</td>
      <td>5.5</td>
      <td>A</td>
      <td>...</td>
      <td>71629.0</td>
      <td>36027.0</td>
      <td>900583.0</td>
      <td>10.196350</td>
      <td>5.345022</td>
      <td>4.3 mi (6.9 km) southeast of Trenton</td>
      <td>40.1907</td>
      <td>-74.6728</td>
      <td>1168.0</td>
      <td>5387.0</td>
    </tr>
    <tr>
      <th>30</th>
      <td>New Mexico</td>
      <td>2017.0</td>
      <td>10.0</td>
      <td>3.184539e+09</td>
      <td>9752.0</td>
      <td>29.0</td>
      <td>F</td>
      <td>8.0</td>
      <td>18.2</td>
      <td>F</td>
      <td>...</td>
      <td>44927.0</td>
      <td>23763.0</td>
      <td>413801.0</td>
      <td>19.993226</td>
      <td>4.547071</td>
      <td>13.8 mi (22.2 km) south-southwest of Willard</td>
      <td>34.4071</td>
      <td>-106.1126</td>
      <td>495.0</td>
      <td>1645.0</td>
    </tr>
    <tr>
      <th>31</th>
      <td>New York</td>
      <td>2017.0</td>
      <td>75.0</td>
      <td>5.920991e+10</td>
      <td>21206.0</td>
      <td>5.0</td>
      <td>A-</td>
      <td>48.0</td>
      <td>4.4</td>
      <td>A</td>
      <td>...</td>
      <td>58003.0</td>
      <td>32382.0</td>
      <td>2903982.0</td>
      <td>14.902110</td>
      <td>4.729330</td>
      <td>11.5 mi (18.5 km) south-southeast of Oneida</td>
      <td>42.9538</td>
      <td>-75.5268</td>
      <td>1705.0</td>
      <td>9712.0</td>
    </tr>
    <tr>
      <th>32</th>
      <td>North Carolina</td>
      <td>2017.0</td>
      <td>30.0</td>
      <td>1.278930e+10</td>
      <td>8687.0</td>
      <td>25.0</td>
      <td>D-</td>
      <td>21.0</td>
      <td>13.6</td>
      <td>D</td>
      <td>...</td>
      <td>46334.0</td>
      <td>25284.0</td>
      <td>1643389.0</td>
      <td>17.027503</td>
      <td>5.464814</td>
      <td>12.7 mi (20.4 km) northwest of Sanford</td>
      <td>35.5557</td>
      <td>-79.3877</td>
      <td>2225.0</td>
      <td>8739.0</td>
    </tr>
    <tr>
      <th>33</th>
      <td>North Dakota</td>
      <td>2017.0</td>
      <td>13.0</td>
      <td>1.420265e+09</td>
      <td>13320.0</td>
      <td>37.0</td>
      <td>F</td>
      <td>30.0</td>
      <td>11.9</td>
      <td>F</td>
      <td>...</td>
      <td>53741.0</td>
      <td>29732.0</td>
      <td>79433.0</td>
      <td>11.515684</td>
      <td>1.829131</td>
      <td>2.7 mi (4.3 km) southwest of McClusky</td>
      <td>47.4501</td>
      <td>-100.4659</td>
      <td>69.0</td>
      <td>573.0</td>
    </tr>
    <tr>
      <th>34</th>
      <td>Ohio</td>
      <td>2017.0</td>
      <td>16.0</td>
      <td>1.986726e+10</td>
      <td>11637.0</td>
      <td>21.0</td>
      <td>D</td>
      <td>25.0</td>
      <td>12.9</td>
      <td>D</td>
      <td>...</td>
      <td>48308.0</td>
      <td>26046.0</td>
      <td>1773853.0</td>
      <td>15.358580</td>
      <td>5.047391</td>
      <td>24.9 mi (40.1 km) north-northeast of Columbus</td>
      <td>40.2862</td>
      <td>-82.7937</td>
      <td>2508.0</td>
      <td>10244.0</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Oklahoma</td>
      <td>2017.0</td>
      <td>9.0</td>
      <td>5.456487e+09</td>
      <td>8082.0</td>
      <td>27.0</td>
      <td>F</td>
      <td>5.0</td>
      <td>19.6</td>
      <td>F</td>
      <td>...</td>
      <td>45339.0</td>
      <td>24208.0</td>
      <td>618683.0</td>
      <td>16.342450</td>
      <td>3.376934</td>
      <td>4.4 mi (7.1 km) south of Edmond</td>
      <td>35.5889</td>
      <td>-97.4943</td>
      <td>941.0</td>
      <td>3455.0</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Oregon</td>
      <td>2017.0</td>
      <td>35.0</td>
      <td>6.173205e+09</td>
      <td>10442.0</td>
      <td>14.0</td>
      <td>C</td>
      <td>33.0</td>
      <td>11.8</td>
      <td>C</td>
      <td>...</td>
      <td>50229.0</td>
      <td>26809.0</td>
      <td>614778.0</td>
      <td>15.890988</td>
      <td>5.717419</td>
      <td>29.1 mi (46.8 km) southeast of Prineville</td>
      <td>43.9336</td>
      <td>-120.5583</td>
      <td>446.0</td>
      <td>2286.0</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Pennsylvania</td>
      <td>2017.0</td>
      <td>36.0</td>
      <td>2.530540e+10</td>
      <td>14717.0</td>
      <td>13.0</td>
      <td>C</td>
      <td>31.0</td>
      <td>11.9</td>
      <td>C</td>
      <td>...</td>
      <td>52548.0</td>
      <td>28502.0</td>
      <td>1638820.0</td>
      <td>12.872288</td>
      <td>4.567713</td>
      <td>2.7 mi (4.3 km) southwest of Bellefonte, in St...</td>
      <td>40.8781</td>
      <td>-77.7996</td>
      <td>2395.0</td>
      <td>8929.0</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Rhode Island</td>
      <td>2017.0</td>
      <td>43.0</td>
      <td>2.204789e+09</td>
      <td>15179.0</td>
      <td>9.0</td>
      <td>B+</td>
      <td>49.0</td>
      <td>4.0</td>
      <td>B</td>
      <td>...</td>
      <td>56361.0</td>
      <td>30469.0</td>
      <td>137244.0</td>
      <td>13.049791</td>
      <td>5.336623</td>
      <td>2.4-mile (3.9 km) west of Crompton, in West Wa...</td>
      <td>41.6762</td>
      <td>-71.5562</td>
      <td>63.0</td>
      <td>895.0</td>
    </tr>
    <tr>
      <th>39</th>
      <td>South Carolina</td>
      <td>2017.0</td>
      <td>12.0</td>
      <td>7.463268e+09</td>
      <td>9953.0</td>
      <td>29.0</td>
      <td>F</td>
      <td>10.0</td>
      <td>17.7</td>
      <td>F</td>
      <td>...</td>
      <td>44779.0</td>
      <td>23943.0</td>
      <td>820308.0</td>
      <td>17.529439</td>
      <td>5.522777</td>
      <td>9.7 mi (15.6 km) southeast of Columbia</td>
      <td>33.9169</td>
      <td>-80.8964</td>
      <td>1610.0</td>
      <td>6939.0</td>
    </tr>
    <tr>
      <th>40</th>
      <td>South Dakota</td>
      <td>2017.0</td>
      <td>5.0</td>
      <td>1.205748e+09</td>
      <td>8937.0</td>
      <td>40.0</td>
      <td>F</td>
      <td>22.0</td>
      <td>13.5</td>
      <td>F</td>
      <td>...</td>
      <td>49495.0</td>
      <td>25740.0</td>
      <td>112086.0</td>
      <td>13.582922</td>
      <td>2.696323</td>
      <td>7.9 mi (12.7 km) northeast of Pierre</td>
      <td>44.4443</td>
      <td>-100.2263</td>
      <td>90.0</td>
      <td>544.0</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Tennessee</td>
      <td>2017.0</td>
      <td>22.0</td>
      <td>8.766468e+09</td>
      <td>8726.0</td>
      <td>24.0</td>
      <td>D-</td>
      <td>14.0</td>
      <td>17.0</td>
      <td>D</td>
      <td>...</td>
      <td>44298.0</td>
      <td>24409.0</td>
      <td>1101732.0</td>
      <td>17.208144</td>
      <td>4.946311</td>
      <td>2.5 mi (4.0 km) northeast of Murfreesboro</td>
      <td>35.8580</td>
      <td>-86.3505</td>
      <td>1824.0</td>
      <td>7626.0</td>
    </tr>
    <tr>
      <th>42</th>
      <td>Texas</td>
      <td>2017.0</td>
      <td>18.0</td>
      <td>4.467523e+10</td>
      <td>8861.0</td>
      <td>32.0</td>
      <td>F</td>
      <td>28.0</td>
      <td>12.1</td>
      <td>F</td>
      <td>...</td>
      <td>51900.0</td>
      <td>26019.0</td>
      <td>4416829.0</td>
      <td>17.226743</td>
      <td>3.978771</td>
      <td>23.6 mi (38.0 km) north of Brady</td>
      <td>31.4757</td>
      <td>-99.3312</td>
      <td>5046.0</td>
      <td>13577.0</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Utah</td>
      <td>2017.0</td>
      <td>13.0</td>
      <td>3.805778e+09</td>
      <td>6575.0</td>
      <td>27.0</td>
      <td>F</td>
      <td>24.0</td>
      <td>12.9</td>
      <td>F</td>
      <td>...</td>
      <td>58821.0</td>
      <td>23873.0</td>
      <td>350205.0</td>
      <td>12.446542</td>
      <td>3.605572</td>
      <td>3.1 mi (5.0 km) northwest of Manti</td>
      <td>39.3055</td>
      <td>-111.6703</td>
      <td>282.0</td>
      <td>1072.0</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Vermont</td>
      <td>2017.0</td>
      <td>6.0</td>
      <td>1.612052e+09</td>
      <td>18039.0</td>
      <td>41.0</td>
      <td>F</td>
      <td>36.0</td>
      <td>11.0</td>
      <td>F</td>
      <td>...</td>
      <td>54267.0</td>
      <td>29167.0</td>
      <td>70873.0</td>
      <td>11.323302</td>
      <td>3.761919</td>
      <td>3.7 mi (6.0 km) southeast of Roxbury</td>
      <td>44.0687</td>
      <td>-72.6658</td>
      <td>57.0</td>
      <td>472.0</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Virginia</td>
      <td>2017.0</td>
      <td>13.0</td>
      <td>1.445888e+10</td>
      <td>11237.0</td>
      <td>20.0</td>
      <td>D</td>
      <td>29.0</td>
      <td>12.0</td>
      <td>D</td>
      <td>...</td>
      <td>63907.0</td>
      <td>33493.0</td>
      <td>887595.0</td>
      <td>10.957080</td>
      <td>3.742032</td>
      <td>16.6 mi (26.7 km) west of Buckingham</td>
      <td>37.5215</td>
      <td>-78.8537</td>
      <td>1459.0</td>
      <td>5949.0</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Washington</td>
      <td>2017.0</td>
      <td>43.0</td>
      <td>1.156656e+10</td>
      <td>10735.0</td>
      <td>10.0</td>
      <td>B</td>
      <td>41.0</td>
      <td>9.0</td>
      <td>B</td>
      <td>...</td>
      <td>59478.0</td>
      <td>30742.0</td>
      <td>893211.0</td>
      <td>13.097744</td>
      <td>4.797290</td>
      <td>7 mi (11 km) west-southwest of Wenatchee</td>
      <td>47.3826</td>
      <td>-120.4472</td>
      <td>853.0</td>
      <td>3434.0</td>
    </tr>
    <tr>
      <th>47</th>
      <td>West Virginia</td>
      <td>2017.0</td>
      <td>21.0</td>
      <td>3.215683e+09</td>
      <td>11359.0</td>
      <td>32.0</td>
      <td>F</td>
      <td>12.0</td>
      <td>17.5</td>
      <td>F</td>
      <td>...</td>
      <td>41043.0</td>
      <td>22966.0</td>
      <td>321546.0</td>
      <td>17.346931</td>
      <td>3.741384</td>
      <td>4.9 mi (7.9 km) east-southeast of Sutton</td>
      <td>38.6409</td>
      <td>-80.6227</td>
      <td>335.0</td>
      <td>1575.0</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Wisconsin</td>
      <td>2017.0</td>
      <td>23.0</td>
      <td>9.928685e+09</td>
      <td>11375.0</td>
      <td>17.0</td>
      <td>C-</td>
      <td>35.0</td>
      <td>11.4</td>
      <td>C</td>
      <td>...</td>
      <td>52413.0</td>
      <td>27523.0</td>
      <td>723730.0</td>
      <td>12.681730</td>
      <td>4.196065</td>
      <td>9.3 mi (15.0 km) east-southeast of Marshfield</td>
      <td>44.6243</td>
      <td>-89.9941</td>
      <td>862.0</td>
      <td>4787.0</td>
    </tr>
    <tr>
      <th>49</th>
      <td>Wyoming</td>
      <td>2017.0</td>
      <td>9.0</td>
      <td>1.513919e+09</td>
      <td>16055.0</td>
      <td>45.0</td>
      <td>F</td>
      <td>11.0</td>
      <td>17.5</td>
      <td>F</td>
      <td>...</td>
      <td>57406.0</td>
      <td>28902.0</td>
      <td>63743.0</td>
      <td>11.180354</td>
      <td>2.939660</td>
      <td>60.6 mi (97.5 km) east-northeast of Lander</td>
      <td>42.9957</td>
      <td>-107.5512</td>
      <td>73.0</td>
      <td>494.0</td>
    </tr>
    <tr>
      <th>50</th>
      <td>District of Columbia</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>9.790400e+08</td>
      <td>19396.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>65830.0</td>
      <td>45290.0</td>
      <td>109200.0</td>
      <td>17.630790</td>
      <td>6.295742</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>459.0</td>
      <td>3195.0</td>
    </tr>
    <tr>
      <th>51</th>
      <td>Puerto Rico</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>19624.0</td>
      <td>11068.0</td>
      <td>1644128.0</td>
      <td>44.641411</td>
      <td>6.728924</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>52 rows × 27 columns</p>
</div>




```python
#Check all columns names, rename colums, add State_Code
gun_final.columns
```




    Index(['State_Name', 'year', 'lawtotal', 'total_spending', 'total_per_pupil',
           'gun law strengh (rank)', 'grade (2017)', 'gun death rate (rank)',
           'gun death rate (rank per 100k)', 'Letter_Grade', 'tota_expenditure',
           'expenditure_per_capita', 'per_capita_rank',
           'poverty_per_capita_expenditure', 'poverty_per_capita_rank',
           'Population', 'Median Age', 'Household Income', 'Per Capita Income',
           'Poverty Count', 'Poverty Rate', 'Unemployment Rate', 'Location',
           'lat (N)', 'lng (W)', 'Number of Deaths', 'Number of Incidents'],
          dtype='object')




```python

# Using .rename(columns={}) in order to rename columns# Using . 
renamed_gun_df = gun_final.rename(columns={"lawtotal":"Number_of_Laws", "gun death rate (rank)":"Rank_Number_Death", "gun death rate (rank per 100k)": "Rank_Number_Death_per_100k","total_spending":"Education_Investment", "gun law strengh (rank)":"Rank_Law_Strenght", "tota_expenditure":"Mental_Health_Investment"})
renamed_gun_df.columns
```




    Index(['State_Name', 'year', 'Number_of_Laws', 'Education_Investment',
           'total_per_pupil', 'Rank_Law_Strenght', 'grade (2017)',
           'Rank_Number_Death', 'Rank_Number_Death_per_100k', 'Letter_Grade',
           'Mental_Health_Investment', 'expenditure_per_capita', 'per_capita_rank',
           'poverty_per_capita_expenditure', 'poverty_per_capita_rank',
           'Population', 'Median Age', 'Household Income', 'Per Capita Income',
           'Poverty Count', 'Poverty Rate', 'Unemployment Rate', 'Location',
           'lat (N)', 'lng (W)', 'Number of Deaths', 'Number of Incidents'],
          dtype='object')




```python
# Drop DC because we don't have enough information for it
one_renamed = renamed_gun_df[renamed_gun_df.State_Name !='District of Columbia']
```


```python
# Drop Puerto Rico because we don't have enough information for it
two_renamed = one_renamed[one_renamed.State_Name !='Puerto Rico']

```


```python
two_renamed['code']='AL', 'AK', 'AZ', 'AR', 'CA', 'CO', 'CT', 'DE', 'FL', 'GA', 'HI', 'ID', 'IL', 'IN', 'IA', 'KS', 'KY', 'LA', 'ME', 'MD', 'MA', 'MI', 'MN', 'MS', 'MO', 'MT', 'NE', 'NV', 'NH', 'NJ', 'NM', 'NY', 'NC', 'ND', 'OH', 'OK', 'OR', 'PA', 'RI', 'SC', 'SD', 'TN', 'TX', 'UT', 'VT', 'VA', 'WA', 'WV', 'WI', 'WY'
two_renamed.head()

#Save full dataframe to csv
two_renamed.to_csv("Resources\gun_dataframe.csv")
```

    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/ipykernel_launcher.py:1: SettingWithCopyWarning:
    
    
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    



```python
#Create smaller dataframe to use plot maps. Add Normalized Values to df
plotly_gun_df = two_renamed[['State_Name', 'code', 'lat (N)', 'lng (W)','Population', 'Number of Deaths', 'Number of Incidents']]
plotly_gun_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>code</th>
      <th>lat (N)</th>
      <th>lng (W)</th>
      <th>Population</th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>AL</td>
      <td>32.7794</td>
      <td>-86.8287</td>
      <td>4799277.0</td>
      <td>1880.0</td>
      <td>5471.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>AK</td>
      <td>64.0685</td>
      <td>152.2782</td>
      <td>720316.0</td>
      <td>267.0</td>
      <td>1349.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>AZ</td>
      <td>34.2744</td>
      <td>-111.6602</td>
      <td>6479703.0</td>
      <td>1094.0</td>
      <td>2328.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>AR</td>
      <td>34.8938</td>
      <td>-92.4426</td>
      <td>2933369.0</td>
      <td>773.0</td>
      <td>2842.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>CA</td>
      <td>37.1841</td>
      <td>-119.4696</td>
      <td>37659181.0</td>
      <td>5562.0</td>
      <td>16306.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
normalized_death = round(plotly_gun_df['Number of Deaths']/plotly_gun_df['Population']*100000,2)
normalized_death.head()
```




    0    39.17
    1    37.07
    2    16.88
    3    26.35
    4    14.77
    dtype: float64




```python
normalized_incidents = round(plotly_gun_df['Number of Incidents']/plotly_gun_df['Population']*100000,2)
normalized_incidents.head()
```




    0    114.00
    1    187.28
    2     35.93
    3     96.89
    4     43.30
    dtype: float64




```python
plotly_gun_df["Norm Incidents"] = normalized_incidents
plotly_gun_df["Norm Deaths"] = normalized_death

plotly_gun_df
```

    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/ipykernel_launcher.py:1: SettingWithCopyWarning:
    
    
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    
    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/ipykernel_launcher.py:2: SettingWithCopyWarning:
    
    
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>code</th>
      <th>lat (N)</th>
      <th>lng (W)</th>
      <th>Population</th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
      <th>Norm Incidents</th>
      <th>Norm Deaths</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>AL</td>
      <td>32.7794</td>
      <td>-86.8287</td>
      <td>4799277.0</td>
      <td>1880.0</td>
      <td>5471.0</td>
      <td>114.00</td>
      <td>39.17</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>AK</td>
      <td>64.0685</td>
      <td>152.2782</td>
      <td>720316.0</td>
      <td>267.0</td>
      <td>1349.0</td>
      <td>187.28</td>
      <td>37.07</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>AZ</td>
      <td>34.2744</td>
      <td>-111.6602</td>
      <td>6479703.0</td>
      <td>1094.0</td>
      <td>2328.0</td>
      <td>35.93</td>
      <td>16.88</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>AR</td>
      <td>34.8938</td>
      <td>-92.4426</td>
      <td>2933369.0</td>
      <td>773.0</td>
      <td>2842.0</td>
      <td>96.89</td>
      <td>26.35</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>CA</td>
      <td>37.1841</td>
      <td>-119.4696</td>
      <td>37659181.0</td>
      <td>5562.0</td>
      <td>16306.0</td>
      <td>43.30</td>
      <td>14.77</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Colorado</td>
      <td>CO</td>
      <td>38.9972</td>
      <td>-105.5478</td>
      <td>5119329.0</td>
      <td>796.0</td>
      <td>3201.0</td>
      <td>62.53</td>
      <td>15.55</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Connecticut</td>
      <td>CT</td>
      <td>41.6219</td>
      <td>-72.7273</td>
      <td>3583561.0</td>
      <td>341.0</td>
      <td>3067.0</td>
      <td>85.59</td>
      <td>9.52</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Delaware</td>
      <td>DE</td>
      <td>38.9896</td>
      <td>-75.5050</td>
      <td>908446.0</td>
      <td>217.0</td>
      <td>1685.0</td>
      <td>185.48</td>
      <td>23.89</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Florida</td>
      <td>FL</td>
      <td>28.6305</td>
      <td>-82.4497</td>
      <td>19091156.0</td>
      <td>3909.0</td>
      <td>15029.0</td>
      <td>78.72</td>
      <td>20.48</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Georgia</td>
      <td>GA</td>
      <td>32.6415</td>
      <td>-83.4426</td>
      <td>9810417.0</td>
      <td>2456.0</td>
      <td>8925.0</td>
      <td>90.97</td>
      <td>25.03</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Hawaii</td>
      <td>HI</td>
      <td>20.2927</td>
      <td>-156.3737</td>
      <td>1376298.0</td>
      <td>63.0</td>
      <td>289.0</td>
      <td>21.00</td>
      <td>4.58</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Idaho</td>
      <td>ID</td>
      <td>44.3509</td>
      <td>-114.6130</td>
      <td>1583364.0</td>
      <td>166.0</td>
      <td>661.0</td>
      <td>41.75</td>
      <td>10.48</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Illinois</td>
      <td>IL</td>
      <td>40.0417</td>
      <td>-89.1965</td>
      <td>12848554.0</td>
      <td>3409.0</td>
      <td>17556.0</td>
      <td>136.64</td>
      <td>26.53</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Indiana</td>
      <td>IN</td>
      <td>39.8942</td>
      <td>-86.2816</td>
      <td>6514861.0</td>
      <td>1608.0</td>
      <td>5852.0</td>
      <td>89.83</td>
      <td>24.68</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Iowa</td>
      <td>IA</td>
      <td>42.0751</td>
      <td>-93.4960</td>
      <td>3062553.0</td>
      <td>273.0</td>
      <td>2517.0</td>
      <td>82.19</td>
      <td>8.91</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Kansas</td>
      <td>KS</td>
      <td>38.4937</td>
      <td>-98.3804</td>
      <td>2868107.0</td>
      <td>542.0</td>
      <td>2145.0</td>
      <td>74.79</td>
      <td>18.90</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Kentucky</td>
      <td>KY</td>
      <td>37.5347</td>
      <td>-85.3021</td>
      <td>4361333.0</td>
      <td>1066.0</td>
      <td>4157.0</td>
      <td>95.31</td>
      <td>24.44</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Louisiana</td>
      <td>LA</td>
      <td>31.0689</td>
      <td>-91.9968</td>
      <td>4567968.0</td>
      <td>2179.0</td>
      <td>8103.0</td>
      <td>177.39</td>
      <td>47.70</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Maine</td>
      <td>ME</td>
      <td>45.3695</td>
      <td>-69.2428</td>
      <td>1328320.0</td>
      <td>112.0</td>
      <td>907.0</td>
      <td>68.28</td>
      <td>8.43</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Maryland</td>
      <td>MD</td>
      <td>39.0550</td>
      <td>-76.7909</td>
      <td>5834299.0</td>
      <td>1691.0</td>
      <td>5798.0</td>
      <td>99.38</td>
      <td>28.98</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Massachusetts</td>
      <td>MA</td>
      <td>42.2596</td>
      <td>-71.8083</td>
      <td>6605058.0</td>
      <td>472.0</td>
      <td>5981.0</td>
      <td>90.55</td>
      <td>7.15</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Michigan</td>
      <td>MI</td>
      <td>44.3467</td>
      <td>-85.4102</td>
      <td>9886095.0</td>
      <td>1588.0</td>
      <td>6136.0</td>
      <td>62.07</td>
      <td>16.06</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Minnesota</td>
      <td>MN</td>
      <td>46.2807</td>
      <td>-94.3053</td>
      <td>5347740.0</td>
      <td>461.0</td>
      <td>2408.0</td>
      <td>45.03</td>
      <td>8.62</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Mississippi</td>
      <td>MS</td>
      <td>32.7364</td>
      <td>-89.6678</td>
      <td>2976872.0</td>
      <td>1176.0</td>
      <td>3599.0</td>
      <td>120.90</td>
      <td>39.50</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Missouri</td>
      <td>MO</td>
      <td>38.3566</td>
      <td>-92.4580</td>
      <td>6007182.0</td>
      <td>2136.0</td>
      <td>6631.0</td>
      <td>110.38</td>
      <td>35.56</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Montana</td>
      <td>MT</td>
      <td>47.0527</td>
      <td>-109.6333</td>
      <td>998554.0</td>
      <td>154.0</td>
      <td>638.0</td>
      <td>63.89</td>
      <td>15.42</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Nebraska</td>
      <td>NE</td>
      <td>41.5378</td>
      <td>-99.7951</td>
      <td>1841625.0</td>
      <td>225.0</td>
      <td>1651.0</td>
      <td>89.65</td>
      <td>12.22</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Nevada</td>
      <td>NV</td>
      <td>39.3289</td>
      <td>-116.6312</td>
      <td>2730066.0</td>
      <td>799.0</td>
      <td>1952.0</td>
      <td>71.50</td>
      <td>29.27</td>
    </tr>
    <tr>
      <th>28</th>
      <td>New Hampshire</td>
      <td>NH</td>
      <td>43.6805</td>
      <td>-71.5811</td>
      <td>1319171.0</td>
      <td>88.0</td>
      <td>964.0</td>
      <td>73.08</td>
      <td>6.67</td>
    </tr>
    <tr>
      <th>29</th>
      <td>New Jersey</td>
      <td>NJ</td>
      <td>40.1907</td>
      <td>-74.6728</td>
      <td>8832406.0</td>
      <td>1168.0</td>
      <td>5387.0</td>
      <td>60.99</td>
      <td>13.22</td>
    </tr>
    <tr>
      <th>30</th>
      <td>New Mexico</td>
      <td>NM</td>
      <td>34.4071</td>
      <td>-106.1126</td>
      <td>2069706.0</td>
      <td>495.0</td>
      <td>1645.0</td>
      <td>79.48</td>
      <td>23.92</td>
    </tr>
    <tr>
      <th>31</th>
      <td>New York</td>
      <td>NY</td>
      <td>42.9538</td>
      <td>-75.5268</td>
      <td>19487053.0</td>
      <td>1705.0</td>
      <td>9712.0</td>
      <td>49.84</td>
      <td>8.75</td>
    </tr>
    <tr>
      <th>32</th>
      <td>North Carolina</td>
      <td>NC</td>
      <td>35.5557</td>
      <td>-79.3877</td>
      <td>9651380.0</td>
      <td>2225.0</td>
      <td>8739.0</td>
      <td>90.55</td>
      <td>23.05</td>
    </tr>
    <tr>
      <th>33</th>
      <td>North Dakota</td>
      <td>ND</td>
      <td>47.4501</td>
      <td>-100.4659</td>
      <td>689781.0</td>
      <td>69.0</td>
      <td>573.0</td>
      <td>83.07</td>
      <td>10.00</td>
    </tr>
    <tr>
      <th>34</th>
      <td>Ohio</td>
      <td>OH</td>
      <td>40.2862</td>
      <td>-82.7937</td>
      <td>11549590.0</td>
      <td>2508.0</td>
      <td>10244.0</td>
      <td>88.70</td>
      <td>21.72</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Oklahoma</td>
      <td>OK</td>
      <td>35.5889</td>
      <td>-97.4943</td>
      <td>3785742.0</td>
      <td>941.0</td>
      <td>3455.0</td>
      <td>91.26</td>
      <td>24.86</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Oregon</td>
      <td>OR</td>
      <td>43.9336</td>
      <td>-120.5583</td>
      <td>3868721.0</td>
      <td>446.0</td>
      <td>2286.0</td>
      <td>59.09</td>
      <td>11.53</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Pennsylvania</td>
      <td>PA</td>
      <td>40.8781</td>
      <td>-77.7996</td>
      <td>12731381.0</td>
      <td>2395.0</td>
      <td>8929.0</td>
      <td>70.13</td>
      <td>18.81</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Rhode Island</td>
      <td>RI</td>
      <td>41.6762</td>
      <td>-71.5562</td>
      <td>1051695.0</td>
      <td>63.0</td>
      <td>895.0</td>
      <td>85.10</td>
      <td>5.99</td>
    </tr>
    <tr>
      <th>39</th>
      <td>South Carolina</td>
      <td>SC</td>
      <td>33.9169</td>
      <td>-80.8964</td>
      <td>4679602.0</td>
      <td>1610.0</td>
      <td>6939.0</td>
      <td>148.28</td>
      <td>34.40</td>
    </tr>
    <tr>
      <th>40</th>
      <td>South Dakota</td>
      <td>SD</td>
      <td>44.4443</td>
      <td>-100.2263</td>
      <td>825198.0</td>
      <td>90.0</td>
      <td>544.0</td>
      <td>65.92</td>
      <td>10.91</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Tennessee</td>
      <td>TN</td>
      <td>35.8580</td>
      <td>-86.3505</td>
      <td>6402387.0</td>
      <td>1824.0</td>
      <td>7626.0</td>
      <td>119.11</td>
      <td>28.49</td>
    </tr>
    <tr>
      <th>42</th>
      <td>Texas</td>
      <td>TX</td>
      <td>31.4757</td>
      <td>-99.3312</td>
      <td>25639373.0</td>
      <td>5046.0</td>
      <td>13577.0</td>
      <td>52.95</td>
      <td>19.68</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Utah</td>
      <td>UT</td>
      <td>39.3055</td>
      <td>-111.6703</td>
      <td>2813673.0</td>
      <td>282.0</td>
      <td>1072.0</td>
      <td>38.10</td>
      <td>10.02</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Vermont</td>
      <td>VT</td>
      <td>44.0687</td>
      <td>-72.6658</td>
      <td>625904.0</td>
      <td>57.0</td>
      <td>472.0</td>
      <td>75.41</td>
      <td>9.11</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Virginia</td>
      <td>VA</td>
      <td>37.5215</td>
      <td>-78.8537</td>
      <td>8100653.0</td>
      <td>1459.0</td>
      <td>5949.0</td>
      <td>73.44</td>
      <td>18.01</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Washington</td>
      <td>WA</td>
      <td>47.3826</td>
      <td>-120.4472</td>
      <td>6819579.0</td>
      <td>853.0</td>
      <td>3434.0</td>
      <td>50.36</td>
      <td>12.51</td>
    </tr>
    <tr>
      <th>47</th>
      <td>West Virginia</td>
      <td>WV</td>
      <td>38.6409</td>
      <td>-80.6227</td>
      <td>1853619.0</td>
      <td>335.0</td>
      <td>1575.0</td>
      <td>84.97</td>
      <td>18.07</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Wisconsin</td>
      <td>WI</td>
      <td>44.6243</td>
      <td>-89.9941</td>
      <td>5706871.0</td>
      <td>862.0</td>
      <td>4787.0</td>
      <td>83.88</td>
      <td>15.10</td>
    </tr>
    <tr>
      <th>49</th>
      <td>Wyoming</td>
      <td>WY</td>
      <td>42.9957</td>
      <td>-107.5512</td>
      <td>570134.0</td>
      <td>73.0</td>
      <td>494.0</td>
      <td>86.65</td>
      <td>12.80</td>
    </tr>
  </tbody>
</table>
</div>




```python
sorted_df = plotly_gun_df.sort_values("Number of Incidents", ascending=False)
sorted_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>code</th>
      <th>lat (N)</th>
      <th>lng (W)</th>
      <th>Population</th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
      <th>Norm Incidents</th>
      <th>Norm Deaths</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12</th>
      <td>Illinois</td>
      <td>IL</td>
      <td>40.0417</td>
      <td>-89.1965</td>
      <td>12848554.0</td>
      <td>3409.0</td>
      <td>17556.0</td>
      <td>136.64</td>
      <td>26.53</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>CA</td>
      <td>37.1841</td>
      <td>-119.4696</td>
      <td>37659181.0</td>
      <td>5562.0</td>
      <td>16306.0</td>
      <td>43.30</td>
      <td>14.77</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Florida</td>
      <td>FL</td>
      <td>28.6305</td>
      <td>-82.4497</td>
      <td>19091156.0</td>
      <td>3909.0</td>
      <td>15029.0</td>
      <td>78.72</td>
      <td>20.48</td>
    </tr>
    <tr>
      <th>42</th>
      <td>Texas</td>
      <td>TX</td>
      <td>31.4757</td>
      <td>-99.3312</td>
      <td>25639373.0</td>
      <td>5046.0</td>
      <td>13577.0</td>
      <td>52.95</td>
      <td>19.68</td>
    </tr>
    <tr>
      <th>34</th>
      <td>Ohio</td>
      <td>OH</td>
      <td>40.2862</td>
      <td>-82.7937</td>
      <td>11549590.0</td>
      <td>2508.0</td>
      <td>10244.0</td>
      <td>88.70</td>
      <td>21.72</td>
    </tr>
    <tr>
      <th>31</th>
      <td>New York</td>
      <td>NY</td>
      <td>42.9538</td>
      <td>-75.5268</td>
      <td>19487053.0</td>
      <td>1705.0</td>
      <td>9712.0</td>
      <td>49.84</td>
      <td>8.75</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Pennsylvania</td>
      <td>PA</td>
      <td>40.8781</td>
      <td>-77.7996</td>
      <td>12731381.0</td>
      <td>2395.0</td>
      <td>8929.0</td>
      <td>70.13</td>
      <td>18.81</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Georgia</td>
      <td>GA</td>
      <td>32.6415</td>
      <td>-83.4426</td>
      <td>9810417.0</td>
      <td>2456.0</td>
      <td>8925.0</td>
      <td>90.97</td>
      <td>25.03</td>
    </tr>
    <tr>
      <th>32</th>
      <td>North Carolina</td>
      <td>NC</td>
      <td>35.5557</td>
      <td>-79.3877</td>
      <td>9651380.0</td>
      <td>2225.0</td>
      <td>8739.0</td>
      <td>90.55</td>
      <td>23.05</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Louisiana</td>
      <td>LA</td>
      <td>31.0689</td>
      <td>-91.9968</td>
      <td>4567968.0</td>
      <td>2179.0</td>
      <td>8103.0</td>
      <td>177.39</td>
      <td>47.70</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Tennessee</td>
      <td>TN</td>
      <td>35.8580</td>
      <td>-86.3505</td>
      <td>6402387.0</td>
      <td>1824.0</td>
      <td>7626.0</td>
      <td>119.11</td>
      <td>28.49</td>
    </tr>
    <tr>
      <th>39</th>
      <td>South Carolina</td>
      <td>SC</td>
      <td>33.9169</td>
      <td>-80.8964</td>
      <td>4679602.0</td>
      <td>1610.0</td>
      <td>6939.0</td>
      <td>148.28</td>
      <td>34.40</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Missouri</td>
      <td>MO</td>
      <td>38.3566</td>
      <td>-92.4580</td>
      <td>6007182.0</td>
      <td>2136.0</td>
      <td>6631.0</td>
      <td>110.38</td>
      <td>35.56</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Michigan</td>
      <td>MI</td>
      <td>44.3467</td>
      <td>-85.4102</td>
      <td>9886095.0</td>
      <td>1588.0</td>
      <td>6136.0</td>
      <td>62.07</td>
      <td>16.06</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Massachusetts</td>
      <td>MA</td>
      <td>42.2596</td>
      <td>-71.8083</td>
      <td>6605058.0</td>
      <td>472.0</td>
      <td>5981.0</td>
      <td>90.55</td>
      <td>7.15</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Virginia</td>
      <td>VA</td>
      <td>37.5215</td>
      <td>-78.8537</td>
      <td>8100653.0</td>
      <td>1459.0</td>
      <td>5949.0</td>
      <td>73.44</td>
      <td>18.01</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Indiana</td>
      <td>IN</td>
      <td>39.8942</td>
      <td>-86.2816</td>
      <td>6514861.0</td>
      <td>1608.0</td>
      <td>5852.0</td>
      <td>89.83</td>
      <td>24.68</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Maryland</td>
      <td>MD</td>
      <td>39.0550</td>
      <td>-76.7909</td>
      <td>5834299.0</td>
      <td>1691.0</td>
      <td>5798.0</td>
      <td>99.38</td>
      <td>28.98</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>AL</td>
      <td>32.7794</td>
      <td>-86.8287</td>
      <td>4799277.0</td>
      <td>1880.0</td>
      <td>5471.0</td>
      <td>114.00</td>
      <td>39.17</td>
    </tr>
    <tr>
      <th>29</th>
      <td>New Jersey</td>
      <td>NJ</td>
      <td>40.1907</td>
      <td>-74.6728</td>
      <td>8832406.0</td>
      <td>1168.0</td>
      <td>5387.0</td>
      <td>60.99</td>
      <td>13.22</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Wisconsin</td>
      <td>WI</td>
      <td>44.6243</td>
      <td>-89.9941</td>
      <td>5706871.0</td>
      <td>862.0</td>
      <td>4787.0</td>
      <td>83.88</td>
      <td>15.10</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Kentucky</td>
      <td>KY</td>
      <td>37.5347</td>
      <td>-85.3021</td>
      <td>4361333.0</td>
      <td>1066.0</td>
      <td>4157.0</td>
      <td>95.31</td>
      <td>24.44</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Mississippi</td>
      <td>MS</td>
      <td>32.7364</td>
      <td>-89.6678</td>
      <td>2976872.0</td>
      <td>1176.0</td>
      <td>3599.0</td>
      <td>120.90</td>
      <td>39.50</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Oklahoma</td>
      <td>OK</td>
      <td>35.5889</td>
      <td>-97.4943</td>
      <td>3785742.0</td>
      <td>941.0</td>
      <td>3455.0</td>
      <td>91.26</td>
      <td>24.86</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Washington</td>
      <td>WA</td>
      <td>47.3826</td>
      <td>-120.4472</td>
      <td>6819579.0</td>
      <td>853.0</td>
      <td>3434.0</td>
      <td>50.36</td>
      <td>12.51</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Colorado</td>
      <td>CO</td>
      <td>38.9972</td>
      <td>-105.5478</td>
      <td>5119329.0</td>
      <td>796.0</td>
      <td>3201.0</td>
      <td>62.53</td>
      <td>15.55</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Connecticut</td>
      <td>CT</td>
      <td>41.6219</td>
      <td>-72.7273</td>
      <td>3583561.0</td>
      <td>341.0</td>
      <td>3067.0</td>
      <td>85.59</td>
      <td>9.52</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>AR</td>
      <td>34.8938</td>
      <td>-92.4426</td>
      <td>2933369.0</td>
      <td>773.0</td>
      <td>2842.0</td>
      <td>96.89</td>
      <td>26.35</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Iowa</td>
      <td>IA</td>
      <td>42.0751</td>
      <td>-93.4960</td>
      <td>3062553.0</td>
      <td>273.0</td>
      <td>2517.0</td>
      <td>82.19</td>
      <td>8.91</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Minnesota</td>
      <td>MN</td>
      <td>46.2807</td>
      <td>-94.3053</td>
      <td>5347740.0</td>
      <td>461.0</td>
      <td>2408.0</td>
      <td>45.03</td>
      <td>8.62</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>AZ</td>
      <td>34.2744</td>
      <td>-111.6602</td>
      <td>6479703.0</td>
      <td>1094.0</td>
      <td>2328.0</td>
      <td>35.93</td>
      <td>16.88</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Oregon</td>
      <td>OR</td>
      <td>43.9336</td>
      <td>-120.5583</td>
      <td>3868721.0</td>
      <td>446.0</td>
      <td>2286.0</td>
      <td>59.09</td>
      <td>11.53</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Kansas</td>
      <td>KS</td>
      <td>38.4937</td>
      <td>-98.3804</td>
      <td>2868107.0</td>
      <td>542.0</td>
      <td>2145.0</td>
      <td>74.79</td>
      <td>18.90</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Nevada</td>
      <td>NV</td>
      <td>39.3289</td>
      <td>-116.6312</td>
      <td>2730066.0</td>
      <td>799.0</td>
      <td>1952.0</td>
      <td>71.50</td>
      <td>29.27</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Delaware</td>
      <td>DE</td>
      <td>38.9896</td>
      <td>-75.5050</td>
      <td>908446.0</td>
      <td>217.0</td>
      <td>1685.0</td>
      <td>185.48</td>
      <td>23.89</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Nebraska</td>
      <td>NE</td>
      <td>41.5378</td>
      <td>-99.7951</td>
      <td>1841625.0</td>
      <td>225.0</td>
      <td>1651.0</td>
      <td>89.65</td>
      <td>12.22</td>
    </tr>
    <tr>
      <th>30</th>
      <td>New Mexico</td>
      <td>NM</td>
      <td>34.4071</td>
      <td>-106.1126</td>
      <td>2069706.0</td>
      <td>495.0</td>
      <td>1645.0</td>
      <td>79.48</td>
      <td>23.92</td>
    </tr>
    <tr>
      <th>47</th>
      <td>West Virginia</td>
      <td>WV</td>
      <td>38.6409</td>
      <td>-80.6227</td>
      <td>1853619.0</td>
      <td>335.0</td>
      <td>1575.0</td>
      <td>84.97</td>
      <td>18.07</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>AK</td>
      <td>64.0685</td>
      <td>152.2782</td>
      <td>720316.0</td>
      <td>267.0</td>
      <td>1349.0</td>
      <td>187.28</td>
      <td>37.07</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Utah</td>
      <td>UT</td>
      <td>39.3055</td>
      <td>-111.6703</td>
      <td>2813673.0</td>
      <td>282.0</td>
      <td>1072.0</td>
      <td>38.10</td>
      <td>10.02</td>
    </tr>
    <tr>
      <th>28</th>
      <td>New Hampshire</td>
      <td>NH</td>
      <td>43.6805</td>
      <td>-71.5811</td>
      <td>1319171.0</td>
      <td>88.0</td>
      <td>964.0</td>
      <td>73.08</td>
      <td>6.67</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Maine</td>
      <td>ME</td>
      <td>45.3695</td>
      <td>-69.2428</td>
      <td>1328320.0</td>
      <td>112.0</td>
      <td>907.0</td>
      <td>68.28</td>
      <td>8.43</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Rhode Island</td>
      <td>RI</td>
      <td>41.6762</td>
      <td>-71.5562</td>
      <td>1051695.0</td>
      <td>63.0</td>
      <td>895.0</td>
      <td>85.10</td>
      <td>5.99</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Idaho</td>
      <td>ID</td>
      <td>44.3509</td>
      <td>-114.6130</td>
      <td>1583364.0</td>
      <td>166.0</td>
      <td>661.0</td>
      <td>41.75</td>
      <td>10.48</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Montana</td>
      <td>MT</td>
      <td>47.0527</td>
      <td>-109.6333</td>
      <td>998554.0</td>
      <td>154.0</td>
      <td>638.0</td>
      <td>63.89</td>
      <td>15.42</td>
    </tr>
    <tr>
      <th>33</th>
      <td>North Dakota</td>
      <td>ND</td>
      <td>47.4501</td>
      <td>-100.4659</td>
      <td>689781.0</td>
      <td>69.0</td>
      <td>573.0</td>
      <td>83.07</td>
      <td>10.00</td>
    </tr>
    <tr>
      <th>40</th>
      <td>South Dakota</td>
      <td>SD</td>
      <td>44.4443</td>
      <td>-100.2263</td>
      <td>825198.0</td>
      <td>90.0</td>
      <td>544.0</td>
      <td>65.92</td>
      <td>10.91</td>
    </tr>
    <tr>
      <th>49</th>
      <td>Wyoming</td>
      <td>WY</td>
      <td>42.9957</td>
      <td>-107.5512</td>
      <td>570134.0</td>
      <td>73.0</td>
      <td>494.0</td>
      <td>86.65</td>
      <td>12.80</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Vermont</td>
      <td>VT</td>
      <td>44.0687</td>
      <td>-72.6658</td>
      <td>625904.0</td>
      <td>57.0</td>
      <td>472.0</td>
      <td>75.41</td>
      <td>9.11</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Hawaii</td>
      <td>HI</td>
      <td>20.2927</td>
      <td>-156.3737</td>
      <td>1376298.0</td>
      <td>63.0</td>
      <td>289.0</td>
      <td>21.00</td>
      <td>4.58</td>
    </tr>
  </tbody>
</table>
</div>




```python
sorted_norm_df = plotly_gun_df.sort_values("Norm Incidents", ascending=False)
sorted_norm_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>code</th>
      <th>lat (N)</th>
      <th>lng (W)</th>
      <th>Population</th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
      <th>Norm Incidents</th>
      <th>Norm Deaths</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>AK</td>
      <td>64.0685</td>
      <td>152.2782</td>
      <td>720316.0</td>
      <td>267.0</td>
      <td>1349.0</td>
      <td>187.28</td>
      <td>37.07</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Delaware</td>
      <td>DE</td>
      <td>38.9896</td>
      <td>-75.5050</td>
      <td>908446.0</td>
      <td>217.0</td>
      <td>1685.0</td>
      <td>185.48</td>
      <td>23.89</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Louisiana</td>
      <td>LA</td>
      <td>31.0689</td>
      <td>-91.9968</td>
      <td>4567968.0</td>
      <td>2179.0</td>
      <td>8103.0</td>
      <td>177.39</td>
      <td>47.70</td>
    </tr>
    <tr>
      <th>39</th>
      <td>South Carolina</td>
      <td>SC</td>
      <td>33.9169</td>
      <td>-80.8964</td>
      <td>4679602.0</td>
      <td>1610.0</td>
      <td>6939.0</td>
      <td>148.28</td>
      <td>34.40</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Illinois</td>
      <td>IL</td>
      <td>40.0417</td>
      <td>-89.1965</td>
      <td>12848554.0</td>
      <td>3409.0</td>
      <td>17556.0</td>
      <td>136.64</td>
      <td>26.53</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Mississippi</td>
      <td>MS</td>
      <td>32.7364</td>
      <td>-89.6678</td>
      <td>2976872.0</td>
      <td>1176.0</td>
      <td>3599.0</td>
      <td>120.90</td>
      <td>39.50</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Tennessee</td>
      <td>TN</td>
      <td>35.8580</td>
      <td>-86.3505</td>
      <td>6402387.0</td>
      <td>1824.0</td>
      <td>7626.0</td>
      <td>119.11</td>
      <td>28.49</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>AL</td>
      <td>32.7794</td>
      <td>-86.8287</td>
      <td>4799277.0</td>
      <td>1880.0</td>
      <td>5471.0</td>
      <td>114.00</td>
      <td>39.17</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Missouri</td>
      <td>MO</td>
      <td>38.3566</td>
      <td>-92.4580</td>
      <td>6007182.0</td>
      <td>2136.0</td>
      <td>6631.0</td>
      <td>110.38</td>
      <td>35.56</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Maryland</td>
      <td>MD</td>
      <td>39.0550</td>
      <td>-76.7909</td>
      <td>5834299.0</td>
      <td>1691.0</td>
      <td>5798.0</td>
      <td>99.38</td>
      <td>28.98</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>AR</td>
      <td>34.8938</td>
      <td>-92.4426</td>
      <td>2933369.0</td>
      <td>773.0</td>
      <td>2842.0</td>
      <td>96.89</td>
      <td>26.35</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Kentucky</td>
      <td>KY</td>
      <td>37.5347</td>
      <td>-85.3021</td>
      <td>4361333.0</td>
      <td>1066.0</td>
      <td>4157.0</td>
      <td>95.31</td>
      <td>24.44</td>
    </tr>
    <tr>
      <th>35</th>
      <td>Oklahoma</td>
      <td>OK</td>
      <td>35.5889</td>
      <td>-97.4943</td>
      <td>3785742.0</td>
      <td>941.0</td>
      <td>3455.0</td>
      <td>91.26</td>
      <td>24.86</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Georgia</td>
      <td>GA</td>
      <td>32.6415</td>
      <td>-83.4426</td>
      <td>9810417.0</td>
      <td>2456.0</td>
      <td>8925.0</td>
      <td>90.97</td>
      <td>25.03</td>
    </tr>
    <tr>
      <th>32</th>
      <td>North Carolina</td>
      <td>NC</td>
      <td>35.5557</td>
      <td>-79.3877</td>
      <td>9651380.0</td>
      <td>2225.0</td>
      <td>8739.0</td>
      <td>90.55</td>
      <td>23.05</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Massachusetts</td>
      <td>MA</td>
      <td>42.2596</td>
      <td>-71.8083</td>
      <td>6605058.0</td>
      <td>472.0</td>
      <td>5981.0</td>
      <td>90.55</td>
      <td>7.15</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Indiana</td>
      <td>IN</td>
      <td>39.8942</td>
      <td>-86.2816</td>
      <td>6514861.0</td>
      <td>1608.0</td>
      <td>5852.0</td>
      <td>89.83</td>
      <td>24.68</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Nebraska</td>
      <td>NE</td>
      <td>41.5378</td>
      <td>-99.7951</td>
      <td>1841625.0</td>
      <td>225.0</td>
      <td>1651.0</td>
      <td>89.65</td>
      <td>12.22</td>
    </tr>
    <tr>
      <th>34</th>
      <td>Ohio</td>
      <td>OH</td>
      <td>40.2862</td>
      <td>-82.7937</td>
      <td>11549590.0</td>
      <td>2508.0</td>
      <td>10244.0</td>
      <td>88.70</td>
      <td>21.72</td>
    </tr>
    <tr>
      <th>49</th>
      <td>Wyoming</td>
      <td>WY</td>
      <td>42.9957</td>
      <td>-107.5512</td>
      <td>570134.0</td>
      <td>73.0</td>
      <td>494.0</td>
      <td>86.65</td>
      <td>12.80</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Connecticut</td>
      <td>CT</td>
      <td>41.6219</td>
      <td>-72.7273</td>
      <td>3583561.0</td>
      <td>341.0</td>
      <td>3067.0</td>
      <td>85.59</td>
      <td>9.52</td>
    </tr>
    <tr>
      <th>38</th>
      <td>Rhode Island</td>
      <td>RI</td>
      <td>41.6762</td>
      <td>-71.5562</td>
      <td>1051695.0</td>
      <td>63.0</td>
      <td>895.0</td>
      <td>85.10</td>
      <td>5.99</td>
    </tr>
    <tr>
      <th>47</th>
      <td>West Virginia</td>
      <td>WV</td>
      <td>38.6409</td>
      <td>-80.6227</td>
      <td>1853619.0</td>
      <td>335.0</td>
      <td>1575.0</td>
      <td>84.97</td>
      <td>18.07</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Wisconsin</td>
      <td>WI</td>
      <td>44.6243</td>
      <td>-89.9941</td>
      <td>5706871.0</td>
      <td>862.0</td>
      <td>4787.0</td>
      <td>83.88</td>
      <td>15.10</td>
    </tr>
    <tr>
      <th>33</th>
      <td>North Dakota</td>
      <td>ND</td>
      <td>47.4501</td>
      <td>-100.4659</td>
      <td>689781.0</td>
      <td>69.0</td>
      <td>573.0</td>
      <td>83.07</td>
      <td>10.00</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Iowa</td>
      <td>IA</td>
      <td>42.0751</td>
      <td>-93.4960</td>
      <td>3062553.0</td>
      <td>273.0</td>
      <td>2517.0</td>
      <td>82.19</td>
      <td>8.91</td>
    </tr>
    <tr>
      <th>30</th>
      <td>New Mexico</td>
      <td>NM</td>
      <td>34.4071</td>
      <td>-106.1126</td>
      <td>2069706.0</td>
      <td>495.0</td>
      <td>1645.0</td>
      <td>79.48</td>
      <td>23.92</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Florida</td>
      <td>FL</td>
      <td>28.6305</td>
      <td>-82.4497</td>
      <td>19091156.0</td>
      <td>3909.0</td>
      <td>15029.0</td>
      <td>78.72</td>
      <td>20.48</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Vermont</td>
      <td>VT</td>
      <td>44.0687</td>
      <td>-72.6658</td>
      <td>625904.0</td>
      <td>57.0</td>
      <td>472.0</td>
      <td>75.41</td>
      <td>9.11</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Kansas</td>
      <td>KS</td>
      <td>38.4937</td>
      <td>-98.3804</td>
      <td>2868107.0</td>
      <td>542.0</td>
      <td>2145.0</td>
      <td>74.79</td>
      <td>18.90</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Virginia</td>
      <td>VA</td>
      <td>37.5215</td>
      <td>-78.8537</td>
      <td>8100653.0</td>
      <td>1459.0</td>
      <td>5949.0</td>
      <td>73.44</td>
      <td>18.01</td>
    </tr>
    <tr>
      <th>28</th>
      <td>New Hampshire</td>
      <td>NH</td>
      <td>43.6805</td>
      <td>-71.5811</td>
      <td>1319171.0</td>
      <td>88.0</td>
      <td>964.0</td>
      <td>73.08</td>
      <td>6.67</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Nevada</td>
      <td>NV</td>
      <td>39.3289</td>
      <td>-116.6312</td>
      <td>2730066.0</td>
      <td>799.0</td>
      <td>1952.0</td>
      <td>71.50</td>
      <td>29.27</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Pennsylvania</td>
      <td>PA</td>
      <td>40.8781</td>
      <td>-77.7996</td>
      <td>12731381.0</td>
      <td>2395.0</td>
      <td>8929.0</td>
      <td>70.13</td>
      <td>18.81</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Maine</td>
      <td>ME</td>
      <td>45.3695</td>
      <td>-69.2428</td>
      <td>1328320.0</td>
      <td>112.0</td>
      <td>907.0</td>
      <td>68.28</td>
      <td>8.43</td>
    </tr>
    <tr>
      <th>40</th>
      <td>South Dakota</td>
      <td>SD</td>
      <td>44.4443</td>
      <td>-100.2263</td>
      <td>825198.0</td>
      <td>90.0</td>
      <td>544.0</td>
      <td>65.92</td>
      <td>10.91</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Montana</td>
      <td>MT</td>
      <td>47.0527</td>
      <td>-109.6333</td>
      <td>998554.0</td>
      <td>154.0</td>
      <td>638.0</td>
      <td>63.89</td>
      <td>15.42</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Colorado</td>
      <td>CO</td>
      <td>38.9972</td>
      <td>-105.5478</td>
      <td>5119329.0</td>
      <td>796.0</td>
      <td>3201.0</td>
      <td>62.53</td>
      <td>15.55</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Michigan</td>
      <td>MI</td>
      <td>44.3467</td>
      <td>-85.4102</td>
      <td>9886095.0</td>
      <td>1588.0</td>
      <td>6136.0</td>
      <td>62.07</td>
      <td>16.06</td>
    </tr>
    <tr>
      <th>29</th>
      <td>New Jersey</td>
      <td>NJ</td>
      <td>40.1907</td>
      <td>-74.6728</td>
      <td>8832406.0</td>
      <td>1168.0</td>
      <td>5387.0</td>
      <td>60.99</td>
      <td>13.22</td>
    </tr>
    <tr>
      <th>36</th>
      <td>Oregon</td>
      <td>OR</td>
      <td>43.9336</td>
      <td>-120.5583</td>
      <td>3868721.0</td>
      <td>446.0</td>
      <td>2286.0</td>
      <td>59.09</td>
      <td>11.53</td>
    </tr>
    <tr>
      <th>42</th>
      <td>Texas</td>
      <td>TX</td>
      <td>31.4757</td>
      <td>-99.3312</td>
      <td>25639373.0</td>
      <td>5046.0</td>
      <td>13577.0</td>
      <td>52.95</td>
      <td>19.68</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Washington</td>
      <td>WA</td>
      <td>47.3826</td>
      <td>-120.4472</td>
      <td>6819579.0</td>
      <td>853.0</td>
      <td>3434.0</td>
      <td>50.36</td>
      <td>12.51</td>
    </tr>
    <tr>
      <th>31</th>
      <td>New York</td>
      <td>NY</td>
      <td>42.9538</td>
      <td>-75.5268</td>
      <td>19487053.0</td>
      <td>1705.0</td>
      <td>9712.0</td>
      <td>49.84</td>
      <td>8.75</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Minnesota</td>
      <td>MN</td>
      <td>46.2807</td>
      <td>-94.3053</td>
      <td>5347740.0</td>
      <td>461.0</td>
      <td>2408.0</td>
      <td>45.03</td>
      <td>8.62</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>CA</td>
      <td>37.1841</td>
      <td>-119.4696</td>
      <td>37659181.0</td>
      <td>5562.0</td>
      <td>16306.0</td>
      <td>43.30</td>
      <td>14.77</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Idaho</td>
      <td>ID</td>
      <td>44.3509</td>
      <td>-114.6130</td>
      <td>1583364.0</td>
      <td>166.0</td>
      <td>661.0</td>
      <td>41.75</td>
      <td>10.48</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Utah</td>
      <td>UT</td>
      <td>39.3055</td>
      <td>-111.6703</td>
      <td>2813673.0</td>
      <td>282.0</td>
      <td>1072.0</td>
      <td>38.10</td>
      <td>10.02</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>AZ</td>
      <td>34.2744</td>
      <td>-111.6602</td>
      <td>6479703.0</td>
      <td>1094.0</td>
      <td>2328.0</td>
      <td>35.93</td>
      <td>16.88</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Hawaii</td>
      <td>HI</td>
      <td>20.2927</td>
      <td>-156.3737</td>
      <td>1376298.0</td>
      <td>63.0</td>
      <td>289.0</td>
      <td>21.00</td>
      <td>4.58</td>
    </tr>
  </tbody>
</table>
</div>




```python
plotly_gun_df.to_csv("Resources\plotly_gun.csv")
```


```python
random_x = np.array(plotly_gun_df["Population"])
random_y = plotly_gun_df["Number of Incidents"]

# Create a trace
trace = go.Scatter(
    x = random_x,
    y = random_y,
    mode = 'markers'
)

data = [trace]

# Plot and embed in ipython notebook!
py.iplot(data, filename='basic-scatter')
```

    Aw, snap! We don't have an account for ''. Want to try again? You can authenticate with your email address or username. Sign in is not case sensitive.
    
    Don't have an account? plot.ly
    
    Questions? accounts@plot.ly



    ---------------------------------------------------------------------------

    PlotlyError                               Traceback (most recent call last)

    <ipython-input-208-32046749386c> in <module>()
         12 
         13 # Plot and embed in ipython notebook!
    ---> 14 py.iplot(data, filename='basic-scatter')
    

    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/plotly/plotly/plotly.py in iplot(figure_or_data, **plot_options)
        162         embed_options['height'] = str(embed_options['height']) + 'px'
        163 
    --> 164     return tools.embed(url, **embed_options)
        165 
        166 


    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/plotly/tools.py in embed(file_owner_or_url, file_id, width, height)
        388         else:
        389             url = file_owner_or_url
    --> 390         return PlotlyDisplay(url, width, height)
        391     else:
        392         if (get_config_defaults()['plotly_domain']


    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/plotly/tools.py in __init__(self, url, width, height)
       1432         def __init__(self, url, width, height):
       1433             self.resource = url
    -> 1434             self.embed_code = get_embed(url, width=width, height=height)
       1435             super(PlotlyDisplay, self).__init__(data=self.embed_code)
       1436 


    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/plotly/tools.py in get_embed(file_owner_or_url, file_id, width, height)
        293                 "'{1}'."
        294                 "\nRun help on this function for more information."
    --> 295                 "".format(url, plotly_rest_url))
        296         urlsplit = six.moves.urllib.parse.urlparse(url)
        297         file_owner = urlsplit.path.split('/')[1].split('~')[1]


    PlotlyError: Because you didn't supply a 'file_id' in the call, we're assuming you're trying to snag a figure from a url. You supplied the url, '', we expected it to start with 'https://plot.ly'.
    Run help on this function for more information.



```python
#population vs gun incidents 

x_axis = np.array(plotly_gun_df["Population"])
y_axis = plotly_gun_df["Number of Incidents"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()

plt.title("Total Number of Incidents per Population")
ax.set_xlabel("Population")
ax.set_ylabel("Number of Incidents")

ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'blue')
```




    [<matplotlib.lines.Line2D at 0x111360ac8>]




![png](output_72_1.png)



```python

#population vs gun deaths #populati 

x_axis = np.array(plotly_gun_df["Population"])
y_axis = plotly_gun_df["Number of Deaths"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()

plt.title("Total Number of Deaths per Population")
ax.set_xlabel("Population")
ax.set_ylabel("Number of Deaths")

ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'blue')
```




    [<matplotlib.lines.Line2D at 0x1a16faad30>]




![png](output_73_1.png)



```python
gkey = "AIzaSyCx3T_aTGD4fLG_JJh631UKC3Mo5EetFTc"
```


```python
gmaps.configure(api_key=gkey) # Fill in with your API key
fig = gmaps.figure()
fig
```


<p>Failed to display Jupyter Widget of type <code>Figure</code>.</p>
<p>
  If you're reading this message in the Jupyter Notebook or JupyterLab Notebook, it may mean
  that the widgets JavaScript is still loading. If this message persists, it
  likely means that the widgets JavaScript library is either not installed or
  not enabled. See the <a href="https://ipywidgets.readthedocs.io/en/stable/user_install.html">Jupyter
  Widgets Documentation</a> for setup instructions.
</p>
<p>
  If you're reading this message in another frontend (for example, a static
  rendering on GitHub or <a href="https://nbviewer.jupyter.org/">NBViewer</a>),
  it may mean that your frontend doesn't currently support widgets.
</p>




```python
#build a heatmap layer showing the number of deaths per state:

fig = gmaps.figure(map_type='HYBRID', center=(37.7, -97.3),zoom_level=4)


heatmap_layer = gmaps.heatmap_layer(plotly_gun_df[["lat (N)", "lng (W)"]], weights=plotly_gun_df["Norm Deaths"], max_intensity=80, point_radius=20)
fig.add_layer(heatmap_layer)
fig
```


<p>Failed to display Jupyter Widget of type <code>Figure</code>.</p>
<p>
  If you're reading this message in the Jupyter Notebook or JupyterLab Notebook, it may mean
  that the widgets JavaScript is still loading. If this message persists, it
  likely means that the widgets JavaScript library is either not installed or
  not enabled. See the <a href="https://ipywidgets.readthedocs.io/en/stable/user_install.html">Jupyter
  Widgets Documentation</a> for setup instructions.
</p>
<p>
  If you're reading this message in another frontend (for example, a static
  rendering on GitHub or <a href="https://nbviewer.jupyter.org/">NBViewer</a>),
  it may mean that your frontend doesn't currently support widgets.
</p>




```python
#build a heatmap layer showing the number of deaths per state:

fig = gmaps.figure(center=(37.7, -97.3),zoom_level=4)


heatmap_layer = gmaps.heatmap_layer(plotly_gun_df[["lat (N)", "lng (W)"]], weights=plotly_gun_df["Norm Incidents"], max_intensity=80, point_radius=12)
fig.add_layer(heatmap_layer)
fig
```


<p>Failed to display Jupyter Widget of type <code>Figure</code>.</p>
<p>
  If you're reading this message in the Jupyter Notebook or JupyterLab Notebook, it may mean
  that the widgets JavaScript is still loading. If this message persists, it
  likely means that the widgets JavaScript library is either not installed or
  not enabled. See the <a href="https://ipywidgets.readthedocs.io/en/stable/user_install.html">Jupyter
  Widgets Documentation</a> for setup instructions.
</p>
<p>
  If you're reading this message in another frontend (for example, a static
  rendering on GitHub or <a href="https://nbviewer.jupyter.org/">NBViewer</a>),
  it may mean that your frontend doesn't currently support widgets.
</p>




```python
py.tools.set_credentials_file(username='pvirgilio', api_key='KdeHIchH9RtyP6zFr6Fh')
```


    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-217-7895fb066538> in <module>()
    ----> 1 py.tools.set_credentials_file(username='pvirgilio', api_key='KdeHIchH9RtyP6zFr6Fh')
    

    AttributeError: module 'plotly.plotly' has no attribute 'tools'



```python
plotly_gun_df.columns
```




    Index(['State_Name', 'code', 'lat (N)', 'lng (W)', 'Population',
           'Number of Deaths', 'Number of Incidents', 'Norm Incidents',
           'Norm Deaths'],
          dtype='object')




```python
df = plotly_gun_df

for col in df.columns:
    df[col] = df[col].astype(str)

scl = [[0.0, 'rgb(242,240,247)'],[0.2, 'rgb(218,218,235)'],[0.4, 'rgb(188,189,220)'],\
            [0.6, 'rgb(158,154,200)'],[0.8, 'rgb(117,107,177)'],[1.0, 'rgb(84,39,143)']]

df['text'] = df['State_Name'] + ' Number of Deaths '+df['Number of Deaths']+' Normalized Death '+df['Norm Deaths']


data = [ dict(
        type='choropleth',
        colorscale = scl,
        autocolorscale = True,
        locations = df['code'],
        z = df['Norm Deaths'].astype(float),
        locationmode = 'USA-states',
        text = df['text'],
        marker = dict(
            line = dict (
                color = 'rgb(255,255,255)',
                width = 2
            ) ),
        colorbar = dict(
            title = "# Lives lost per 100k")
        ) ]

layout = dict(
        title = 'Number of Deaths by Firearm in the U.S. per 100K (2014-2018)',
        geo = dict(
            scope='usa',
            projection=dict( type='albers usa' ),
            showlakes = True,
            lakecolor = 'rgb(255, 255, 255)'),
             )
    
fig = dict( data=data, layout=layout )
py.iplot( fig, filename='d3-cloropleth-map' )
```

    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/ipykernel_launcher.py:4: SettingWithCopyWarning:
    
    
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    
    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/ipykernel_launcher.py:8: SettingWithCopyWarning:
    
    
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    


    Aw, snap! We don't have an account for ''. Want to try again? You can authenticate with your email address or username. Sign in is not case sensitive.
    
    Don't have an account? plot.ly
    
    Questions? accounts@plot.ly



    ---------------------------------------------------------------------------

    PlotlyError                               Traceback (most recent call last)

    <ipython-input-220-7c94859d8a01> in <module>()
         36 
         37 fig = dict( data=data, layout=layout )
    ---> 38 py.iplot( fig, filename='d3-cloropleth-map' )
    

    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/plotly/plotly/plotly.py in iplot(figure_or_data, **plot_options)
        162         embed_options['height'] = str(embed_options['height']) + 'px'
        163 
    --> 164     return tools.embed(url, **embed_options)
        165 
        166 


    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/plotly/tools.py in embed(file_owner_or_url, file_id, width, height)
        388         else:
        389             url = file_owner_or_url
    --> 390         return PlotlyDisplay(url, width, height)
        391     else:
        392         if (get_config_defaults()['plotly_domain']


    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/plotly/tools.py in __init__(self, url, width, height)
       1432         def __init__(self, url, width, height):
       1433             self.resource = url
    -> 1434             self.embed_code = get_embed(url, width=width, height=height)
       1435             super(PlotlyDisplay, self).__init__(data=self.embed_code)
       1436 


    /anaconda3/envs/PythonData1/lib/python3.6/site-packages/plotly/tools.py in get_embed(file_owner_or_url, file_id, width, height)
        293                 "'{1}'."
        294                 "\nRun help on this function for more information."
    --> 295                 "".format(url, plotly_rest_url))
        296         urlsplit = six.moves.urllib.parse.urlparse(url)
        297         file_owner = urlsplit.path.split('/')[1].split('~')[1]


    PlotlyError: Because you didn't supply a 'file_id' in the call, we're assuming you're trying to snag a figure from a url. You supplied the url, '', we expected it to start with 'https://plot.ly'.
    Run help on this function for more information.



```python
#unemployment rate vs gun incidents per 100k
gun_census_deaths = gun_census_deaths.dropna()
x_axis = np.array(gun_census_deaths["Unemployment Rate"])
y_axis = gun_census_deaths["Norm Incidents"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()


ax.set_xlabel("Unemployment Rate")
ax.set_ylabel("Incidents per 100K Pop")
ax.set_title("Unemployment Rate vs. Incidents per 100K Population")

ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'r', linestyle = 'dashed', linewidth = 1)
sns.set_style("whitegrid")


# Save 
plt.grid(True)
fig.savefig("UnemploymentRate.png")


```


![png](output_81_0.png)



```python
#mental health spending per capita vs gun incidents per 100k
gun_census_deaths = gun_census_deaths.dropna()
x_axis = np.array(gun_census_deaths["expenditure_per_capita"])
y_axis = gun_census_deaths["Norm Incidents"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()


ax.set_xlabel("Mental Health Spending per Capita")
ax.set_ylabel("Incidents per 100K Pop")
ax.set_title("Mental Health Spending per Capita vs. Incidents per 100K Population")

ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'r', linestyle = 'dashed', linewidth = 1)

plt.grid(True)
fig.savefig("MentalHealth.png")
```


![png](output_82_0.png)



```python
#population vs gun incidents per 100k
gun_census_deaths = gun_census_deaths.dropna()
x_axis = np.array(gun_census_deaths["Population"]/100000)
y_axis = gun_census_deaths["Norm Incidents"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()


ax.set_xlabel("Population/100K")
ax.set_ylabel("Incidents per 100K Pop")
ax.set_title("Population/100K vs. Incidents per 100K Population")

ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'r', linestyle = 'dashed', linewidth = 1)

plt.grid(True)
fig.savefig("Populationper100K.png")
```


![png](output_83_0.png)



```python
#gun law strength vs gun incidents per 100k
gun_census_deaths = gun_census_deaths.dropna()
x_axis = np.array(gun_census_deaths["gun law strengh (rank)"])
y_axis = gun_census_deaths["Norm Incidents"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()


ax.set_xlabel("Gun Law Strength Rank")
ax.set_ylabel("Incidents per 100K Pop")
ax.set_title("Gun Law Strength Rank vs. Incidents per 100K Population")


ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'r', linestyle = 'dashed', linewidth = 1)

plt.grid(True)
fig.savefig("GunLawStrengthRank.png")
```


![png](output_84_0.png)



```python
#mental health spending vs gun incidents per 100k
gun_census_deaths = gun_census_deaths.dropna()
x_axis = np.array(gun_census_deaths["tota_expenditure"]/gun_census_deaths["Population"])
y_axis = gun_census_deaths["Norm Incidents"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()


ax.set_xlabel("Mental Health Spending per Capita")
ax.set_ylabel("Incidents per 100K Pop")
ax.set_title("Mental Health Spending per Capita vs. Incidents per 100K Population")

ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'r', linestyle = 'dashed', linewidth = 1)

plt.grid(True)
fig.savefig("MentalHealthSpendingperCapita.png")
```


![png](output_85_0.png)



```python
#education spending per pupil vs gun incidents per 100k
gun_census_deaths = gun_census_deaths.dropna()
x_axis = np.array(gun_census_deaths["total_per_pupil"])
y_axis = gun_census_deaths["Norm Incidents"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()


ax.set_xlabel("Education Spending per Pupil ($)")
ax.set_ylabel("Incidents per 100K Pop")
ax.set_title("Education Spending per Pupil vs. Incidents per 100K Population")

ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'r', linestyle = 'dashed', linewidth = 1)

plt.grid(True)
fig.savefig("EducationSpendingperPupil.png")
```


![png](output_86_0.png)



```python
#gun death rate (rank)
gun_census_deaths = gun_census_deaths.dropna()
x_axis = np.array(gun_census_deaths["gun death rate (rank per 100k)"])
y_axis = gun_census_deaths["Norm Incidents"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()


ax.set_xlabel("Gun Death Rate (Rank per 100k)")
ax.set_ylabel("Incidents per 100K Pop")
ax.set_title("Gun Death Rate vs. Incidents per 100K Population")

ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'r', linestyle = 'dashed', linewidth = 1)

plt.grid(True)
fig.savefig("GunDeathRateRank.png")
```


![png](output_87_0.png)



```python
gun_census_deaths_disp = gun_census_deaths[["State_Name","Number of Incidents","Population","Norm Incidents","Pop in 100k"]]

gun_census_deaths_disp1=gun_census_deaths_disp.sort_values("Norm Incidents",ascending=False)

gun_census_deaths_disp1.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>Number of Incidents</th>
      <th>Population</th>
      <th>Norm Incidents</th>
      <th>Pop in 100k</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>1349</td>
      <td>720316.0</td>
      <td>187.278916</td>
      <td>7.20316</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Delaware</td>
      <td>1685</td>
      <td>908446.0</td>
      <td>185.481581</td>
      <td>9.08446</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Louisiana</td>
      <td>8103</td>
      <td>4567968.0</td>
      <td>177.387407</td>
      <td>45.67968</td>
    </tr>
    <tr>
      <th>39</th>
      <td>South Carolina</td>
      <td>6939</td>
      <td>4679602.0</td>
      <td>148.281841</td>
      <td>46.79602</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Illinois</td>
      <td>17556</td>
      <td>12848554.0</td>
      <td>136.637944</td>
      <td>128.48554</td>
    </tr>
  </tbody>
</table>
</div>




```python
#total gun laws per state vs incidents per 100k
gun_census_deaths = gun_census_deaths.dropna()
x_axis = np.array(gun_census_deaths["lawtotal"])
y_axis = gun_census_deaths["Norm Incidents"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()


ax.set_xlabel("Total Number of Gun Laws per State")
ax.set_ylabel("Incidents per 100K Pop")
ax.set_title("Total Gun Laws vs. Incidents per 100K Population")

ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'r', linestyle = 'dashed', linewidth = 1)

plt.grid(True)
fig.savefig("TotalNumberGunLaws.png")
```


![png](output_89_0.png)



```python
#poverty_per_capita_rank vs incidents per 100k
gun_census_deaths = gun_census_deaths.dropna()
x_axis = np.array(gun_census_deaths["poverty_per_capita_rank"])
y_axis = gun_census_deaths["Norm Incidents"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()


ax.set_xlabel("Poverty Rank per Capita")
ax.set_ylabel("Incidents per 100K Pop")
ax.set_title("Poverty Rank per Capita vs. Incidents per 100K Population")

ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'r', linestyle = 'dashed', linewidth = 1)

plt.grid(True)
fig.savefig("PovertyRank.png")
```


![png](output_90_0.png)



```python
#Median Age per state vs incidents per 100k
gun_census_deaths = gun_census_deaths.dropna()
x_axis = np.array(gun_census_deaths["Median Age"])
y_axis = gun_census_deaths["Norm Incidents"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()


ax.set_xlabel("Median Age per State")
ax.set_ylabel("Incidents per 100K Pop")
ax.set_title("Median Age per State vs. Incidents per 100K Population")

ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'r', linestyle = 'dashed', linewidth = 1)

plt.grid(True)
fig.savefig("MedianAge.png")
```


![png](output_91_0.png)



```python
#Per Capita Income per state vs incidents per 100k
gun_census_deaths = gun_census_deaths.dropna()
x_axis = np.array(gun_census_deaths["Per Capita Income"])
y_axis = gun_census_deaths["Norm Incidents"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()


ax.set_xlabel("Income per Capita")
ax.set_ylabel("Incidents per 100K Pop")
ax.set_title("Income per Capita vs. Incidents per 100K Population")

ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'r', linestyle = 'dashed', linewidth = 1)

plt.grid(True)
fig.savefig("Income.png")
```


![png](output_92_0.png)



```python
# Store filepath in a variable
gun_onwership_by_state = "Resources/GunsByState.xlsx"
```


```python
gun_ownership_by_state_df = pd.read_excel(gun_onwership_by_state)

gun_ownership_by_state_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>Gun ownership rates</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>48.9</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>61.7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>32.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>57.9</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>20.1</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Merge gun_census with gun ownership by state
gun_census_deaths_owner = gun_census_deaths.merge(gun_ownership_by_state_df, how = 'outer', on = 'State_Name')
gun_census_deaths_owner.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>year</th>
      <th>lawtotal</th>
      <th>total_spending</th>
      <th>total_per_pupil</th>
      <th>gun law strengh (rank)</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
      <th>Letter_Grade</th>
      <th>...</th>
      <th>Household Income</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
      <th>Unemployment Rate</th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
      <th>Pop in 100k</th>
      <th>Norm Incidents</th>
      <th>Gun ownership rates</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>2017.0</td>
      <td>10.0</td>
      <td>6.826622e+09</td>
      <td>9128.0</td>
      <td>36.0</td>
      <td>F</td>
      <td>2.0</td>
      <td>21.4</td>
      <td>F</td>
      <td>...</td>
      <td>43253.0</td>
      <td>23680.0</td>
      <td>870631.0</td>
      <td>18.140878</td>
      <td>5.040968</td>
      <td>1880</td>
      <td>5471</td>
      <td>47.99277</td>
      <td>113.996337</td>
      <td>48.9</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>2017.0</td>
      <td>4.0</td>
      <td>2.646225e+09</td>
      <td>20172.0</td>
      <td>44.0</td>
      <td>F</td>
      <td>1.0</td>
      <td>23.0</td>
      <td>F</td>
      <td>...</td>
      <td>70760.0</td>
      <td>32651.0</td>
      <td>69514.0</td>
      <td>9.650487</td>
      <td>4.572854</td>
      <td>267</td>
      <td>1349</td>
      <td>7.20316</td>
      <td>187.278916</td>
      <td>61.7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>7.205417e+09</td>
      <td>7489.0</td>
      <td>47.0</td>
      <td>F</td>
      <td>16.0</td>
      <td>15.2</td>
      <td>F</td>
      <td>...</td>
      <td>49774.0</td>
      <td>25358.0</td>
      <td>1131901.0</td>
      <td>17.468409</td>
      <td>4.882323</td>
      <td>1094</td>
      <td>2328</td>
      <td>64.79703</td>
      <td>35.927573</td>
      <td>32.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>4.683434e+09</td>
      <td>9694.0</td>
      <td>39.0</td>
      <td>F</td>
      <td>9.0</td>
      <td>17.7</td>
      <td>F</td>
      <td>...</td>
      <td>40768.0</td>
      <td>22170.0</td>
      <td>547328.0</td>
      <td>18.658682</td>
      <td>4.132961</td>
      <td>773</td>
      <td>2842</td>
      <td>29.33369</td>
      <td>96.885186</td>
      <td>57.9</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>2017.0</td>
      <td>106.0</td>
      <td>6.620811e+10</td>
      <td>10467.0</td>
      <td>1.0</td>
      <td>A</td>
      <td>43.0</td>
      <td>7.9</td>
      <td>A</td>
      <td>...</td>
      <td>61094.0</td>
      <td>29527.0</td>
      <td>5885417.0</td>
      <td>15.628107</td>
      <td>5.758662</td>
      <td>5562</td>
      <td>16306</td>
      <td>376.59181</td>
      <td>43.298870</td>
      <td>20.1</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 27 columns</p>
</div>




```python
#Gun ownership rates per state vs incidents per 100k
gun_census_deaths_owner = gun_census_deaths_owner.dropna()
x_axis = np.array(gun_census_deaths_owner["Gun ownership rates"])
y_axis = gun_census_deaths_owner["Norm Incidents"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()


ax.set_xlabel("Gun Ownership Percentage per Capita")
ax.set_ylabel("Incidents per 100K Pop")
ax.set_title("Gun Ownership Percentage vs. Incidents per 100K Population")

ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'r', linestyle = 'dashed', linewidth = 1)

plt.grid(True)
fig.savefig("GunOwnershipPercentage.png")
```


![png](output_96_0.png)



```python
# Store filepath in a variable
police_spending_by_state = "Resources/PoliceExpenditureByState.xlsx"
```


```python
police_spending_by_state_df = pd.read_excel(police_spending_by_state)

police_spending_by_state_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>PoliceExpenditurePerCapita</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>38.15</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>219.55</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>36.51</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>39.65</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>45.92</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Merge gun_census with gun ownership by state
gun_census_deaths_owner_police = gun_census_deaths_owner.merge(police_spending_by_state_df, how = 'outer', on = 'State_Name')
gun_census_deaths_owner_police.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>State_Name</th>
      <th>year</th>
      <th>lawtotal</th>
      <th>total_spending</th>
      <th>total_per_pupil</th>
      <th>gun law strengh (rank)</th>
      <th>grade (2017)</th>
      <th>gun death rate (rank)</th>
      <th>gun death rate (rank per 100k)</th>
      <th>Letter_Grade</th>
      <th>...</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
      <th>Unemployment Rate</th>
      <th>Number of Deaths</th>
      <th>Number of Incidents</th>
      <th>Pop in 100k</th>
      <th>Norm Incidents</th>
      <th>Gun ownership rates</th>
      <th>PoliceExpenditurePerCapita</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alabama</td>
      <td>2017.0</td>
      <td>10.0</td>
      <td>6.826622e+09</td>
      <td>9128.0</td>
      <td>36.0</td>
      <td>F</td>
      <td>2.0</td>
      <td>21.4</td>
      <td>F</td>
      <td>...</td>
      <td>23680.0</td>
      <td>870631.0</td>
      <td>18.140878</td>
      <td>5.040968</td>
      <td>1880</td>
      <td>5471</td>
      <td>47.99277</td>
      <td>113.996337</td>
      <td>48.9</td>
      <td>38.15</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alaska</td>
      <td>2017.0</td>
      <td>4.0</td>
      <td>2.646225e+09</td>
      <td>20172.0</td>
      <td>44.0</td>
      <td>F</td>
      <td>1.0</td>
      <td>23.0</td>
      <td>F</td>
      <td>...</td>
      <td>32651.0</td>
      <td>69514.0</td>
      <td>9.650487</td>
      <td>4.572854</td>
      <td>267</td>
      <td>1349</td>
      <td>7.20316</td>
      <td>187.278916</td>
      <td>61.7</td>
      <td>219.55</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>7.205417e+09</td>
      <td>7489.0</td>
      <td>47.0</td>
      <td>F</td>
      <td>16.0</td>
      <td>15.2</td>
      <td>F</td>
      <td>...</td>
      <td>25358.0</td>
      <td>1131901.0</td>
      <td>17.468409</td>
      <td>4.882323</td>
      <td>1094</td>
      <td>2328</td>
      <td>64.79703</td>
      <td>35.927573</td>
      <td>32.3</td>
      <td>36.51</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arkansas</td>
      <td>2017.0</td>
      <td>11.0</td>
      <td>4.683434e+09</td>
      <td>9694.0</td>
      <td>39.0</td>
      <td>F</td>
      <td>9.0</td>
      <td>17.7</td>
      <td>F</td>
      <td>...</td>
      <td>22170.0</td>
      <td>547328.0</td>
      <td>18.658682</td>
      <td>4.132961</td>
      <td>773</td>
      <td>2842</td>
      <td>29.33369</td>
      <td>96.885186</td>
      <td>57.9</td>
      <td>39.65</td>
    </tr>
    <tr>
      <th>4</th>
      <td>California</td>
      <td>2017.0</td>
      <td>106.0</td>
      <td>6.620811e+10</td>
      <td>10467.0</td>
      <td>1.0</td>
      <td>A</td>
      <td>43.0</td>
      <td>7.9</td>
      <td>A</td>
      <td>...</td>
      <td>29527.0</td>
      <td>5885417.0</td>
      <td>15.628107</td>
      <td>5.758662</td>
      <td>5562</td>
      <td>16306</td>
      <td>376.59181</td>
      <td>43.298870</td>
      <td>20.1</td>
      <td>45.92</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 28 columns</p>
</div>




```python
#Police expenditure per capita per state vs incidents per 100k per state
gun_census_deaths_owner_police = gun_census_deaths_owner_police.dropna()
x_axis = np.array(gun_census_deaths_owner_police["PoliceExpenditurePerCapita"])
y_axis = gun_census_deaths_owner_police["Norm Incidents"]

y_axis.tolist()
(slope, intercept, _, _, _) = linregress(x_axis, y_axis.tolist())

slope

fit = slope * x_axis + intercept
fit
fig, ax = plt.subplots()


ax.set_xlabel("Police Expenditure per Capita")
ax.set_ylabel("Incidents per 100k Population")
ax.set_title("Police Expenditure per Capita vs. Incidents per 100K Pop")

ax.plot(x_axis, y_axis, linewidth=0, marker='o')
ax.plot(x_axis, fit, 'r', linestyle = 'dashed', linewidth = 1)

plt.grid(True)
fig.savefig("PoliceExpenditure.png")
```


![png](output_100_0.png)



```python
# per100k = Take state population and divide by 100000
# Take incidents/deaths and divide that by per100k
per100k = gun_census_deaths["Population"]/100000
norm_deaths = gun_census_deaths["Number of Deaths"]/per100k
norm_incidents = gun_census_deaths["Number of Incidents"]/per100k
```


```python
# View all columns in our dataframe
gun_census_deaths.columns
```




    Index(['State_Name', 'year', 'lawtotal', 'total_spending', 'total_per_pupil',
           'gun law strengh (rank)', 'grade (2017)', 'gun death rate (rank)',
           'gun death rate (rank per 100k)', 'Letter_Grade', 'tota_expenditure',
           'expenditure_per_capita', 'per_capita_rank',
           'poverty_per_capita_expenditure', 'poverty_per_capita_rank',
           'Population', 'Median Age', 'Household Income', 'Per Capita Income',
           'Poverty Count', 'Poverty Rate', 'Unemployment Rate',
           'Number of Deaths', 'Number of Incidents', 'Pop in 100k',
           'Norm Incidents'],
          dtype='object')




```python
# Create a bar graph to show the letter grade vs the number of incidents/population
x_axis = gun_census_deaths["Letter_Grade"]
y_axis = gun_census_deaths["Number of Incidents"] / gun_census_deaths["Population"] * 100000
plt.ylabel("Incidents per 100k Population")
plt.xlabel("Gun Law Letter Grade")
plt.title("Gun Law Strength Grade (by State) vs. Incidents per 100K Pop")
plt.bar(x_axis, y_axis)
sns.set_style("whitegrid")
plt.savefig("Grade_Incidents")
```


![png](output_103_0.png)



```python
# Create a bar graph to show the letter grade vs the number of deaths/population
x_axis = gun_census_deaths["Letter_Grade"]
pop = gun_census_deaths["Population"]/100000
y_axis = gun_census_deaths["Number of Deaths"] / pop
plt.xlabel("Gun Law Letter Grade")
plt.ylabel("Deaths per 100K Pop")
plt.title("Gun Law Strength Grade (by State) vs. Deaths per 100K Pop")
plt.bar(x_axis, y_axis)
sns.set_style("whitegrid")
plt.savefig("Grade_Deaths")
```


![png](output_104_0.png)

