---
layout: post
read_time: true
show_date: true
title: "Bokeh Plots in Blogger"
date: 2016-06-17
image: http://bokeh.pydata.org/en/latest/_static/images/logo.png
tags: [Python]
category: Data Science
author: Strakul
description: ""
img_show: false
---

![](http://bokeh.pydata.org/en/latest/_static/images/logo.png)

  
This is a quick post to test if I can add [Bokeh](http://bokeh.pydata.org/en/latest/) plots in my blog.  
  
  
  
Here is my plot.  
  


  
The formatting is a bit off, but it works!  
  
The way I did this was to paste the HTML and Javascript directly in my post as well as set the Options on the right to **Interpret typed HTML**.  
That is, I used **script, div = components(plot)** to generate what I needed to include and at the top of my post added some text to load BokehJS from CDN. See [their website](http://bokeh.pydata.org/en/0.11.0/docs/user_guide/embed.html) for instructions.  
  
Here is a second attempt, but using an iframe and hosting the HTML file elsewhere:  
  
  
This is a lot easier to manage so I'll probably stick to using iframes.  
  
For those that don't know, Bokeh is a plotting library in Python (and I think in a few other coding languages as well) that allows for interactive plots in websites. I'll probably be editing this post as I fix things, but for now I wanted to test if this works in Blogger so I can prepare more interesting posts in the future. I may also test using iframes as the scripts to paste can be rather large, even for a simple plot like this one. 
