---
layout: post
read_time: true
show_date: true
title: "Data Science: Principal Component Analysis of Twitter Data"
date: 2016-07-22
image: /assets/img/posts/2016/20160722/biplot_0_3_1.png
tags: [Data Science, Politics, Text Mining, Python]
category: Data Science
author: Strakul
description: ""
img_show: false
---

![](/assets/img/posts/2016/20160722/biplot_0_3.png)

  
As described on my [last blog post](http://strakul.blogspot.com/2016/07/data-science-presidential-candidates-on.html) on this topic, I've been tracking tweets from the US presidential candidates, Hillary Clinton and Donald Trump. I've looked at the top words they used and the sentiments expressed in their tweets given their word choice. However, some words are used with others almost all the time, a notable example being a slogan like Make America Great Again. As such, it may be beneficial to look at groups of words rather than individual words. For that, I took an approach applying a Principal Component Analysis. Below I describe what this is, how I used it, and what it reveals. Do note, however, that I'm applying things I learned in astronomy to this problem rather than taking courses specific to text mining. It may be that there are better tools out there than what I've used.  
  
**The PCA**  
The [Principal Component Analysis](http://setosa.io/ev/principal-component-analysis/), or PCA, is a tool generally used to identify patterns and to reduce the number of variables you have to consider in your analysis. For example, if you have data with 200 columns, it may be that a significant amount of the variance in your data can be explained by just 100 principal components. In the PCA, the first component is chosen in such a way that has the largest variance, subsequent components are orthogonal and continue covering as much variance as possible. In this way, the PCA samples as much of the variability in the data set with the first few components. Mathematically, each component is a linear combination of all the input parameters times coefficients specific for that component. These coefficients, or loading factors, are constrained such that the sum of the squares of them are equal to 1. As such, the loading factors serve as weights describing how strongly certain parameters contribute to the specific principal component. Parameters with large values of positive or negative loading factors are correlated with each other, which can serve to identify trends in your data.  
  
**PCA on Text Data**  
For this project, I tabulated the most frequent words across tweets by [@HillaryClinton](https://twitter.com/HillaryClinton) and [@realDonaldTrump](https://twitter.com/realDonaldTrump). I made use of the [NLTK](http://www.nltk.org/) (Natural Language Toolkit) package for my language processing needs. I used [stemming](https://en.wikipedia.org/wiki/Stemming) to deal with inflected words, such as plurals, and removed stop words like common English words (the, and, it, etc) and certain political terms (the candidates names, for example). For purposes of this post, I considered the top 50 words, but in my full analysis I used a larger number of words. I then created a document-term-matrix from the tweets and the top words. That is, if I have 5000 tweets, I created a matrix with 5000 rows and 50 columns where each column corresponds to a particular word and each row to a particular tweet. The values of the matrix are how often the words appear in a tweet. This is, as you can imagine, a sparse matrix as most elements will be zero (it’s unlikely that a single tweet will have a significant number of the top words). There are ways to deal with sparse matrices in Python, but I left it as a list of dictionaries for ease and since my machine had no trouble with this.  
  
**PCA Results**  
Passing the document-term-matrix to [scikit-learn’s PCA method](http://scikit-learn.org/stable/modules/decomposition.html#pca) allowed me to obtain the components and loading factors for my dataset. I opted to use as many components as needed to account for 80% of the variance in the original dataset. With 50 words, I only needed 33 principal components. There are a variety of ways to examine these results and I present two of them below.  
  


![](/assets/img/posts/2016/20160722/biplot_3_4_1.png)

  
In this plot, I examine two principal components against each other in a biplot. This is a scatter plot of the values of the components, but with arrows indicating some of the prominent terms as indicated by their loading factors. The values of the loading factors are used to determine the length and direction of these arrows and as such they serve as a way of expressing direction. That is, tweets which use these terms will be moved along the length of those arrows. There are, in practice, 50 arrows I could draw, but I only chose to draw the most important parameters.  
  
This is the same as the figure at the top of this post, though for a different combination of principal components. Note that the words are stemmed, that is, the ending of words are trimmed to clean up plurals and similar. This is why peopl is lacking an e. In the figures, I also included the distribution on the top and the right for the second figure. While these clouds of tweets can look similar, there are some differences in their spread when you consider tweets by [@HillaryClinton](https://twitter.com/HillaryClinton) vs [@realDonaldTrump](https://twitter.com/realDonaldTrump).  
  
Another way to examine the PCA results is to look at the loading factors themselves. Below, I have a [Bokeh](http://bokeh.pydata.org/en/latest/) grid plot which shows the various principal component along the x-axis and the individual words along the y-axes. Each grid box is color-coded based on the sign of the loading factor and how large the square of that value is. You can hover over each box to the see the loading factor value for that word-component pair. Looking at it vertically, you can see which words constitute your principal components. Looking at it horizontally, you can see how individual terms are shared between components. To view this on its own page, [click here](http://dr-rodriguez.github.io/files/pca_factors.html).  
  
  
  
I like to interpret these results by saying that the PCA has grouped words together. For example, the words make, america, great, and again are all prominent terms in the first principal component. Instead of dealing with 4 terms we can just deal with a single term that is a combination of all four. That is, the PCA has naturally grouped words in rough phrases and instead of considering a tweet as 140 characters or as a dozen or so words, we can consider them as being located in a space defined by specific phrases.  
  
If for some reason the grid plot above is not working, here is another version generated with [Seaborn](http://stanford.edu/~mwaskom/software/seaborn/index.html). As with all figures on this blog, you can click it to see a larger version.  


![](/assets/img/posts/2016/20160722/loadings_1.png)

  
  
As an example, here are a few tweets with very large (negative) values of principal component one, whose highest ranked terms include make, america, great, and again. The more times those set of words are used, the greater the value for this principal component.  
  


> "We don’t need to make America great again. America never stopped being great. But we do need to make America whole again." —Hillary in SC
> 
> — Hillary Clinton (@HillaryClinton) [February 28, 2016](https://twitter.com/HillaryClinton/status/703744706719703041)

  


> "[@lee_richter](https://twitter.com/lee_richter): TRUMP will make America SAFE again...He will make America LEGAL again...He will make America GREAT again! [#Trump2016](https://twitter.com/hashtag/Trump2016?src=hash) USA"
> 
> — Donald J. Trump (@realDonaldTrump) [November 30, 2015](https://twitter.com/realDonaldTrump/status/671140719629848576)

  
  
**Future Work**  
My hypothesis is that by breaking up tweets in terms of sentiments and placing them in this principal component “phrase-space”, I can create a model that will be able to distinguish if a tweet came from [@HillaryClinton](https://twitter.com/HillaryClinton) or [@realDonaldTrump](https://twitter.com/realDonaldTrump). Next blog post, I’ll describe how I do this and how accurate the model ends up being. And then we'll push that to the limit. 
