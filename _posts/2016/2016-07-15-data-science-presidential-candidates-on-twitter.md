---
layout: post
read_time: true
show_date: true
title: "Data Science: Presidential Candidates on Twitter"
date: 2016-07-15
image: /assets/img/posts/2016/20160715/wordclouds.png
tags: [Data Science, Politics, Text Mining, Python]
category: Data Science
author: Strakul
description: ""
img_show: false
---

![](/assets/img/posts/2016/20160715/wordclouds_1.png)

  
Over the past few months, I've been working on a little hobby data science project to explore twitter data with regards to the upcoming presidential election in the United States. The project has evolved quite a bit and detailing it in full is beyond the scope of a single blog post. As such, I've decided to split it into (at least) 3 posts. This post is the first of the series and will go over the basics of gathering data from Twitter and doing some simple text mining. The second and third posts will discuss more details of the project and show some neat visualizations I've created. I'll release all my code after the third post for any curious coders. For now, let's get started seeing what Hillary Clinton and Donald Trump's Twitter accounts are talking about.  
  
  
**Twitter: the Basics**  
If you haven't heard about Twitter, where have you been? As a quick primer, Twitter is a popular social media service that allows you to communicate your ideas with others. It's limited to 140 characters per message (commonly called a tweet), which makes communications short and to the point. Because so many people are using Twitter and the data is easy to access, it makes it a popular choice for budding data scientists to use as a resource.  
  
**Gathering the Data**  
There are a number of different ways to access Twitter data. A quick google search of twitter and python will reveal several packages you can use to access the Twitter API and start mining data. For my purposes, I wrote my own code using the requests package to interface directly with their [REST API](https://dev.twitter.com/rest/public) and used [pandas](http://pandas.pydata.org/) and [JSON](http://www.json.org/) files to organize the resulting data. While this meant a bit more work reading deeper into the documentation, it was nonetheless rewarding and allowed me to have much finer control as to what I could do. It also helped me understand the limitations of the Twitter API. For example, did you know you can only get 3200 tweets from a user's timeline with the API? You can certainly see all of their posts on the Twitter website, but the public API is limited to only return the most recent ones up to this limit.  
  
With limits in mind and a project envisioned, I started gathering tweets from [@HillaryClinton](https://twitter.com/HillaryClinton) and [@realDonaldTrump](https://twitter.com/realDonaldTrump). I considered storing the data in a SQL or Postgres database to practice using them, but the scope of this project is small enough that JSON files would suffice. I also gathered some data for @BernieSanders, but didn't include it in the analysis. For now, I'd like to explore how the two main candidates for the presidency differ from each other with regards to their tweets. In the interest of reproducibility, I used tweets gathered up to July 13, 2016. This amounted to 2683 tweets from Clinton since December 11, 2015 and 3260 tweets from Trump since November 16, 2015.  
  
**Generating Word Clouds**  
One of the cool things I like to do with Twitter data is to generate word clouds. There are a variety of ways to do this, but I like to use this [wordcloud](http://amueller.github.io/word_cloud/index.html) package for python. It allows me to create a cloud and supply an image to be used as a mask and/or for coloring words. In this case, I supplied an image of the Democratic and Republican logos with a single color corresponding to the party color. I considered the top 200 words and generated the image at the top of this post.  
  
Both candidates clearly talk about each other, as evidenced by the large Clinton and Trump words. Trump's word cloud is notable for it's catch phrases: Make America Great Again and his tendency to refer to Hillary as Crooked Clinton. Just like Trump, Clinton mentions America plenty of times (pretty much a requirement in US politics); however, she doesn't refer to her Democratic opponent quite as much (compare with Trump's references to Rubio, Cruz, and Kasich). Given the large timespan covered, no major trends are immediately obvious other than for catch-phrases.  
**  
** **Running a Sentiment Analysis**  
An alternative way to examine text data is to pass it through a Sentiment Analysis. The idea behind this is that words can be associated to feelings or emotions and you might be able to get some additional information if you consider this. For this purpose, I used the [NRC Word-Emotion Association Lexicon](http://saifmohammad.com/WebPages/NRC-Emotion-Lexicon.htm). This is a set of over 14,000 words that have been associated with 8 emotions and 2 sentiments by crowdsourcing. I had used it before in R, so I wrote my own set of Python scripts to read this lexicon and process my Twitter data through it.  
  


![](/assets/img/posts/2016/20160715/sentiment_normalized_1.png)

  
**  
** Above you can see the normalized tallies for how often a word associated for each emotion, or for a positive or negative word, is used per tweet. I choose to normalize as I have more tweets from Trump than Clinton.  
  
At a glance, the two candidates are very similar in the sentiments behind their tweets, but there are a few notable differences. Clinton tends to use more positive words per tweet than Trump; in fact, her ratio is above one suggesting more than one positive word per tweet on average. She also tends to use more words associated with fear than Trump. On the other hand, Trump tends to use more words associated with disgust. Perhaps this reflects the candidates individual approaches to get the vote out?  
  
Below are some example tweets of very positive and very negative tweets, as well as the highest fear tweet from Hillary and the highest disgust tweet from Trump. In my opinion, even this small a sample of tweets reveals differences between the two candidates.  
  
Top positive tweets:  


> Hard-working Americans deserve a president with both the ideas and the know-how to create good jobs with rising incomes here in the U.S.
> 
> — Hillary Clinton (@HillaryClinton) [March 16, 2016](https://twitter.com/HillaryClinton/status/709908737650204677)

> "[@LiliannyLeebou](https://twitter.com/LiliannyLeebou): I think the first female president of the USA will be [@IvankaTrump](https://twitter.com/IvankaTrump) a beautiful intelligent young genuine successful lady!"
> 
> — Donald J. Trump (@realDonaldTrump) [June 3, 2016](https://twitter.com/realDonaldTrump/status/738601935771570180)

  
Top negative tweets:  


> The ugly, divisive rhetoric we're hearing from Donald Trump—the encouragement of violence and aggression—is not only wrong, it’s dangerous.
> 
> — Hillary Clinton (@HillaryClinton) [March 12, 2016](https://twitter.com/HillaryClinton/status/708709898461249537)

> Man shot inside Paris police station. Just announced that terror threat is at highest level. Germany is a total mess-big crime. GET SMART!
> 
> — Donald J. Trump (@realDonaldTrump) [January 7, 2016](https://twitter.com/realDonaldTrump/status/685089631973601280)

  
Top fear tweet:  


> Gun violence prevention laws might not stop every shooting or terrorist attack—but they will save lives. [pic.twitter.com/raNqMJ4ez8](https://t.co/raNqMJ4ez8)
> 
> — Hillary Clinton (@HillaryClinton) [June 14, 2016](https://twitter.com/HillaryClinton/status/742734905306681344)

  
Top disgust tweet:  


> N.Y.C. has the worst Mayor in the United States. I hate watching what is happening with the dirty streets, the homeless and crime! Disgrace
> 
> — Donald J. Trump (@realDonaldTrump) [December 7, 2015](https://twitter.com/realDonaldTrump/status/673865641892421632)

  
**  
** **Future Work**  
One thing I did not do was save details of tweets such as the number of retweets or favorites. It would be interesting to explore if there are any trends between word choice or sentiment and popularity of a tweet. It may be that there is no trend, or that it's more complex than just these few parameters, but it may be worthwhile to consider at some future point. At the moment, I've made a mental note to revisit this, but for now this is all this blog post is concerned with.  
  
Next time, we'll talk about how I applied a Principle Component Analysis to further explore this dataset. 
