---
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


Can a bike in NYC be faster than a cab?

Here I am trying to answer this and other questions. This first blog post is mostly based on some musings I had while
participating to a [Kaggle challenge on taxi trips](https://www.kaggle.com/c/nyc-taxi-trip-duration) in New York City. 
I decided to take a look at correlations between the taxi rides dataset and the one available
on [Citi Bike trips](https://www.citibikenyc.com/system-data). Citi bike is a bike sharing service with ~500 stations spread all over the city,
mainly in Manhattan and Brooklyn.


## Data Overview

The two dataset contain respectively 1.5 and 4.5 milion rides, that took place during the first six months of 2016.
Each ride is characterized by starting and arrival points, respective times, and a number of other 
information we are less interested in (e.g. biker or taxi ID).
To give an idea of the spacial distribution here is a map of the Citi Bike stations and a subset of the taxi pick-up locations.
I did not include cab rides to and from airports and in general I cleaned the datasets from outliers and other spurious entries.

<img width="360"  src="https://roundedup.github.io/assets/images/bikecabs/scatter.jpg" alt="">

## Weekly

We are ready to answer the initial question. What is faster?
To simulate the grid structure of most parts of the city I computed a L1 distance traveled for each trip, 
and subsequently computed the average velocity. The big assumption is that bikes are going from station to station
without stopping, I think it is a fair assumption especially during weekdays and given how Citi Bike works.
Here is the result averaged over all the weeks in the dataset. The bands correspond to 68% interval.

<img src="https://roundedup.github.io/assets/images/bikecabs/speed.png" alt="">

<img src="https://roundedup.github.io/assets/images/bikecabs/lognormal.png" alt="">

The effect of traffic is clear on the cabs, interestingly also bikers go faster at night (skipping some red light?).
So if you are a slightly above average biker and there is some traffic, better ride that bike!

As a side node the distribution of taxi's velocities beautifully follows a [log normal distribution](https://en.wikipedia.org/wiki/Log-normal_distribution#Occurrence_and_applications), 
while bikes are closer to a normal distribution with some big tail, probably dominated by leisure trips.


We can also just look at the overall weekly usage and duration of trips. Not surprisingly people like to take longer
bike trips on weekends, more surprisingly there are a lot of commuters who enjoy bikes.

<img src="https://roundedup.github.io/assets/images/bikecabs/usage.png" alt="">
<img src="https://roundedup.github.io/assets/images/bikecabs/duration.png" alt="">


## Semester
What happens when we look at seasonal effects?
As a quick overview we can look at a color map of usage per hour of the day for each day in the dataset. The following plots 
illustrate again what seen above but they also reveal how weather has a clear impact on bikes
 (warmer and better weather corresponds to more rides). 
Fun fact the white line early in the year is not an artifact. It corresponds to the blizzard of January 23-24.


<img src="https://roundedup.github.io/assets/images/bikecabs/taxi_year.png" alt="">
<img src="https://roundedup.github.io/assets/images/bikecabs/bike_year.png" alt="">


## Video

Finally I had a bit of fun trying to produce a video to show the distribution in time and space of bike rides.
How do you like it? Basically for each trip I assume a straight trajectory and constant speed, given a time I compute the hypothetical 
position of each bike riding at that time. I produced many (countless) plots and then put them together 
with a time lapse software. You can find the code I used to produce the frames with the rest of the code used for this post on my on my GitHub 
[repo](https://github.com/roundedup), you will find some more plots and some other experiments with the data.
  

<video width="320"  controls="controls">
  <source src="https://roundedup.github.io/assets/videos/bikecabs/fin.mp4" type="video/mp4">
</video>

