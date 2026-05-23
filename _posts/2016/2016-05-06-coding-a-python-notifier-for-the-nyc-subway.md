---
layout: post
read_time: true
show_date: true
title: "Coding: A Python Notifier for the NYC Subway"
date: 2016-05-06
image: https://upload.wikimedia.org/wikipedia/commons/6/65/NYCT_R142A.jpg
tags: [Life in NYC, Python]
category: Life in NYC
author: Strakul
description: ""
img_show: false
---

![](https://upload.wikimedia.org/wikipedia/commons/6/65/NYCT_R142A.jpg)  
---  
New York City Subway 6 train. Photo by Robert McConnell (Transferred from en.wikipedia to Commons.)  
  
For fun, I created a small application in Python that checks the status of the New York City [subway system](http://www.mta.info/) and sends me an email in the morning and afternoon if there are delays in the specific lines I tend to use to get to/from work. Now, sure, something like this already exists and is offered by [MTA](https://www.mymtaalerts.com/LoginC.aspx?ReturnUrl=%2f), but I wanted to go ahead and write this myself.  
  
Below, I describe how it works in case you want to create something similar. The code is available at [GitHub](https://github.com/dr-rodriguez/MTA-Notifier), should you care to grab it.  
  
**Accessing the Subway Status**  
This is super easy, but will be the thing that changes the most if you happen to live in another city and want to use their subway/metro status. For NYC, the status is displayed in several locations on the [MTA website](http://www.mta.info/). By going to the [developers](http://web.mta.info/developers/) part of their website you can find some information on how to access their API. Accepting their terms and conditions gives you access to a variety of resources you can use. I was most interested in the [service status text](http://web.mta.info/status/serviceStatus.txt), which is updated every minute. This is in a simple XML format that various Python packages can parse. I ended up using ElementTree from **xml.etree.ElementTree**.  
  
The main part of my code is just grabbing this text file, parsing it, examining the status of the subways lines I'm interested in, and, if there is an issue, grabbing the message text. All of this is fairly straightforward and I decided to push myself to write the application as a Python class to practice object-oriented programming. Instantiating the Notifier class sets up some defaults, which include grabbing API keys, and allows me to use various methods to grab the subway status and send emails to myself.  
  
**Using the Mailgun API**  
I wanted my application to notify me via an email or a text message if there was a delay in the lines I tend to take. I had heard about [Mailgun](https://www.mailgun.com/) from a similar project I saw online and decided to try them out. This is an emailing service that can be used to send lots of emails via their RESTful API. As such, it is easy to access and offers a free version that can be tried out. The free version gets you 10,000 email messages per month and a sandbox server that can send out up to 300 email messages per day to authorized addresses (such as your own). These limits are well beyond what this little hobby application generates.  
  
To use Mailgun, I needed my code to use the proper API keys and my email. It's bad practice to have these directly in your code, as these are meant to be private, so in my usual fashion I saved them in an external JSON file that gets read in and parsed. I also added the alternative to get these via system variables as that allows me to set these variables in the Heroku environment without having to supply my JSON file openly.  
  
With all that set and done it's an easy thing to send an email to myself:  

    
    
     url = "https://api.mailgun.net/v3/" + self.mailgun_url + "/messages"  
     from_text = "Mailgun Sandbox "  
     r = requests.post(  
           url,  
           auth=("api", self.mailgun_key),  
           data={"from": from_text,  
              "to": self.my_email,  
              "subject": subject,  
              "html": self.html_to_send})  
    

  
**Alternative with Yagmail**  
After figuring out how to do all this with Mailgun, I came across [Yagmail](https://github.com/kootenpv/yagmail). This is a simple Python package that takes care of all the backend required to send emails with your gmail credentials to whatever address you want. They have a pretty [good tutorial](http://kootenpv.github.io/2016-04-24-yagmail) on their website.  
  
For my application, using Yagmail is even easier than Mailgun:  

    
    
     with yagmail.SMTP(self.my_email, self.my_email_pass) as yag:
         yag.send(to=self.my_email, subject=subject, contents=self.html_to_send.encode('utf-8'))
    

  
If you run into trouble trying to send email with your gmail account (for example if you have two-factor authorization like I do), you may need to set up a special application password for gmail. You can see instructions on how to do that [here](https://support.google.com/accounts/answer/185833).  
  
While my original goal was to have it send both email and text messages, I decided to not pursue the text messages as the subway line status message can be very long. However, just for reference I'll mention I was looking into using [Twilio](https://www.twilio.com/) for my text messaging needs. Not sure if they have a free trial, or how their API works, but I'm sure it's something I could figure out.  
  
**Setting up locally with Mac OS**  
Once I wrote up the various scripts to parse the NYC subway line status and generate the email message I wanted, I started looking into how run programs on a schedule. I know about **cron** , but with a little quick research I found that [launchd](http://launchd.info/) is the recommended way to take care of this in Mac OS X. These are simple XML files with the plist file designation that live in your ~/Library/LaunchAgents directory.  
Here's what part of mine looks like:  

    
    
    
    
    
     Label
     com.mtanotifier
     Program
     /Users/strakul/PycharmProjects/mta_notifier/run_now.sh
     RunAtLoad
     
     StandardErrorPath
     /tmp/com.mtanotifier.err
     StandardOutPath
     /tmp/com.mtanotifier.out
     StartCalendarInterval
     
      
       Hour
       16
       Minute
       0
       Weekday
       5
      
     
     WorkingDirectory
     /Users/strakul/PycharmProjects/mta_notifier/
    
    
    

  
You can see it's a bunch of keys describing things like the label, the program to run, whether or not it should run when loaded, etc. You can see that in this example I have it to run my program (a file called run_now.sh) at 16:00 on the 5th day of the week.  
  
Now, I actually didn't write all that. I used [LaunchControl](http://www.soma-zone.com/LaunchControl/) to set up everything that I needed. This is a nifty and lightweight editor for launchd configuration files and I recommend using it. With the launch daemon in place I can know that my computer will automatically check the subway status at the specified intervals and send me an email notification about the particular lines I wish to track.

  
**Setting up online with Heroku**  
Unfortunately, my computer is not on all the time and I don't have access to a server at work (not to mention it's questionable to run a hobby application at work ;). When it's not on, the daemon will not run and I won't know what the subway status is. Yes, I could check it manually, but that defeats the purpose. For that, I turned to [Heroku](https://www.heroku.com/). This is a cloud service to host applications you create. I've already used it before to host work-in-progress applications that I'm developing for my research group, so it seemed like a natural choice.  
  
While researching Heroku, I came across their Scheduler Add On and their Clock process type. It seems you have to be a paying customer of Heroku to use the Scheduler, which in any case is very limited. Hence, I opted to go straight towards using the clock process type. This is what you define in your Procfile to let Heroku know what to do.  
For example, in my case I have the following simple Procfile:  

    
    
     clock: python clock.py
    

  
Simple, right? Now, [clock.py](https://github.com/dr-rodriguez/MTA-Notifier/blob/master/clock.py) is a python file that uses the [Advanced Python Scheduler](http://apscheduler.readthedocs.io/en/3.0/) package to know when to run its various functions. I recommend you see my GitHub repo to see how I wrote clock.py. There may be better ways to do this, and this might not be scalable to larger or more complex applications, but it runs fine for me.  
  
There is one limitation to this and it's that, unlike the web processes that I've run before, Heroku clock processes need to run continuously. A free Heroku account only allows your application to run for 16 hours a day. So if you wanted to constantly track the subway status (or something else) you would need to pay to allow your app to run continuously. However, I realized that I only need to run this in the morning as in the afternoon I have my computer on and can rely on the launchd daemon. I managed to time it so that my application sleeps in the afternoon, but is active in the morning based on Heroku's free quota.  
  
Note that to run this application I need to provide a number of API keys and/or passwords. It is bad practice to put this directly in the code, so on my machine they are saved in a separate file that is not version controlled and won't appear on GitHub. However, when running on Heroku I needed to provide these keys without relying on git. For that, I used environment variables that one can set with Heroku. If you look closely at [notifier.py](https://github.com/dr-rodriguez/MTA-Notifier/blob/master/notifier.py), which contains the bulk of my application, you'll see that upon initializing the Notifier class, it searches for the JSON file with my keys, but if it doesn't find it, it relies on the environment variables. This allows the same code to run on both my machine and on Heroku.  
  
**Update:** As of June 2016, Heroku updated its service to instead provide 450 free dyno hours per month per _account_ (1000 hours when verified) rather than the previous 16 free hours per day per _application_. Hence, running a clock dyno continuously will use up a significant fraction of your free dyno hours each month limiting what else you can run.  
  
**Summary**  
This was a simple, fun, and useful application to write. It took me a few days working on/off to figure out all the details and debug some of the major bugs (there might still be minor ones), but I have it working in a way that I like. I can think of other uses of automatic email messaging based on accessing APIs and processing the data with simple scripts and programs. Feel free to grab the [code](https://github.com/dr-rodriguez/MTA-Notifier) and try this out for yourself, you'll just have to make a few changes to use your own keys and email. If you want to use this for other cities, you'll have to figure out how to access their subway/metro statuses. 
