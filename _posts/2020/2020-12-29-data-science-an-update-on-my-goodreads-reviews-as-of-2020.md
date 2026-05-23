---
layout: post
read_time: true
show_date: true
title: "Data Science: An Update on my Goodreads Reviews as of 2020"
date: 2020-12-29
image: /assets/img/posts/2020/20201229/wordcloud.png
tags: [Data Science, Programming, Text Mining, Books]
category: Data Science
author: Strakul
description: ""
img_show: false
---

[![](assets/img/posts/2020/20201229/wordcloud.png)](assets/img/posts/2020/20201229/wordcloud_1.png)

A few years ago, in 2016, I wrote a brief post on statistics when looking at my [Goodreads reviews](https://strakul.blogspot.com/2016/09/data-science-my-goodreads-reviews_13.html). A friend's comment recently reminded me of this work and I decided to update it with information all the way up to the present day. Unfortunately, a big limitation has been that, as of earlier this month, Goodreads discontinued their API. I had a lot of code written to access it and generate the plots and had to spend some time rewriting it to use a CSV export of my library. It's not the same, but I was able to reconstruct most of the information. So without further ado, let's look at what we can discover.

  


  


If you want to generate some of these figures yourself, perhaps using your own Goodreads library, you can use the scripts in this [Github repo](https://github.com/dr-rodriguez/Exploring-Goodreads). Note that I created this a bit before I was used to ensuring reproduceability so some of the scripts may not run properly and the environment is not fully documented. However, it should still give you the gist of what data is available.

  


### My Reading History

An immediate limitation of not using the Goodreads API is that the amount of time I spent reading each book is lost. The CSV returns the date I added the book and the date I marked it as read. In some cases this is indeed the start/end date, but in others it is not, as I could have added a book 4 years earlier as to-read but only got to it recently. Hence, I can't track how fast/slow I've been reading books anymore which is a shame.

  


One of the basic stats, though, is the number of books I've read (and/or added) over the past few years. You can find that plot at right. It was interesting to me that I've been using Goodreads for 10 years so this is quite a bit of data, though my earlier entries are generally me adding books I had read in years prior. I also wasn't writing reviews as much in 2010 and 2011, only really starting with my blog (which kicked off in January of 2012).

  


[![](assets/img/posts/2020/20201229/book_counts.png)](assets/img/posts/2020/20201229/book_counts_1.png)

  


Overall, I tend read about a dozen books pear year. 2020 has been special, but although I've read more I haven't actually reviewed more. Some of the books I've decided against reviewing have been old re-reads.  


  


An important consideration is also how long are the books I read. I'm a fan of epic fantasy and that certainly tends towards longer books. The plot below shows the distribution of the number of pages for books I've read across the years. My average is around 500 pages, though it ranges from short stories of a dozen pages to epics that are 1200 pages long.

  


[![](assets/img/posts/2020/20201229/page_histogram_1.png)](assets/img/posts/2020/20201229/page_histogram.png)

  


Another interesting aspect of this is when do I read. Do I read newly released novels or old classics? Turns out I do a bit of both. I do keep up to date with new releases, but do spend some time reading older books. The ones labeled are those published originally before 1984.

  


[![](assets/img/posts/2020/20201229/years_1.png)](assets/img/posts/2020/20201229/years.png)

  


### My Reviewing Patterns

One thing I do in Goodreads is to rate and review the books I read. Now, I do far more in-depth reviews here on my blog, but I do tend to put at least a paragraph of text in most of my reviews on Goodreads. And I try to rank it 1-5 stars, though in general most of the books I review tend to be in the 4-5 star range. Here's how my ratings shape up against the averages of all users. 

  


[![](assets/img/posts/2020/20201229/rating_comparison.png)](assets/img/posts/2020/20201229/rating_comparison_1.png)

  


Note the x and y-axes scales are different, but for the most part my ratings tend to follow the community's. For clarity in the plot I introduced some jitter; otherwise they would be a series of vertical lines.

  


I was also curious as to what I write in my reviews, so I did a quick analysis of the most frequently used words (eliminating common stop words) and plotted up their frequencies. Nothing too dramatic here, I'm afraid, though the importance of _character_ is quite high. I'll note that the word cloud at the start of this post is also generated from the common terms I use.

  


[![](assets/img/posts/2020/20201229/words_frequency.png)](assets/img/posts/2020/20201229/words_frequency_1.png)

  


### The Authors I Read

And who are the authors that I tend to read and review? That is also an easy plot to generate. However, another limitation of the lack of the Goodreads API is that I can't easily generate the world maps that I used before. So I can't provide more information of where they are in the world, nor other things like their gender distribution or Goodreads popularity. 

  


[![](assets/img/posts/2020/20201229/author_frequency.png)](assets/img/posts/2020/20201229/author_frequency_1.png)

  


I'll note that the figure above is only showing those that I have reviewed at least 3 times. Those I haven't reviewed are not displayed and those with less than 3 reviews are grouped under 'Other Authors'. So while I have a clear bias towards Brandon Sanderson, I actually have nearly 60 other reviews of books where I've only read 1 or 2 books from that author.

  


One thing I decided to explore this time was to examine how I rate some of my most-read authors. This is what is shown below. Some of the bars may look odd given the small number statistics and the limited range of data. I do tend to avoid giving 2-star reviews, but a few books have landed there unfortunately. Remember that these are authors I've reviewed at least 3 times, so low personal ratings don't stop me from going back and reading yet another book.

  


[![](assets/img/posts/2020/20201229/rating_author.png)](assets/img/posts/2020/20201229/rating_author_1.png)

  


It'll be interesting to re-visit this in a few years time to see how things have evolved. With more time I could also generate more of these plots vs time, but I think I'd need to gather a bit of a larger sample for that to be more meaningful. I do hope that Goodreads creates a new public API. The CSV output is very good, but lacks some of the finer detail I would have liked to have access to. 
