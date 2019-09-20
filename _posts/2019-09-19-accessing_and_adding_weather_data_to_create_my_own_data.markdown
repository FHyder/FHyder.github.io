---
layout: post
title:      "Accessing and adding weather data to create my own data."
date:       2019-09-19 22:15:44 -0400
permalink:  accessing_and_adding_weather_data_to_create_my_own_data
---


I’m at a point in my DataScience career where I am ready to generate my own data to build predictions on. Do you ever look at a dataset and just wonder “I bet if we had info on “__” my model would perform way better”. I’m born and raised in New York, a city girl through and through, but my parents are from Bangladesh, so maybe a city girl with some jungle mixed in —concrete jungle.

Bangladesh has suffered from an especially bad outbreak of Dengue fever this year, a mosquito born disease that causes very intense flu-like symptoms in most. In extreme cases it can be lethal. I once got it while living in Bangladesh and I thought it would be interesting to gather some data on this disease. After some thorough research I found a dataset that reported on cases in Malaysia from 1993 - 2010. Southeast Asia climate is generally subtropical, perfect breeding grounds for mosquitos. I thought it would be interesting to collect some weather data to my set. I turned to dark sky, a website that offers historical weather data based on location. 

First, you have to get a key from the dark sky website an store it in as a key object  

I scoped out all the unique values in my “City” feature and then did a quick google search on latitude, longitude and stored these as variables

![](http://github.com/FHyder/CapstoneProject/blob/master/df.city.unique.png)
![](http://github.com/FHyder/CapstoneProject/blob/master/df.city.unique2.png)




To get historical weather data you create the forecast object with the following parameters (*key,latitude,longitude, time=(year, month, day).isoformat())). I accessed the ‘data’ key in the forecast object to find specific information for that day

![](https://github.com/FHyder/CapstoneProject/blob/master/forecast.png)




I then created a function to extract weather information and used it on 10 different cities.

![](https://github.com/FHyder/CapstoneProject/blob/master/getweatherdata.png)








However, until about 2003 there were only about 11 weather stations in all of Malaysia so there were definitely instances of missing data. 


![](https://github.com/FHyder/CapstoneProject/blob/master/incomplete.png)



To remedy this I’m going to fill all null values with the average of that feature for that month based on available data

```%%time

mdf.cloudCover = mdf[['City', 'month', 'cloudCover']].groupby(['City','month']).transform(lambda x: x.fillna(x.mean()))  
mdf.windSpeed =  mdf[['City', 'month', 'windSpeed']].groupby(['City','month']).transform(lambda x: x.fillna(x.mean()))
mdf.humidity =  mdf[['City', 'month', 'humidity']].groupby(['City','month']).transform(lambda x: x.fillna(x.mean()))
mdf.temperatureMax =  mdf[['City', 'month', 'temperatureMax']].groupby(['City','month']).transform(lambda x: x.fillna(x.mean()))
```





Now I had complete weather data added to my set!




![](https://github.com/FHyder/CapstoneProject/blob/master/complete.png)

