---
title: Predicting success on Kickstarter
categories:
  - Kickstarter
  - Crowdfunding
  - Classification
#tags:
#  - 
#  - bikes
#  - citibikes
#  - taxi
---


Can the success of crowdfunding campaigns be predicted? 
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
Let's start by talking about money, goals range from less than a hundred USD to almost a hundred million. Success can be dramatic, with campaigns 
reaching tens or hundreds time their initial pledge.

<figure>
    <img width="160" src="/assets/images/kickstarter/scatter_goal.png" style="width: 500px;">
    <figcaption>Required goal (in USD) vs ratio of collected funding over goal. 
    Blue and red correspond to whether a project has been successfull or not.</figcaption>
</figure>
A few other trends are visible in the plot below (I cut the high ratio tail). The most expensive campaigns are rarely funded. 
There is a threshold effect such that a there are few campaigns failing by a small amount 
(psychological effect in funders? Friends and family of the creator chipping in?).

What about categories?
Some are more popular, with a larger number of campaigns, but that does not correlate with success 
in that category. 

<figure class="half">
    <img width="160" src="/assets/images/kickstarter/cat_donut.png">
    <img width="160" src="/assets/images/kickstarter/funded_ratio.png">
    <figcaption> Left: donut chart representing amount of campaign categories in the dataset. 
    Right: chart showing the ratio of success in each category.</figcaption>
</figure>


Finally we can take a look at spacial data. Different states have different preferences and overall engagement.  


<iframe width="720" height="580" frameborder="0" seamless="seamless" scrolling="no" src="https://plot.ly/~roundedup/3.embed?width=640&height=480"></iframe>

This map is very helpful in visualizing data, bear in mind that certain states might have very few campaigns in certain categories and
thus big error bars should be associated with their success percentage. However results are reliable for states like California, New York and other densely populated ones.

## Choose your words carefully

Let us turn our attention to the title and short description, a sort of pitch, of each campaign. 
We can use term frequency–inverse document frequency [(tf-idf)](https://en.wikipedia.org/wiki/Tf-idf), which should highlight how
relevant a word is in set of documents. With this method we can turn each document in a vector of features corresponding to the weights of words
present (or not) in it. I implement it using [scikit-learn](http://scikit-learn.org/stable/modules/feature_extraction.html#tfidf-term-weighting).
For simplicity I consider title and blurb together so that each campaign is characterized only by one text.  
How can we look for words that are more relevant in funded projects versus failed ones? 
One simple thing to do is to divide the collection in two sets, either successful or not, and then compute the average tf-idf representation of the texts in the two sets.
Now take one word and look at its scores in the averages. If it's not correlated with success or failure it will likely have a similar score
in both vectors, on the other hand a word appearing only in one of the two sets will have different values.
An easy way to assign one score only is to take the difference between the two. Positive score will be correlated with success and viceversa.
Now visualization is easy. I choose the top 10 positive and negative terms in each category and stack them obtaining the following.


<figure>
    <img src="/assets/images/kickstarter/tfidf.png">
    <figcaption> Top 10 relevant words for successful campaigns versus top 10 for unfunded ones, in two different categories.</figcaption>
</figure>

The examples are just for two categories. So what can we conclude? People don't like to fund tv and animation,
using "film" is better than "movie" and sequels are not that bad. Food trucks aren't cool anymore, while beer love will never fade.

## Predicting success

Back to the opening question, can we predict the success of a campaign?
Encouraged by the tf-idf results I wanted probe the power of words in the title and blurb. 
Indeed tf-idf already gives us a vector representation of each campaign. These features can be readily used for regression or classification.   
The latter is what we are looking for. To predict whether a campaign will be funded or not we will train a Boosted Decision Tree, 
in this case using the [XGBoost](http://xgboost.readthedocs.io/en/latest/) package.
I wanted to compare the perfomance of tf-idf with other features, for instance:
goal, length of campaign and start day (in the year), number of characters in title and blurb, subcategory, US state, and so on.
I trained using k-fold validation (with 6 folds) category by category, after optimizing the hyperparameters with a grid search.
Training was performed separately on the tf-idf and on the other features, and on both together.
The results in term of [f-1 score](https://en.wikipedia.org/wiki/F1_score) are reported in the table below.

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
