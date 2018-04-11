---
title: Kick
categories:
  - Kickstarter
  - 
#tags:
#  - nyc
#  - bikes
#  - citibikes
#  - taxi
---


Can the success of a crowdfunding campaign be predicted? 
Is it easier to fund a "film" or a "movie"? And in which state? 
We will find out a few fascinating correlations.


## Data overview

Kickstarter is a crowdfunding campaigns website. Anybody can try to fund their project, you just need
to give it a title and assign it a category (and sub category), write a short description (blurb) and a longer one, set a monetary goal.
A location is also associated with it, and there is a deadline usually set within one or two months from the start of the campaign.
There are other details (intermediate, ) but we will neglect them.

That's all we know, is it enough to predict its success?
To answer this question I used the dataset made available [here](https://webrobots.io/kickstarter-datasets/) by Web Robots. 
It contains more the data from 100k campaigns, including goal and amount of funding collected by the deadline.

The dataset required minimal cleaning, after removing some outlier I just restricted the location to be within the US for homogeneity.
Let's start by talking about money, goals range from less than a hundred USD to hundreds of thousand. Success can be dramatic, few projects 
obtain funds up to tenfold the set goal.

<figure class="half">
    <img width="160" src="/assets/images/kickstarter/scatter_goal.png">
    <figcaption>Required goal (in USD) vs ratio of collected funding over goal. 
    Blue and red correspond to whether a project has been successfull or not.</figcaption>
</figure>
A few other trends are visible in the plot below. The most expensive campaigns are rarely funded. 
There is a threshold effect such that a there are few campaigns failing by a small amount 
(psychological effect in funders? Friends and family of the creator chipping in?).

What about categories?
Some are more popular, with a larger number of campaigns, but that does not correlate with success 
in that category. 

<figure class="half">
    <img width="160" src="/assets/images/kickstarter/cat_donut.png">
    <figcaption>Caption describing these two images.</figcaption>
</figure>


<figure class="half">
    <img width="160" src="/assets/images/kickstarter/funded_ratio.png">
    <figcaption>Caption describing these two images.</figcaption>
</figure>


Finally we can take a look at spacial data. Different states have different preferences and overall engagement.  


<iframe width="720" height="580" frameborder="0" seamless="seamless" scrolling="no" src="https://plot.ly/~roundedup/3.embed?width=640&height=480"></iframe>

This map is very helpful in visualizing data, bear in mind that certain states might have very few campaigns in certain categories and
thus big error bars should be associated with their success percentage. However results are reliable for bigger states...

## Choose your words carefully


<figure>
    <img src="/assets/images/kickstarter/tfidf.png">
    <figcaption>Caption describing these two images.</figcaption>
</figure>




## Predicting success

Back to the opening question, can we predict the success of a campaign?
Encouraged by the tf-idf results I wanted probe the power of title and blurb words. 
Indeed tf-idf already provides us with a vector representation of each campaign ..., which can 
be readily used to train a Boosted Decision Tree. In this case I used the [XGBoost]() ....
I wanted to compare the perfomance of it with other features extracted from the data, for instance:
goal, length of campaign and start day (in the year), length of title and blurb, subcategory, US state, and so on.
I trained separately on the tf-idf and on the features, category by category, after optimizing the hyperparameters with a grid search.
Finally I trained one more model using both at the same time.  
The results are below, where I show the f-1 score obtained on the test (20% of the campaigns in the dataset).

In most categories language is as powerful as the other features. Their combination improves the overall score by a few percents.
Most surprising is the power of this simple method, getting up to f1-score ~0.9. 
Did we discover the secret for success?


Category    |tf-idf|feat. |all
---       | :---: | :---: | :----------------------:
Art         | 0.61 | 0.66 | 0.69
Comics      | 0.85 | 0.86 | 0.88
Crafts      | 0.55 | 0.69 | 0.73
Dance       | 0.86 | 0.88 | 0.9
Design      | 0.81 | 0.86 | 0.87
Fashion     | 0.69 | 0.77 | 0.8
Film & Video| 0.74 | 0.81 | 0.83
Food        | 0.54 | 0.68 | 0.72
Games       | 0.82 | 0.89 | 0.91
Journalism  | 0.31 | 0.29 | 0.34
Music       | 0.82 | 0.83 | 0.85
Photography | 0.66 | 0.79 | 0.82
Publishing  | 0.81 | 0.84 | 0.85
Technology  | 0.47 | 0.57 | 0.61
Theater     | 0.84 | 0.85 | 0.87
All         | 0.74 | 0.79 | 0.81


Next I could try to turn this classification problem into a regression one and predict the pledged amount.
My code as always is on my GitHub [repo](https://github.com/roundedup) if somebody wants to play with it.
