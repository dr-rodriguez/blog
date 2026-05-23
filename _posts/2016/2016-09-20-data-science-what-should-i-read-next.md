---
layout: post
read_time: true
show_date: true
title: "Data Science: What Should I Read Next?"
date: 2016-09-20
image: /assets/img/posts/2016/20160920/DT_zoom_border_1.png
tags: [Data Science, Books, Python]
category: Data Science
author: Strakul
description: ""
img_show: false
---

![](/assets/img/posts/2016/20160920/DT_zoom_border.png)

  
As I wrote about [last week](http://strakul.blogspot.com/2016/09/data-science-my-goodreads-reviews_13.html), I’ve spent a bit of time looking over my reviews on [Goodreads](https://www.goodreads.com/) to explore trends in what authors I read, how fast I read, and how I review books. In today’s post, we’ll tackle something a little more ambitious: given the data I can readily access from the [Goodreads API](https://www.goodreads.com/api?rel=nofollow), can I predict how I will rate books I haven’t yet read?  
  
Let’s dive right in.  
  
**The Data**  
First of all, what kind of information do we actually have? (For details on how I gathered the data, I’ll point you to [last week’s post](http://strakul.blogspot.com/2016/09/data-science-my-goodreads-reviews_13.html).) I constrained myself to use only basic information that I could gather from Goodreads. I looped through my reviews to gather my ratings and text and book details like first author, average rating, number of ratings, year of publication, and number of pages. For each unique author, I grabbed their gender, number of published works, and number of fans. There was missing information in a lot of places and my initial attempts removed these entries (resulting in 92 reviews); however, I opted later on to impute missing values with the global averages. This allows me to use all 118 reviews even if some had missing information.  
  
For gender information, I used 0 as males or missing values and 1 for females. For the author names, I opted to create a dummy variable that would be 0 if I have read the author multiple times or 1 if this is the first time I have read the author. I thought of having dummy variables for each author, but I have too many authors I have read only once (what I denote as Single-Read Authors) and splitting the others would produce far too many features for the limited amount of data I had in hand. In the end, I had 118 data points and 8 features to consider.  
  
**Decision Trees**  
For this little project I wanted to use decision trees. I had learned about them by participating in the Essentials of Data Science bootcamp and wanted to give them another try, this time in Python. Decision trees are a kind of model that attempts to predict possible outcomes based on a set of decisions it takes on each node. For each node you would ask a question, such as “Does this book have more than 800 pages?” If true, you would follow one path (typically left), otherwise you would go right after that node. You then ask another question to continue splitting the data and repeat the process.  
  
To decide which question (that is, which feature and threshold to consider), you consider a measure of the quality of the split. For classification models (ie, is this a 5-star review or a 4-star review), the [Gini impurity](https://en.wikipedia.org/wiki/Decision_tree_learning#Gini_impurity) is commonly used. This is a measure of how often a random element would be incorrectly labeled for that particular node and has values between 0 and 1. For regression models (ie, the numeric value of positivity), decision trees typically use the mean square error as their criterion for determining the quality of the split. In either case, features and threshold are selected at each node that minimize these measures of error/impurity.  
  
I used Python’s [scikit-learn](http://scikit-learn.org/stable/index.html) to create and evaluate my trees. The figure below shows one tree created when I provide it with all 118 reviews and ask it to classify each review as 5-star, 4-star, or 1-3 stars. I constrain the model to only go 7 layers deep and to only split nodes if there at least 3 elements in it. Following the examples provided in scikit-lean, I generated a figure, which explains it best. You will want to click it to see a larger version.  
  


![](/assets/img/posts/2016/20160920/decision_tree_rating_2_1.png)

  
  
The figure above is the best decision tree classification model for my current set of Goodreads reviews, given the constraints I provided above. You can see it goes down 7 levels (not counting the top one) to attempt to classify every review I provided. Each node is color coded by which class it adopts; 5-star reviews for example are purple. Lighter shades of that color indicate nodes that are classified similarly, but whose entries don’t all have the same class. You can see the gini impurity listed for each node; noes that have gini=0 are the darkest shades of purple/orange/green, those with higher values are progressively lighter.  
  
Let’s have a closer look at that top node. It asks if the number of pages in a book is less than or equal to 830.5 pages. There are 118 samples in that node (all the reviews), and these are distributed in three classes: 24 1-3 star reviews, 47 4-star reviews, and 47 5-star reviews. It adopts an average of 4-stars as the best classification for all 118 reviews, but this is clearly not accurate, hence the gini impurity is rather high at 0.6 (=1 - [(24/118)^2 + (47/118)^2 + (47/118)^2]). After the split, two more nodes are produced. The one on the right is for books longer than 830.5 pages. In that node, there are 12 books, all of which are 5-star books. As such, the gini impurity is 0 and the branch ends. The left branch continues on attempting to split the books as it tries to reach gini=0.  
  
I didn’t allow the model to progress all the way through, but this should still give you an idea of how this model is working. It also provides some interesting results, for example, the fact that books longer than 830 pages always get 5-star reviews from me. Curious, that. Now, let’s work on creating a more complete model.  
**  
****Random Forests**  
While the above provides the basic gist of what I’m doing, I’m using a lot more than just one decision tree for my model. I’m using a random forest. This is very much the same idea, except that multiple decision trees are created from a subsample of the data, which is randomly drawn with replacement (aka, bootstrapping). The results of the trees are averaged together to improve the accuracy and control the amount of over-fitting. I opted to use 100 trees for my models, though when tuning with cross-validation I saw that a smaller number (~50) would have been sufficient. I used the [RandomForestClassifier](http://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html) and [RandomForestRegressor](http://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html) methods in [scikit-learn](http://scikit-learn.org/stable/index.html). In both cases, I split my data into two parts: 80% training data to create the model and 20% test data to examine how well the model worked. Ideally, the model should have incorporating imputing of the training data and applied that to the test data, but for simplicity, I imputed any missing data across the full set prior to splitting.  
  
The first model I created was the classifier, which attempts to predict whether a book will receive 1-3 stars, 4 stars, or 5 stars, similar to the example above. I allowed it to go as deep and split as much as it needed to in order to fit my training data. The model was then compared to the test data (24 books). You can see the confusion matrix below:  
  


![](/assets/img/posts/2016/20160920/confusion_matrix.png)

  
Overall, the model did moderately OK. It correctly predicted 3/4 1-3 star books. For 4-star books, it predicted 12 4-star reviews, but only 7 of those are accurate. That’s a precision (positive predictive value) of 7/12=0.58. The sensitivity (aka recall or true positive rate) is 7/10=0.7. For 5-star reviews my model has a precision of 5/8=0.62, but a sensitivity of 5/10=0.5. We’re dealing with a small number of reviews and with limited features to explore so it’s not surprising that we’re not doing amazing.  
  
The second model I created considered the positivity score. If you recall from [last week](http://strakul.blogspot.com/2016/09/data-science-my-goodreads-reviews_13.html), this is the number of positive words minus the number of negative words in my reviews. The positive/negative sentiments are estimated by comparing with the [NRC Word Emotion Lexicon](http://saifmohammad.com/WebPages/NRC-Emotion-Lexicon.htm). I treated this as a continuous variable, as there is no set number of classes. As such, I used the RandomForestRegressor method to carry out a similar random forest model. The result of this model is a number, which is directly comparable to the positivity. When comparing to the 24 test data points, I found the model differed by an average -0.4 with a standard deviation of 2.7. Recalling that positivity is number of words and there are no fractional words, I see that this model does a decent job at predicting the value of the positivity score within about 3 words.  
  
One final aspect of these models we can consider is the importance of the various features used. The higher the number, the more important it is in the model:  
  


![](/assets/img/posts/2016/20160920/rf_features_1.png)

  
  
Overall, neither the gender of the author or whether or not they are the first book I read from that author matter much in determining the rating or positivity of the review. In terms of the rating I give a book, the number of pages is most important, though other features are comparable. For the positivity of the review, the year in which the book was published is the most important feature. It would have been good to include additional features, namely a set of dummy variables for the genre of the book. However, I did not have that information readily accessible. The models I created may not be perfect, but they are a decent first pass at attempting to predict my reviews.  
  
**The Prediction**  
Finally, with all this in hand it’s time to put it in practice. I went back to the [Goodreads API](https://www.goodreads.com/api?rel=nofollow) and grabbed all books on my To-Read shelf along with their author information. I had 66 books that I had marked as wanting to read. I formatted the data so I could pass the exact same models I created above. I then filtered the data by selecting only the 5-star values and only the top 10% highest positivities. The results, sorted by positivity score, are below:  
  
Title| Author| Average Rating| Positivity  
---|---|---|---  
The War of the Worlds| H.G. Wells| 3.78| 4.83  
Diamonds in the Sky| Mike Brotherton| 3.19| 4.81  
Night of Knives| Ian C. Esslemont| 3.80| 4.79  
Spellwright| Blake Charlton| 3.62| 4.79  
I, Robot| Isaac Asimov| 4.16| 4.77  
Tau Ceti| Kevin J. Anderson| 3.44| 4.77  
Throne of the Crescent Moon| Saladin Ahmed| 3.62| 4.72  
Do Androids Dream of Electric Sheep?| Philip K. Dick| 4.07| 4.69  
The Man in the High Castle| Philip K. Dick| 3.71| 4.69  
The Player of Games| Iain M. Banks| 4.25| 4.67  
Use of Weapons| Iain M. Banks| 4.18| 4.67  
The Myst Reader| Rand Miller| 4.29| 4.66  
The Mirror Empire| Kameron Hurley| 3.51| 4.64  
The Wandering Fire| Guy Gavriel Kay| 4.10| 4.63  
Against a Dark Background| Iain M. Banks| 4.08| 4.62  
Lord of Light| Roger Zelazny| 4.10| 4.61  
Ringworld| Larry Niven| 3.96| 4.61  
  
How accurate is this? Only time will tell, though I expect the model will continue to be refined as I read and review more books. I wish I had more feature to use, though, since genre specifications (science fiction, fantasy, etc) would probably be quite powerful. Regardless, this is a promising list. I certainly have high expectations for some of these, though a few are a bit surprising. I didn’t enjoy [Consider Phlebas](https://www.goodreads.com/review/show/162798639?book_show_action=false&from_review_page=1) that much, so I’m curious to see if I’ll enjoy other Iain M. Banks books. **Tau Ceti** was fairly low on my list, but I had already purchased it in the past and opted to give it a try. We’ll see how it goes.  
  
So what’s next? Well, beside reading more, I want to create a [Heroku](https://www.heroku.com/) app that takes the user name and does all the analysis above to predict the best books to consider. I may have to rewrite parts of the code and figure out some new tricks, but it’s a good challenge and it’ll be a nifty tool I can use to see my model performs. If it works very well, I may consider making it publicly accessible as well so others can see their own book predictions. In the meantime, you can find all the code I used for this little project on [GitHub](https://github.com/dr-rodriguez/Exploring-Goodreads). 
