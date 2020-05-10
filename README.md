# python-apis-challenge

## Part I - WeatherPy

Goal in this project is to create a Python script to visualize the weather of 500+ cities across the world of varying distance from the equator. To accomplish this, I used Citipy library and [OpenWeatherMap API](https://openweathermap.org/api).

First requirement is to create a series of scatter plots to showcase the following relationships:

* Temperature (F) vs. Latitude
* Humidity (%) vs. Latitude
* Cloudiness (%) vs. Latitude
* Wind Speed (mph) vs. Latitude

Second requirement is to run linear regression on each relationship, only this time separating them into Northern Hemisphere (greater than or equal to 0 degrees latitude) and Southern Hemisphere (less than 0 degrees latitude):

* Northern Hemisphere - Temperature (F) vs. Lat
* Southern Hemisphere - Temperature (F) vs. Lat
* Northern Hemisphere - Humidity (%) vs. Lat
* Southern Hemisphere - Humidity (%) vs. Lat
* Northern Hemisphere - Cloudiness (%) vs. Lat
* Southern Hemisphere - Cloudiness (%) vs. Lat
* Northern Hemisphere - Wind Speed (mph) vs. Lat
* Southern Hemisphere - Wind Speed (mph) vs. Lat

Final notebook includes:

* Randomly select **at least** 500 unique (non-repeat) cities based on latitude and longitude.
* Perform a weather check on each of the cities using a series of successive API calls.
* Include a print log of each city as it's being processed with the city number and city name.
* CSV of all retrieved data and a PNG image for each scatter plot.

### Part II - VacationPy

Goal of this project is to use weather data to identify hotels in cities with ideal weather condition. Tools include jupyter-gmaps and the Google Places API.

* Create a heat map that displays the humidity for every city from the part I - WeatherPy.

* Narrow down the DataFrame to find ideal weather condition.

* Using Google Places API to find the first hotel for each city located within 5000 meters of coordinates.

* Plot the hotels on top of the humidity heatmap with each pin containing the **Hotel Name**, **City**, and **Country**.