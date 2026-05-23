---
layout: post
read_time: true
show_date: true
title: "Data Science: The Divided States of America"
date: 2016-07-29
image: /assets/img/posts/2016/20160729/map_twitter.png
tags: [Data Science, Politics, Text Mining, Python]
category: Data Science
author: Strakul
description: ""
img_show: false
---

![](/assets/img/posts/2016/20160729/map_twitter_1.png)

  
  
In the prior [two](http://strakul.blogspot.com/2016/07/data-science-presidential-candidates-on.html) [posts](http://strakul.blogspot.com/2016/07/data-science-principal-component.html), I have described how I gathered twitter data from [@HillaryClinton](https://twitter.com/HillaryClinton) and [@realDonaldTrump](https://twitter.com/realDonaldTrump), how I ran a sentiment analysis on the individual tweets, and how I performed a principal component analysis on the most commonly used words. Today, I’ll tie everything together and describe how I created a model to predict whether a given tweet belongs to either of the two candidates.  
  
**Support Vector Machine Classifier**  
While attending the [Essentials of Data Science Bootcamp](http://strakul.blogspot.com/2016/04/data-science-essentials-of-data-science.html), we were briefly introduced to a variety of models capable of classifying data among several groups. We briefly mentioned Support Vector Machines, or SVM, but never applied it; however, after reading through what they are I grew curious and decided to use this in my analysis. What SVM does is attempt to divide up the data among your classifiers. In the simplest example, it will find the line that fits between the distribution of your two-dimensional data (see figure below). In general, it does not have to be a straight line, you can have more than two classifications, and the data can contain any number of dimensions. The [Introduction to Statistical Learning](http://www-bcf.usc.edu/~gareth/ISL/) book by Gareth James, Daniela Witten, Trevor Hastie and Robert Tibshirani has a good introduction to SVM.  
  
![](http://scikit-learn.org/stable/_images/sphx_glr_plot_separating_hyperplane_0011.png)  
---  
Example of using SVM to split two types of data points. Credit:[ scikit-learn](http://scikit-learn.org/stable/modules/svm.html#svm-mathematical-formulation)  
  
I used scikit-learn’s [SVC](http://scikit-learn.org/stable/modules/generated/sklearn.svm.SVC.html#sklearn.svm.SVC) class to create and apply this model to my dataset. As is standard, I split my data into training and test datasets and applied a 5-fold cross-validation scheme to tune the parameters of the model. This was the most time-consuming step in the process (about 4 and a half minutes on my machine). For simplicity, I only considered an [RBF kernel](http://scikit-learn.org/stable/auto_examples/svm/plot_rbf_parameters.html#example-svm-plot-rbf-parameters-py) and a 5 possible values for the penalty term and gamma. My data consisted of the results from the sentiment analysis and the principal component analysis of the most common 200 words of 5943 (80% of which are used for training). I used scikit-learn’s [joblib](http://scikit-learn.org/stable/modules/model_persistence.html) to save the model to a file so I can use it elsewhere without having to re-run it.  
  
**Results: Confusion Matrix**  
Given that my data is multi-dimensional (123 columns), I can’t create a figure like the one above. However, I can use a [confusion matrix](https://en.wikipedia.org/wiki/Confusion_matrix) to see how accurately I predict values from my test dataset. You can see this below.  


![](/assets/img/posts/2016/20160729/confusion_matrix.png)

  
  
My test dataset consists of 1189 tweets divided roughly equally between the two candidates. My model correctly classified 460/547 as @HillaryClinton and 514/642 as @realDonaldTrump. This is about an 80% accuracy rate for both candidates. Not bad! Potential ways to improve this would be to use more sophisticated models (perhaps a neural network?) or perhaps considering each word rather than a PCA on the top terms. As it stands, I was satisfied with this accuracy so I moved on to do something crazy.  
  
**Pushing the Limits: Twitter Search**  
It’s great to be able to classify a tweet as belonging to either candidate, but wouldn’t it be awesome if you could do the same for tweets NOT belonging to the two candidates? Well, I set out to do just that. I set up a program to run every day on my machine and grab tweets mentioning specific terms (_politic_ OR _trump_ OR _hillary_ OR _clinton_ OR _election_ OR _politics_). I processed these tweets deleting any where I was unable to determine their location or whose location was outside the contiguous 48 states of the United States. Most users do not include geolocation information on their tweets, so in their absence I used the[ Google Maps API](https://developers.google.com/maps/) to resolve their stated location in their profile to a set of latitude and longitudes. In the end, I had 14952 tweets to consider (between May 28, 2016 and July 13, 2016) and passed them through my model. Note that I did not use tweets during either the Republican or Democratic conventions.  
  
**CAUTION** : It is very important to note that this is not the same type of data that I used for the model. That is, the model is attempting to predict who wrote the specific tweet, **NOT** whether they support one candidate or another. As such, there are funny examples of tweets that clearly support one candidate, but have been classified as belonging to the opposing one. So bear that in mind as you examine the results below.  
  
First, let’s have a look at some of the results. The first 5 entries in the table below were matched to Clinton, while the last 5 were matched to Trump.  
  


Twitter Text Classification  
---  
And #Trump is a Loser! https://t.co/hukzFSLi5g  
@CNN Unlike Hillary, Trump obeys the law  
@Kia_Mak It’s a telling indictment of Donald Trump, really.  
When 10,000 DIE BY #ISIS AND MASS CASUALTY DOCTORS RESPOND then you'll get it. This is a WAR! DONALD TRUMP @realDonaldTrump @DrKellyVictory  
Read this, everyone.... https://t.co/QMsrTeeQgP  
@townlecat @aheffne @Elewashington Hillbots will elect Trump. Bernie never attacked Clinton. The GOP will destroy her & she'll lose big.  
I just signed @NARAL's open letter taking a stand with #MenForChoice against Donald Trump! Sign here: https://t.co/2Ewve336vT  
Hillary "Flip Flop" Clinton VS Bernie "The Rock" Sanders  
  
#NationalFlipFlopDay https://t.co/GfPndlKDQt  
In This Digital Age Of Mass Media The World Is Watching The Gross Humiliating Display Of Election Garbage Between Trump & Hillary. Dignity?  
Hillary to Lose the FBI Primary And Leavenworth Caucus - American Thinker - #PJNET 999 - https://t.co/kCt2C37zVm https://t.co/5XiXMmvtAZ  
  
  
As you can see, the model does not accurately predict whether a tweet supports a candidate. This is a random sampling, so it’s possible the rest of the tweets are better, but that’s not the point. The point is to show that just because a twitter user **talks** like one candidate (based on my model), it does **not imply support** for that candidate. With this caveat in mind, we can now proceed to place these twitter users on a map of the United States.  
  
  
  
Above is a [Bokeh](http://bokeh.pydata.org/en/latest/) interactive figure of the United States with individual tweets (hover to see the text and prediction). To see a larger version of the plot on its own page, [click here](http://dr-rodriguez.github.io/files/US_map_state.html). To create the above, I first group each tweet by user, taking note of the most common prediction for the user. I then [reverse geocode](https://github.com/thampiman/reverse-geocoder) to find the state each user belongs to and group by state tallying up the predictions. Each state is color-coded by whether my model predicts more Clinton (blue) or Trump (red) classifications for those users. A lighter shade of blue/red is used whenever the difference between Clinton and Trump is less than or equal to the Poisson noise. That is, lighter shades are used when the difference is less than the square-root of the total number of twitter users I counted for that state.  
  
This map is the figure that’s displayed at the very top of this blog post. Here is a cleaner version without the Twitter data superimposed:  


![](/assets/img/posts/2016/20160729/map.png)

  
  
If you have a look at Nate Silver’s [FiveThirtyEight website](http://projects.fivethirtyeight.com/2016-election-forecast/), you’ll see the forecast for the 2016 election, which naturally looks very different than mine above. [FiveThirtyEight’s](http://fivethirtyeight.com/features/a-users-guide-to-fivethirtyeights-2016-general-election-forecast/) map is built poll data and models predicting who will win the election. It’s been accurate before and I expect it will be just as good this election cycle. It has the advantage that it’s using real poll data rather than inferring political opinions based on random people’s 140-character messages. It’s fun to compare to my own map, but it is not surprising that they look quite different. Again, though, I’d like to stress that what my model tests is how **similar** a person tweets to @HillaryClinton and @realDonaldTrump, **NOT** whether or not they would vote for them.  
  
**Final Thoughts**  
Working with Twitter data is an amazing and rewarding experience. There are certainly pitfalls, kinks, and limits to what you can do, but when you get it working it’s really cool to see the results flowing in. It’s impressive to think that in this day and age we can tap people’s opinions and speech patterns across the world with just a few simple lines of code. This project was inspired by my participation in the [Essentials of Data Science Bootcamp](http://strakul.blogspot.com/2016/04/data-science-essentials-of-data-science.html) and from discussions afterwards. It took a while to write/rewrite (some original parts were in R) while balancing my actual job, but it was very fun to work on. I still have some ideas of where I can take this so you may see a few bonus posts in the future. In the meantime, my code and data is all on [GitHub](https://github.com/dr-rodriguez/The-Divided-States-of-America) for all to access.  
  
This election is certainly an interesting one with the choice of candidates and what’s going on all over the world. Now, more than ever, your vote matters. Come November, be sure to cast your vote! 
