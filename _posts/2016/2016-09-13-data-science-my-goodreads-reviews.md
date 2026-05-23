---
layout: post
read_time: true
show_date: true
title: "Data Science: My Goodreads Reviews"
date: 2016-09-13
image: /assets/img/posts/2016/20160913/wordcloud.png
tags: [Data Science, Books, Python]
category: Data Science
author: Strakul
description: ""
img_show: false
---

[![](assets/img/posts/2016/20160913/wordcloud.png)](assets/img/posts/2016/20160913/wordcloud_1.png)

Followers of my blog will know that I read and review quite a few of books throughout the year. I track the books I read and those I want to read on [Goodreads](http://www.goodreads.com/) and recently came across their API. I decided to figure out how to access it and see what sort of information I could glean from my Goodreads reading history. This particular post explores trends in my reading and reviewing habits, as well as looking at what authors I've read. Next week’s post will discuss my attempt to create a model to predict the reviews I give a particular book. With that model in hand, I can decide what books to read based on my own interests.  
  
Let’s jump right in.  
  
  
**Accessing the Data**  
  
The [Goodreads API](https://www.goodreads.com/api?rel=nofollow) is fairly standard with lots of HTTP GET/POST methods available. It does require authentication, so you’ll have to register an application with Goodreads if you want to repeat some of what I’ve done here. I’ve accessed similar APIs in the past (see for example, my analysis of [my own blog in R](http://strakul.blogspot.com/2015/08/data-science-my-blog-with-r.html), or [twitter data in Python](http://strakul.blogspot.com/2016/07/data-science-presidential-candidates-on.html)), so rather than writing my own code I turned to existing Python code. Namely, I used [sefakilic’s](https://github.com/sefakilic) [Python wrapper for the Goodreads API](https://github.com/sefakilic/goodreads). This has all the basic functionality I needed to access and parse data from the API. Like many public APIs, Goodreads imposes limits on its users. For example, one can call a method only once per second and you can’t harvest the huge amount of reviews available on Goodreads without their explicit consent. For our purposes, this is fine but be sure to read the Terms of Service if you want to develop something more ambitious.  
  
  
**My Reading History**  
  
I joined Goodreads towards the end of 2010 and added a bunch of books that I had read in the past. I also started writing reviews for those I had recently finished. Using the API, I gathered just the ones where I had written a review: 118 books over the past 6 years. This is the dataset I use for all subsequent figures and analyses. I gathered the data on September 10, 2016, so any reviews or books added afterwards do not feature in these posts.  
  
There’s a lot of information you can gather for each review, including the rating, the review text, the date you started reading the book, the date you say you finished the book, as well as properties for the book itself including authors, title, average rating, number of ratings, number of pages, publication year, etc etc. Some reviews do lack some information, however. For example, one review may lack a date when I ended the book or not list page numbers or a publication date. Regardless, most of the information is present and can be used. Entries with missing values can either be removed or imputed.  
  
One cool thing I can do is to compare the date read versus the date started values to figure out how long it takes me to read a book. Below you can find a figure where I’ve compared the length of a book, by its reported number of pages, and the number of days it took to complete the book. Remember that you can click these figures to see larger versions.  
  


[![](assets/img/posts/2016/20160913/reading_rate_1.png)](assets/img/posts/2016/20160913/reading_rate.png)

  
There are a few suspicious points with large page numbers and 0 days to read. These include [Storm Front](http://strakul.blogspot.com/2014/06/book-review-storm-front-by-jim-butcher.html), [Firefight](http://strakul.blogspot.com/2015/01/book-review-firefight-by-brandon.html), [The Wandering Earth](http://strakul.blogspot.com/2014/12/short-story-review-wandering-earth-by.html), and [The Language of Flowers](http://strakul.blogspot.com/2014/06/book-review-language-of-flowers-by.html). Some of those I did indeed read in a single sitting, either because they are short or I was on a long flight, others may have been erroneously set. The figure also shows several outliers with very long read times. These are cases where other things were going on in my life and took time away from reading. In fact, if you remove [The Crippled God](https://www.goodreads.com/review/show/1455474930?book_show_action=false&from_review_page=1), [Dust of Dreams](https://www.goodreads.com/review/show/783728177?book_show_action=false&from_review_page=1), and [The Lord of the Rings](https://www.goodreads.com/review/show/133362085?book_show_action=false&from_review_page=1), you would notice that most reading times tend to lie in the 15-30 day mark regardless of length. The best-fit regression line (with 95% confidence intervals) is shown and clearly is affected by these three 800+ page outliers.  
  
Another way to examine this data is to divide out the number of pages by the amount of time I took to read them. This number of pages per day is a useful metric to see how fast I can read. In the figure below you can see how this has changed over the years I’ve been on Goodreads. The numbers below each point indicate the number of books used for that point and the number of books I read that year. As mentioned earlier, not every review has full metadata so sometimes I’m missing the number of pages or the date I finished a book.  
  


[![](assets/img/posts/2016/20160913/reading_rate_2.png)](assets/img/posts/2016/20160913/reading_rate_2_1.png)

  
It’s very clear that my reading rate has slowed down even with small number statistics. This has been a combination of work and other interests competing with time. This year has been particularly hard and I certainly feel like I’m behind schedule.  
  
  
**My Reviewing Patterns**  
  
Beyond tracking which books I’ve read and which ones I want to read, I use Goodreads to rate and review books. As mentioned above, all this analysis is being carried out on only those books that I’ve reviewed. First of, are my reviews comparable to those that others have made?  
  


[![](assets/img/posts/2016/20160913/rating_comparison.png)](assets/img/posts/2016/20160913/rating_comparison_1.png)

  
The above figure demonstrates that my reviews are indeed similar to the averages. Note that Goodreads reviews are discrete: 5-star, 4-star, etc; there are no fractional stars. I’ve added some random jitter to my reviews so the plot looks nice, but the fit is performed on the actual (un-jittered) data. This result is perhaps not too surprising or instructive as my review is part of the average. So instead, let’s have a look at the sentiments expressed in my reviews. As I’ve described [elsewhere](http://strakul.blogspot.com/2016/08/data-science-republican-democratic_1.html), I like to use the [NRC Word Emotion Association Lexicon](http://saifmohammad.com/WebPages/NRC-Emotion-Lexicon.htm) to quantify the sentiments expressed in text. Below you can find a figure comparing the positivity, which I define as the difference between the number of positive words and negative words used in a review:  
  


[![](assets/img/posts/2016/20160913/sentiment_3.png)](assets/img/posts/2016/20160913/sentiment_3_1.png)

  
In the figure above, positivity has been jittered as it corresponds to number of words and there are no fractional words. With that in mind, I can’t see a clear trend between my use of words and the average user ratings. There are three notable outliers where I’ve used very positive language to review them: [Leviathan Wakes](https://www.goodreads.com/review/show/174387294?book_show_action=false&from_review_page=1) by James S.A. Corey, [Gardens of the Moon](https://www.goodreads.com/review/show/238483985?book_show_action=false&from_review_page=1) by Steven Erikson, and [Bridge of Birds](http://strakul.blogspot.com/2014/04/book-review-bridge-of-birds-by-barry.html) by Barry Hughart. I certainly enjoyed all three books and rated them 5-stars. While there isn’t an obvious trend, keep the positivity in mind as I’ll use it again next week.  
**  
** **  
** **The Authors I Read**  
  
One final aspect I wanted to look at was at what authors I’ve reviewed. From the data, I can see that I’ve reviewed a total of 59 authors. In many cases, I’ve only read a single book by that author:  
  


[![](assets/img/posts/2016/20160913/author_frequency_1.png)](assets/img/posts/2016/20160913/author_frequency.png)

  
From the Goodreads API, I can extract the hometown of the author and then use the [Google Maps API](https://developers.google.com/maps/) to resolve the text to latitude and longitude. Again, not every author lists a hometown, but for those that do, I can create maps to see where in the world these authors are from.  
Let’s start with the United States:  


[![](assets/img/posts/2016/20160913/usmap_1.png)](assets/img/posts/2016/20160913/usmap.png)

  
Plenty of author’s I’ve reviewed come from the US. In fact, there’s actually a bit of overlap so some of the green circles include more than one author. I’ve reviewed a few Canadian authors as well, notably Guy Gavriel Kay, Karl Schroeder, and Steven Erikson.  
Since I read primarily in English, I also expect some authors from the United Kingdom, so let’s look at Europe:  


[![](assets/img/posts/2016/20160913/europemap_1.png)](assets/img/posts/2016/20160913/europemap.png)

  
As we can see above, I’ve reviewed a number of authors from England and one from Scotland. Beyond the UK, we can spot [Carlos Ruiz Zafón](http://strakul.blogspot.com/2013/07/book-review-shadow-of-wind-by-carlos.html) in Barcelona, Spain; [André Gide](https://www.goodreads.com/review/show/1200594598?book_show_action=false&from_review_page=1) in Paris, France; and [Mikhael Bulgakov](http://strakul.blogspot.com/2014/06/book-review-master-and-margarita-by.html) in Kiev, (present-day) Ukraine, all of which I read thanks to my book club in Chile. I read [René Barjavel](http://strakul.blogspot.com/2012/06/book-review-ice-people-la-nuit-des.html) of Nyons, France at the recommendation of a colleague at work.  
  
Finally, let’s have a look at the whole world:  


[![](assets/img/posts/2016/20160913/fullmap.png)](assets/img/posts/2016/20160913/fullmap_1.png)

  
Because of my prevalence in reviewing English-language books, I don’t have quite as many in the rest of the world. Notable among them are book-club authors like [Ayaan Hirsi Ali](http://strakul.blogspot.com/2014/10/book-review-infidel-by-ayaan-hirsi-ali.html) of Somalia, [Mario Vargas Llosa](http://strakul.blogspot.com/2014/03/book-review-aunt-julia-and-scriptwriter.html) of Peru, and [Christos Tsiolkas](http://strakul.blogspot.com/2013/09/book-review-slap-by-christos-tsiolkas.html) of Australia. Liu Cixin is one of my favorite authors thanks to his [Three Body Problem](http://strakul.blogspot.com/2015/07/book-review-three-body-problem-by-liu.html) series and [Fuyumi Ono](https://www.goodreads.com/review/show/133694862?book_show_action=false&from_review_page=1) is the author of The Twelve Kingdoms, which has been turned into an anime. I managed to borrow an English translation of one of the novels when I lived in Los Angeles. Note that while [J.R.R. Tolkien](https://en.wikipedia.org/wiki/J._R._R._Tolkien) lived and worked in the UK, he was born in present-day South Africa.  
  
This concludes this rather long, image-heavy post. Tune in next week to learn how I attempt to predict which books I will like. 
