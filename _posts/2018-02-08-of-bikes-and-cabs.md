---
layout: single
title: Of Bikes and Cabs
categories:
  - EDA
  - NYC
#tags:
#  - nyc
#  - bikes
#  - citibikes
#  - taxi
---


Can riding a bike in NYC be faster than getting a cab?

Here I am trying to answer this and other questions in my first blog post. It is mostly based on some musing I had while
participating to a [Kaggle challenge](https://www.asda) on taxi rides in New York City. 
I decided to take a look at correlations between the taxi rides dataset and the one available
on [citibike trips](https://www.asda). Citibike is a bike sharing service with ~500 stations spread all over the city
mainly in Manhattan and Brooklyn. I briefly cleaned the latter dataset, for instance matching the time window with the 
taxi one to the first half of 2016, and published it on [Kaggle](https://www.asda).


## Data

The two dataset contain respectively n and m rides, spread throughout the first six months of 2016.
The both contain starting and arrival points and times, duration of the trip plus a number of other 
information we are less interested about (e.g. biker ID).
To give an idea of the spacial distribution here is a map of the taxi pick-up locations and bike stations 
(I cut cab rides to airports).

[Burp](https://www.asda) fy [repo](https://github.com/roundedup/xxx).


<img width="320"  src="https://roundedup.github.io/assets/images/bikecabs/scatter.jpg" alt="">

## Weekly

We are ready to answer the initial question. What is faster?
To simulate the grid structure of most parts of the city I computed a L1 distance traveled for each trip, 
and subsequently computed the average speed. The big assumption is that bikes are going from station to station
without stopping, I think it is a fair assumption given how citibike works, and especially during weekdays.
Here is the result averaged over all the weeks in the dataset.
There is a bit effect of traffic in cabs and bikes can indeed be faster. The bands correspond to
So if you are a slightly above average biker and there is some traffic, better ride that bike!

<img src="https://roundedup.github.io/assets/images/bikecabs/speed.png" alt="">

<img src="https://roundedup.github.io/assets/images/bikecabs/lognormal.png" alt="">

As a side node the distribution of taxi's velocities beautifully follows a [log normal distribution](https://en.wikipedia.org/wiki/Log-normal_distribution#Occurrence_and_applications), 
while bikes are closer to a normal distribution with some big tail, probably dominated by long leisure trips.


We can also just look at the overall weekly usage and duration of trips. Not surprisingly people like to take longer
bike trips on weekends, more surprisingly there are a lot of commuters who enjoy bikes.

<img src="https://roundedup.github.io/assets/images/bikecabs/usage.png" alt="">
<img src="https://roundedup.github.io/assets/images/bikecabs/duration.png" alt="">


## Semester

<img src="https://roundedup.github.io/assets/images/bikecabs/taxi_year.png" alt="">
<img src="https://roundedup.github.io/assets/images/bikecabs/bike_year.png" alt="">


## Video

Finally I had a bit of fun trying to produce a video to show the distribution in time and space of bike rides.
How do you like it? basically I produced many (countless) plots and then put them together 
with a time lapse software. The code is very rudimentary, you can find it with the rest of the code used for this post on my on my GitHub 
[repo](https://github.com/roundedup)
  

<video width="320"  controls="controls">
  <source src="https://roundedup.github.io/assets/videos/bikecabs/fin.mp4" type="video/mp4">
</video>

