# VacationPy
----

#### Note
* Instructions have been included for each segment. You do not have to follow them exactly, but they are included to help you think through the steps.


```python
# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import requests
import gmaps
import os
import json
import pprint

# Import API key
from api_keys import g_key
```

### Store Part I results into DataFrame
* Load the csv exported in Part I to a DataFrame


```python
# Read csv
weather_csv = pd.read_csv("../WeatherPy/output/weather_data.csv")

# Convert csv into data frame
weather_data = pd.DataFrame(weather_csv)

# Clean data frame 
weather_data=weather_data.drop(labels='Unnamed: 0', axis=1)

weather_data
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
      <th>City</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>WindSpeed</th>
      <th>Country</th>
      <th>Date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Nianzishan</td>
      <td>47.52</td>
      <td>122.88</td>
      <td>56.05</td>
      <td>46</td>
      <td>4</td>
      <td>4.00</td>
      <td>CN</td>
      <td>1589042647</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bluff</td>
      <td>-46.60</td>
      <td>168.33</td>
      <td>52.68</td>
      <td>80</td>
      <td>30</td>
      <td>10.18</td>
      <td>NZ</td>
      <td>1589042058</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Ubari</td>
      <td>26.59</td>
      <td>12.78</td>
      <td>96.85</td>
      <td>9</td>
      <td>0</td>
      <td>11.03</td>
      <td>LY</td>
      <td>1589042149</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Nowy Dwór Gdański</td>
      <td>54.21</td>
      <td>19.12</td>
      <td>59.00</td>
      <td>58</td>
      <td>37</td>
      <td>6.93</td>
      <td>PL</td>
      <td>1589042648</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Hithadhoo</td>
      <td>-0.60</td>
      <td>73.08</td>
      <td>85.14</td>
      <td>79</td>
      <td>100</td>
      <td>13.87</td>
      <td>MV</td>
      <td>1589041968</td>
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
    </tr>
    <tr>
      <th>564</th>
      <td>Preeceville</td>
      <td>51.95</td>
      <td>-102.67</td>
      <td>37.22</td>
      <td>75</td>
      <td>100</td>
      <td>7.45</td>
      <td>CA</td>
      <td>1589042584</td>
    </tr>
    <tr>
      <th>565</th>
      <td>Whitehorse</td>
      <td>60.72</td>
      <td>-135.05</td>
      <td>53.01</td>
      <td>45</td>
      <td>20</td>
      <td>2.24</td>
      <td>CA</td>
      <td>1589042793</td>
    </tr>
    <tr>
      <th>566</th>
      <td>Pisco</td>
      <td>-13.70</td>
      <td>-76.22</td>
      <td>71.01</td>
      <td>82</td>
      <td>30</td>
      <td>8.05</td>
      <td>PE</td>
      <td>1589042793</td>
    </tr>
    <tr>
      <th>567</th>
      <td>Celestún</td>
      <td>20.87</td>
      <td>-90.40</td>
      <td>88.32</td>
      <td>58</td>
      <td>86</td>
      <td>4.94</td>
      <td>MX</td>
      <td>1589042794</td>
    </tr>
    <tr>
      <th>568</th>
      <td>Basoko</td>
      <td>1.24</td>
      <td>23.62</td>
      <td>75.88</td>
      <td>80</td>
      <td>73</td>
      <td>2.98</td>
      <td>CD</td>
      <td>1589042794</td>
    </tr>
  </tbody>
</table>
<p>569 rows × 9 columns</p>
</div>



### Humidity Heatmap
* Configure gmaps.
* Use the Lat and Lng as locations and Humidity as the weight.
* Add Heatmap layer to map.


```python
# Configure gmaps
gmaps.configure(api_key=g_key)

# Set variables for location and humidity
locations_ = weather_data[['Lat', 'Lng']]
humidity = weather_data['Humidity'].astype(float)

```


```python
# figure_layout = {
#     'width': '600px',
#     'height': '400px',
#     'border': '1px solid black',
#     'padding': '1px',
#     'margin': '0 auto 0 auto'
# } layout=figure_layout

# Create gmap with heat layer
fig = gmaps.figure(zoom_level = 1.2, center = (25, 25))
heat_layer = gmaps.heatmap_layer(locations_, weights = humidity, dissipating = False, max_intensity=100, point_radius=3)
fig.add_layer(heat_layer)
fig
```


    Figure(layout=FigureLayout(height='420px'))


### Create new DataFrame fitting weather criteria
* Narrow down the cities to fit weather conditions.
* Drop any rows will null values.


```python
# Filter data to ideal vacation weather
vacation_data = weather_data.loc[(weather_data['Max Temp'] > 50) & (weather_data['Max Temp'] < 80) & (weather_data['Humidity'] < 35) & 
                 (weather_data['Cloudiness'] < 10) & (weather_data['WindSpeed'] < 10)] 

vacation_data = vacation_data.dropna()
```

### Hotel Map
* Store into variable named `hotel_df`.
* Add a "Hotel Name" column to the DataFrame.
* Set parameters to search for hotels with 5000 meters.
* Hit the Google Places API for each city's coordinates.
* Store the first Hotel result into the DataFrame.
* Plot markers on top of the heatmap.


```python
# Store filtered data into data frame
hotel_df = pd.DataFrame(vacation_data)

# Add 'Hotel Name' column
hotel_df['Hotel Name'] = ''

# Clean data
hotel_df = hotel_df.reset_index().drop(labels='index', axis = 1)
hotel_df

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
      <th>City</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>WindSpeed</th>
      <th>Country</th>
      <th>Date</th>
      <th>Hotel Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Altay</td>
      <td>47.87</td>
      <td>88.12</td>
      <td>55.72</td>
      <td>33</td>
      <td>0</td>
      <td>9.26</td>
      <td>CN</td>
      <td>1589042572</td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>Taos</td>
      <td>36.41</td>
      <td>-105.57</td>
      <td>57.99</td>
      <td>26</td>
      <td>1</td>
      <td>8.05</td>
      <td>US</td>
      <td>1589042496</td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>Korla</td>
      <td>41.76</td>
      <td>86.15</td>
      <td>68.95</td>
      <td>23</td>
      <td>9</td>
      <td>3.76</td>
      <td>CN</td>
      <td>1589042139</td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>Yuty</td>
      <td>-26.60</td>
      <td>-56.25</td>
      <td>78.17</td>
      <td>30</td>
      <td>0</td>
      <td>8.97</td>
      <td>PY</td>
      <td>1589042684</td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>Saint George</td>
      <td>37.10</td>
      <td>-113.58</td>
      <td>79.00</td>
      <td>17</td>
      <td>1</td>
      <td>3.36</td>
      <td>US</td>
      <td>1589042685</td>
      <td></td>
    </tr>
    <tr>
      <th>5</th>
      <td>Gemeente Wageningen</td>
      <td>51.97</td>
      <td>5.67</td>
      <td>75.20</td>
      <td>27</td>
      <td>0</td>
      <td>5.82</td>
      <td>NL</td>
      <td>1589042686</td>
      <td></td>
    </tr>
    <tr>
      <th>6</th>
      <td>San Juan</td>
      <td>-31.54</td>
      <td>-68.54</td>
      <td>77.38</td>
      <td>23</td>
      <td>1</td>
      <td>4.36</td>
      <td>AR</td>
      <td>1589042713</td>
      <td></td>
    </tr>
    <tr>
      <th>7</th>
      <td>Tarata</td>
      <td>-17.62</td>
      <td>-66.02</td>
      <td>75.20</td>
      <td>22</td>
      <td>0</td>
      <td>1.86</td>
      <td>BO</td>
      <td>1589042727</td>
      <td></td>
    </tr>
    <tr>
      <th>8</th>
      <td>Springbok</td>
      <td>-29.66</td>
      <td>17.89</td>
      <td>70.11</td>
      <td>23</td>
      <td>0</td>
      <td>9.86</td>
      <td>ZA</td>
      <td>1589042731</td>
      <td></td>
    </tr>
    <tr>
      <th>9</th>
      <td>Neyshabur</td>
      <td>36.21</td>
      <td>58.80</td>
      <td>71.67</td>
      <td>23</td>
      <td>0</td>
      <td>4.09</td>
      <td>IR</td>
      <td>1589042744</td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>




```python
# base url
base_url = "https://maps.googleapis.com/maps/api/place/nearbysearch/json"

# search parameters
params = {
    "radius": 5000,
    "type": 'lodging',
    "search": 'hotel',
    "key": g_key,
}

# iterate through rows to pull location for seach and add hotel name to data frame
for index, row in hotel_df.iterrows():
    
    locations = f"{row['Lat']} {row['Lng']}"
    params['location'] = locations
        
    response = requests.get(base_url, params=params).json()

    try:
        hotel_df.loc[index, 'Hotel Name'] = response['results'][0]['name']
        
    except IndexError:
        hotel_df.loc[index, 'Hotel Name'] = ''
        
hotel_df = hotel_df.loc[hotel_df['Hotel Name'] != '']
hotel_df

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
      <th>City</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>WindSpeed</th>
      <th>Country</th>
      <th>Date</th>
      <th>Hotel Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Altay</td>
      <td>47.87</td>
      <td>88.12</td>
      <td>55.72</td>
      <td>33</td>
      <td>0</td>
      <td>9.26</td>
      <td>CN</td>
      <td>1589042572</td>
      <td>Jinqiao Hotel</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Taos</td>
      <td>36.41</td>
      <td>-105.57</td>
      <td>57.99</td>
      <td>26</td>
      <td>1</td>
      <td>8.05</td>
      <td>US</td>
      <td>1589042496</td>
      <td>Inn on La Loma Plaza</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Korla</td>
      <td>41.76</td>
      <td>86.15</td>
      <td>68.95</td>
      <td>23</td>
      <td>9</td>
      <td>3.76</td>
      <td>CN</td>
      <td>1589042139</td>
      <td>Gulisitan Hotel</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Yuty</td>
      <td>-26.60</td>
      <td>-56.25</td>
      <td>78.17</td>
      <td>30</td>
      <td>0</td>
      <td>8.97</td>
      <td>PY</td>
      <td>1589042684</td>
      <td>Hotel Independencia</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Saint George</td>
      <td>37.10</td>
      <td>-113.58</td>
      <td>79.00</td>
      <td>17</td>
      <td>1</td>
      <td>3.36</td>
      <td>US</td>
      <td>1589042685</td>
      <td>Best Western Plus Abbey Inn</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Gemeente Wageningen</td>
      <td>51.97</td>
      <td>5.67</td>
      <td>75.20</td>
      <td>27</td>
      <td>0</td>
      <td>5.82</td>
      <td>NL</td>
      <td>1589042686</td>
      <td>Fletcher Hotel-Restaurant De Wageningsche Berg</td>
    </tr>
    <tr>
      <th>6</th>
      <td>San Juan</td>
      <td>-31.54</td>
      <td>-68.54</td>
      <td>77.38</td>
      <td>23</td>
      <td>1</td>
      <td>4.36</td>
      <td>AR</td>
      <td>1589042713</td>
      <td>Hotel Provincial</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Tarata</td>
      <td>-17.62</td>
      <td>-66.02</td>
      <td>75.20</td>
      <td>22</td>
      <td>0</td>
      <td>1.86</td>
      <td>BO</td>
      <td>1589042727</td>
      <td>TARA TARA Centro Alternativo Hosting</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Springbok</td>
      <td>-29.66</td>
      <td>17.89</td>
      <td>70.11</td>
      <td>23</td>
      <td>0</td>
      <td>9.86</td>
      <td>ZA</td>
      <td>1589042731</td>
      <td>Faithful Guest Lodge</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Neyshabur</td>
      <td>36.21</td>
      <td>58.80</td>
      <td>71.67</td>
      <td>23</td>
      <td>0</td>
      <td>4.09</td>
      <td>IR</td>
      <td>1589042744</td>
      <td>Amiran Hotel</td>
    </tr>
  </tbody>
</table>
</div>




```python
# NOTE: Do not change any of the code in this cell

# Using the template add the hotel marks to the heatmap
info_box_template = """
<dl>
<dt>Name</dt><dd>{Hotel Name}</dd>
<dt>City</dt><dd>{City}</dd>
<dt>Country</dt><dd>{Country}</dd>
</dl>
"""
# Store the DataFrame Row
hotel_info = [info_box_template.format(**row) for index, row in hotel_df.iterrows()]
locations = hotel_df[["Lat", "Lng"]]
locations

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
      <th>Lat</th>
      <th>Lng</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>47.87</td>
      <td>88.12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>36.41</td>
      <td>-105.57</td>
    </tr>
    <tr>
      <th>2</th>
      <td>41.76</td>
      <td>86.15</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-26.60</td>
      <td>-56.25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>37.10</td>
      <td>-113.58</td>
    </tr>
    <tr>
      <th>5</th>
      <td>51.97</td>
      <td>5.67</td>
    </tr>
    <tr>
      <th>6</th>
      <td>-31.54</td>
      <td>-68.54</td>
    </tr>
    <tr>
      <th>7</th>
      <td>-17.62</td>
      <td>-66.02</td>
    </tr>
    <tr>
      <th>8</th>
      <td>-29.66</td>
      <td>17.89</td>
    </tr>
    <tr>
      <th>9</th>
      <td>36.21</td>
      <td>58.80</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Add marker layer ontop of heat map
markers = gmaps.marker_layer(locations, hotel_info)

fig.add_layer(markers)
fig

# Display figure

```


    Figure(layout=FigureLayout(height='420px'))



```python

```
