---
layout: post
read_time: true
show_date: true
title: "Data Science: Republican & Democratic Conventions"
date: 2016-08-01
image: /assets/img/posts/2016/20160801/convention_clouds.png
tags: [Data Science, Politics, Text Mining, Python]
category: Data Science
author: Strakul
description: ""
img_show: false
---

[![](assets/img/posts/2016/20160801/convention_clouds.png)](assets/img/posts/2016/20160801/convention_clouds_1.png)

  
In the past few weeks, the two major political parties in the United States of America held their national conventions. While I couldn't listen to all the speeches, I followed the news and paid attention to the overall scene. After they were done, I decided to grab the speeches of the major speakers and see if I could find any obvious trends in their word choices, similar to what I did with my Twitter project. In this blog post, I'll discuss what I can see in the data. You can find my data and all my scripts at this [GitHub repo](https://github.com/dr-rodriguez/conventions_2016).  
  
**Getting the Data**  
The first step was gathering all the data. I'm not aware of a website or resource that gathered all of the transcripts or prepared remarks for the speakers. I know of [The American Presidency Project](http://www.presidency.ucsb.edu/index.php) and it sounds like a useful resource, but I ended up just grabbing transcripts from news sources. In some cases, I could only get prepared remarks, which can differ slightly from their actual speeches. These were all saved to individual text files, which I read and processed in Python. In total, I grabbed 9 speeches from the Republican National Convention (RNC) and 10 from the Democratic National Convention (DNC). You can see which ones later on this blog post.  
  
**Word Frequencies**  
By now, you probably know I love word clouds. You can see at the top of this post the most common words used in the DNC and RNC speeches. Perhaps more useful, however, is to see the word counts themselves. For this, I looked only at Hillary Clinton's and Donald Trump's speeches. I tallied up the most frequent words, using stemming to group similar words, and then created the bar chart below. As always, you can click the figures to see a larger version.  
  


[![](assets/img/posts/2016/20160801/top_words_1.png)](assets/img/posts/2016/20160801/top_words.png)

  
These are the top 10 words used by Clinton and top 10 words used by Trump. Because these are not the same, the graph displays the union of them, 15 in total, with them sorted by Clinton's word count. You can see that Hillary Clinton used work (works, working, etc), people, and us most frequently, whereas Donald Trump used america, american, and country most often. Just by those three word choices you can see there is a different focus in these two speeches.  
  
**Speech Sentiments**  
Another thing I did was to pass each speech through my sentiment analysis code, which uses the [NRC Word-Emotion Association Lexicon](http://saifmohammad.com/WebPages/NRC-Emotion-Lexicon.htm). I tallied the total number of words for each emotion and sentiment to produce the figures you see below. First up, let's compare all the DNC and RNC speeches to each other. Because the speeches have different lengths, I normalized each sentiment by dividing by the number of words. For this first figure, I took the average of the sentiments of the individual speeches.  
  


[![](assets/img/posts/2016/20160801/convention_full.png)](assets/img/posts/2016/20160801/convention_full_1.png)

  
At a glance, both parties tended to use words associated with very similar emotions. This may be just the nature of political speeches in general. The most interesting contrast in my opinion, is the _trust_ emotion at the far right. It appears that Republicans used trust-associated words about 10% of the time whereas Democrats were closer to 8%. Bear in mind, however, that sentences like "I trust you" vs "Trust me" both have the same _trust_ value in this analysis. Whether or not Republicans are asking Americans to trust them, or expressing trust in Americans requires a more detailed look into the speeches, which is beyond the scope of this post.  
  
**DNC Sentiments**  
Let's have a look more closely at the individual speeches, starting with the DNC. The figure below breaks down the sentiment for each of the 10 speeches I analyzed. At a glance, pretty similar, but there are a few differences I want to point out.  


[![](assets/img/posts/2016/20160801/dnc_normalized.png)](assets/img/posts/2016/20160801/dnc_normalized_1.png)

  
Let's have a look at _trust_ again on the far right. The two speakers who used the least words for _trust_ where Bernie Sanders and Elizabeth Warren. Interestingly enough, these two also expressed the least _joy_ , the least _positive_ words, and the most _disgust_ words. They also had more _negative_ words and more words associated with _sadness_ , though they weren't unique in that respect.  
  
Looking at the far left, at _anger_ , we see that most speakers hover at around the 4% mark, with the notable exceptions of Bill Clinton and Michelle Obama. Perhaps the spouses of the current and hopeful future president wanted to reign back some of the negativity and create a more uplifting narrative?  
  
**RNC Sentiments**  
Let's switch gears and look at the Republicans now. Below you'll find a sentiment figure for the 9 speeches I analyzed in this case. I can see some more marked differences here than for the Democrats.  


[![](assets/img/posts/2016/20160801/rnc_normalized.png)](assets/img/posts/2016/20160801/rnc_normalized_1.png)

  
If you look at this for a while, you'll see that there are pretty much two separate camps present here. Let's start with _anger_ at the left. Speakers like Chris Chrisie, Donald Trump, Rudy Giuliani, and Ted Cruz all expressed comparable levels of _anger_ , at about 5%. Mike Pence and Ben Carson were a bit lower, but the three lowest are Donald Jr, Ivanka, and Melania Trump. You can see this pattern again in the _disgust,_  _fear_ , and _negative_ emotions. The reverse is the case for _positive_ words: Ben Carson and Ivanka and Melania Trump top the charts with well over 15% of _positive_ words whereas the other, more main-stream speakers use less _positive_ words.  
  
From this, I hypothesize that, whether intentionally or accidentally, two separate messages are being delivered. On one hand, you have the stereotypical, media representation of the Republican that is being addressed by the likes of Donald Trump and gang: fearful, angry, negative. On the other hand, you have Trump's children, and to an extent Ben Carson, who are trying to use more positive rhetoric to perhaps appeal to the more moderate Republicans. This is far more pronounced here than in the DNC (see Bernie/Warren above), which perhaps hints at a more fractured party. It would have been very interesting to see where George and Jeb Bush would fall in this analysis.  
  
**Final Thoughts**  
This is probably the interesting election I have seen and it's exciting that it comes at a time when I have developed the skills and knowledge to look at this in a more data-centric way and with, hopefully, a more objective view. If you'd like to repeat this analysis, or look more closely at the data for a specific speaker, feel free to head over to [GitHub](https://github.com/dr-rodriguez/conventions_2016) to grab my data and all the scripts.  
  
Most importantly, don't forget to cast your vote this November! 
