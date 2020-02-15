---
title: "Keep your customers by your side"
excerpt: "In this post I propose you to explore data from a fictional digital music service company and build algorithms that will help them to detect user churn"
header:
  image: /assets/images/20191031/goodbye.jpg
  caption: "Photo by [Jan Tinneberg](https://unsplash.com/@craft_ear) on [**Unsplash**](https://unsplash.com/photos/gJJhG4gM7NA)"
tags: 
  - Machine Learning
  - DataScience
  - Churn Prediction
  - PySpark
  - Udacity
classes: wide
share: True
comments: true
---

<figure style="width: 300px; margin:0 1em 0 0;" class="align-left">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/woman_headphone_1.jpg" alt="Woman with headphone">
  <figcaption>Credit: Bruce Mars - Unsplash</figcaption>
</figure>  In April 2019, the **I**nternational **F**ederation of the **P**honographic **I**ndustry (IFPI[^1]) have published their annual report[^2] in which they shared some key figures about music market:
{: style="text-align: justify;"}
- global recorded **music market grew by 9.7% in 2018**, the fourth consecutive year of growth
- total revenues for 2018 were US$19.1 billion
- **streaming revenue grew by 34%** and accounted for almost half of global revenue, driven by a 32.9% increase in paid subscription streaming
- there were **255 million users of paid streaming services** at the end of 2018
{: .small style="text-align: justify;margin-left:300px;"}
_"Music streaming is now bigger than ever, with billions of dollars going into the music streaming industry every year. Most music fans are now using subscription-based streaming services such as Spotify, Pandora, and Apple Music[...]. The stats are pouring in faster than ever for music streaming, showing that this is an industry that has earned a bit more attention."_ ([source](https://www.comparitech.com/tv-streaming/streaming-statistics/)).
{: style="text-align: justify;"}
This is real. This is NOW. And this is even more true in emerging markets: _"In Asia-Pacific region, music industry revenues approached $450 million in 2014 and are expected to reach beyond $2 billion by 2020."_ ([source](https://www2.deloitte.com/content/dam/Deloitte/in/Documents/technology-media-telecommunications/in-tmt-rise-of-on-demand-content.pdf)).
{: style="text-align: justify;"}
<figure class="align-center" style="width: 800px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/global_music_industry_revenues_1999_to_2017.png" alt="Evolution of music industry from 1999 to 2017 (source: IFPI)">
  <figcaption>Evolution of music industry from 1999 to 2017</figcaption>
</figure>

<figure style="width: 350px" class="align-right">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/man_headphone.jpg" alt="Man with headphone">
  <figcaption>Credit: NeONBRAND - Unsplash</figcaption>
</figure>

When talking about digital music service the user can get lost very quickly as the number of apps is plethoric. To name, but a few: Amazon Music, Apple Music, Google Play Music, Spotify, Deezer, Napster, Pandora...  
{: style="text-align: justify;"}
***The problem?***  
People can decide to leave whenever they want so the service has to be really attractive and efficient, app must be resilient, available, easy to use and recommender system must satisfy both the relevance of the proposals and the right dose of diversity.  
{: style="text-align: justify;"}
***Why is it important?***  
Because loosing a customer/user is very costly: of course the company looses the _Monthly Recurring Revenue (MRR)_ but not only.  
As explained in this [post](https://www.business2community.com/customer-experience/true-cost-customer-churn-part-1-01990510),
_"there is also potential for loss of renewal income or upsell deals[...]. The probability of upselling to an existing customer is around 65%, compared to the probability to selling 
to a new prospect which is only around 13%."_.  
More than that, _"when churn occurs, not only does the marketing team have to dedicate time and resources to bringing in new leads and prospective customers, but now they 
have to refocus their attention to re-attract customers that have been lost."_  
So in the end **we need to add to churn costs the customer acquisition cost** (a.k.a CAC). And sometimes, depending on the business domain, **this can be really expensive**.
{: style="text-align: justify;"}

---
In this post **I propose you to explore data from a fictional digital music service company and build algorithms that will help them to detect user churn**.
{: style="text-align: justify;"}
**NOTE:** Everything that follows is based on personal work done as part of one project done during my self-training via [`Udacity DataScience nanodegree`](https://www.udacity.com/school-of-data-science).
The comments, interpretations and conclusions are therefore my own ones and are my sole responsibility. This work should therefore not be considered for anything other than their learning value.
{: .notice--warning}

**ACKNOWLEDGEMENT:** data come from Udacity. I would like to thank them for providing us with data that reflects so much of real life.
{: .notice--success}

---
# Let's get this party started!
In this project our goal is to predict user churn. We work for a fictitious company called _"Sparkify"_, a digital music service similar to famous Spotify or Deezer.
{: style="text-align: justify;"}

### Business use case  
_"Many of the users stream their favourite songs with our "Sparkify" service every day using either the free tier that places advertisements 
between the songs or using the premium subscription model where they stream music as free but pay a monthly flat rate.  
Users can upgrade, downgrade or cancel their service at any time so it is crucial to make sure our users love our service."_
{: .small style="text-align: justify;"}
<figure class="align-center" style="width: 550px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/sparkify_overview.png" alt="Sparkify, our fictitious company">
  <figcaption>Sparkify, our fictitious company</figcaption>
</figure>
<figure class="align-left" style="width: 425px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/sparkify_overview_user_collect.png" alt="Sparkify, data collection">
  <figcaption>Sparkify, data collection process</figcaption>
</figure>
_"Every time a user interacts with the service while they are playing songs, logging out, liking a song with a thumbs up, hearing an ad 
or downgrading their service it generates data.  
All this data contains the key insights for keeping our users happy and helping our business thrive."_
{: .small style="text-align: justify;"}
***Our job in the data team is to predict which user are at risk to churn***, either downgrading from premium to free tier or cancelling their service altogether.  
If we can accurately identify these users before they leave, business teams can offers them discounts and incentives potentially saving our business millions in revenue
(this text has been taken from the video of introduction of this project within Udacity's course).
{: .small style="text-align: justify;"}

## 1. About the data we have at our disposal
<div class="notice--primary">
	<h4>We are provided 2 datasets in JSON format:</h4>
	<ul>
		<li>a "tiny" one (128MB although)</li>
		<li>a full dataset (12GB)</li>
	</ul>
	Note that if the dataset can be easily used on a single machine to start the exploration phase, it is not the case for the full one as it contains millions of events.
	We are in big data context and so we have to use appropriate tools.<br />
	<b>To use the full dataset one has to build a Spark cluster on the cloud (AWS, IBM, whatever).</b>
</div>

### 1.1. Global overview
In the dataset, we have 18 features:
```
root
 |-- artist: string (nullable = true)
 |-- auth: string (nullable = true)
 |-- firstName: string (nullable = true)
 |-- gender: string (nullable = true)
 |-- itemInSession: long (nullable = true)
 |-- lastName: string (nullable = true)
 |-- length: double (nullable = true)
 |-- level: string (nullable = true)
 |-- location: string (nullable = true)
 |-- method: string (nullable = true)
 |-- page: string (nullable = true)
 |-- registration: long (nullable = true)
 |-- sessionId: long (nullable = true)
 |-- song: string (nullable = true)
 |-- status: long (nullable = true)
 |-- ts: long (nullable = true)
 |-- userAgent: string (nullable = true)
 |-- userId: string (nullable = true)
``` 

Each feature has been analyzed one by one (number of missing values, number of different values, etc). Here is the table corresponding to the `Data Understanding` phase:  

| Column name | Type | Data Definition |
|-------------|-----------------|----------|
| `artist`    | Categorical     | Name of the artist the user is currently listening to |
| `auth`    | Categorical     | Login status of the user ('Logged In', 'Logged Out', 'Cancelled', 'Guest') | 
| `firstName`    | Categorical     | First name of the user |
| `lastName`    | Categorical     | Last name of the user |
| `gender`    | Categorical     | Gender of the user |
| `itemInSession`    | Numerical     | Number of elements played in the same session |
| `length`    | Numerical     | Number of seconds of the song listened by user |
| `level`    | Categorical     | Free or paid user? |
| `location`    | Categorical     | User's location (in United States) |
| `method`    | Categorical     | HTTP method used for the action (PUT or GET), this is a technical information |
| `page`    | Categorical     | User's action (one event corresponding to what used did: listening a song, an ad, logging, downgrading, etc) |
| `registration`    | Numerical     | User's registration timestamp |
| `sessionId`    | Numerical     | Session id |
| `song`    | Categorical     | Song currently played by user |
| `status`    | Numerical     | HTTP code for user's action (200=OK, 404=error) |
| `ts`    | Numerical     | User's action timestamp |
| `userAgent`    | Categorical     | User's browser user-agent |
| `userId`    | Numerical     | User technical id |

### 1.2. What can we use to determine when user will churn?
Each row in the dataset corresponds to a specific event for a specific user. So for each user we have one to many events that describe his behaviour among time. Our goal will be to learn from this behaviour in order to detect user that might soon churn.
{: style="text-align: justify;"}
**NOTE on Big Data:** in the smaller dataset there are 278154 events corresponding to 225 unique users. This dataset has been used to explore the data and build/debug the `Feature Engineering` script.  
This script has then been used within a Spark cluster deployed on AWS cloud (EMR)[^3][^4].  
***The graphs below come from the full dataset that contains more than 26 million events corresponding to 22278 unique users.***
{: .notice .small style="text-align: justify;"}

#### `Gender` vs. churn
<figure class="align-left" style="width: 500px; margin:0 1em 0;">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/eda_churn_vs_gender.png" alt="Gender vs. churn">
  <figcaption>Number of churn users and churn vs. gender</figcaption>
</figure>
First observation is that (fortunately for _Sparkify_!) **there are more users that stay than users who churn**. This will be an issue to deal with during the `Modeling` phase and it will be discussed later.  
{: .small style="text-align: justify;"}
From what we can see within this dataset **there is no real evidence that Men (gender=1 in the graph) churn more than Women** (22.8% of Men have churned while 22% of Women have churned, we can say that it is more or less the same).
{: .small style="text-align: justify;"}
<br />

#### `Subscription level` vs. churn
<figure class="align-right" style="width: 500px;  margin:0 0 0 1em;">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/eda_churn_vs_subscription.png" alt="Subscription level vs. churn">
  <figcaption>Subscription level vs. churn</figcaption>
</figure>
Are the users with a paid subscription level (level=1 in the graph) more willing to leave or not?  
{: .small style="text-align: justify;"}
There are more users with a paid subscription account than free tier so here again we have to be very careful when making conclusions.  
Figures tell us that **24% of people with a paid subscription have churned versus "only" 18% for people with a free account** who have churned.
{: .small style="text-align: justify;"}
<br /><br />

#### `Registration time` vs. churn
<figure class="align-left" style="width: 500px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/eda_churn_vs_registration.png" alt="Registration time vs. churn">
  <figcaption>Registration time vs. churn</figcaption>
</figure>
We have seen that the column `registration` refers to the date the user registered to the service, we can analyze for how long did the user tried the service before cancelling.  
{: .small style="text-align: justify;"}
In this dataset, **churn users have less used the service**. Building a feature with the time elapsed (such as number of days for example) since the registration could be something useful in the modeling part.  
Once this is said, we must remain very cautious about that because of course, as soon as the user stops using the service this value stops increasing (and that might explain why it is less for churn users).
{: .small style="text-align: justify;"}
<br /><br />

#### `User engagement` vs. churn
The idea developped here is that the more a user interacted with the service (for example in terms of number of artists/songs listened, number of sessions, time spent listening) the more chances we have this user enjoys the service and then will not quit. Let's see if there are big differences between churn users and the others.
{: .small style="text-align: justify;"}
<figure class="align-center" style="width: 800px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/eda_churn_vs_user_engagement.png" alt="User engagement vs. churn">
  <figcaption>User engagement vs. churn</figcaption>
</figure>
<div class="notice--info">
	<h4>Observations:</h4>
	<ul>
		<li><b>churn users have listened to less unique artists and songs</b>. It can means that they are less connected with the service but those figures can also be explained because churn users have stopped using the service so the "counter" has stopped as well</li>
		<li><b>the total time of listening is also lower for churn users</b> (explained by the previous measures: less connections, less songs listened = less total time)</li>
	</ul>
	<i>Note that the differences were more obvious in the smaller dataset.</i>
</div>

#### `User social interactions` vs. churn
This section is quite similar to the previous one: here we will focus on "social interactions" for a user. It could be for example: how many friends did the user add? How many `Thumbs up` or `Thumbs down`? Could it be a clue to detect incoming churn?
{: .small style="text-align: justify;"}
<figure class="align-center" style="width: 800px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/eda_churn_vs_social_interaction.png" alt="User social interactions vs. churn">
  <figcaption>User social interactions vs. churn</figcaption>
</figure>
<div class="notice--info">
	<h4>Observations:</h4>
	<ul>
		<li>As imagined, there were <b>less "positive social interactions" (add a friend, give a like) with the app for churn users.</b></li>
		<li>On the other side, the distribution for "negative social interactions" (dislike/thumbs down) are quite close from each other. That could mean that the recommendations (for example) were not so good and the user gave a thumbs down.</li>
		<li>The last one has to be taken with caution because users with a "paid" subscription are listening less (and even no) ads. Though, we can observe that <b>churn users have rolled ads more than the others</b>. Perhaps they cancelled due to the amount of ads.</li>
	</ul>
</div>

#### `Upgrade/Downgrade` vs. churn
While having a look at the different pages, I noticed 2 kind of pages that could be interesting ones: `Upgrade` and `Downgrade`. Intuition here is to say that if user has upgraded the service he is now more engaged whereas, on the other side, the "downgrade" step could be the first step towards the churn.  
{: .small style="text-align: justify;"}
<figure class="align-center" style="width: 800px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/eda_churn_vs_upgrade.png" alt="Upgrade/Downgrade vs. churn">
  <figcaption>Number of Upgrade/Downgrade events per user vs. churn</figcaption>
</figure>
***Observation:*** the most interesting thing here is that **churn users thought more about downgrading** (even if most of them stayed in the end). This could be the first step towards the cancellation.
{: .notice--info .small style="text-align: justify;"}

#### `User's Operating System` vs. churn
<figure class="align-left" style="width: 550px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/eda_churn_vs_user_os.png" alt="User OS vs. churn">
  <figcaption>User OS vs. churn (note that this graph has been made with data from tiny dataset)</figcaption>
</figure>
There is a feature with user's browser user-agent. We could try to extract some information such as the underlying Operating System or even maybe the device.  
{: .small style="text-align: justify;"}
Process: a regular expression has been used to retrieve Operating System information from the user-agent value[^5]. Then, values were mapped to most known OS by using this table[^6].  
{: .small style="text-align: justify;"}
***Observation:*** in this smaller dataset people with a Mac are the ones who have churned most (but they are also the most represented users!). I would highliht **'Linux' users that are near 40/60% of churn/no churn!** Maybe there is an issue with the browser they are using or something that does not work as expected.
{: .small style="text-align: justify;"}
<br />

## 2. Collect everything we need
A `Feature Engineering` phase has been ran against the full dataset in order to collect values per user for each of the element seen during the `Data Exploration` phase.  
{: style="text-align: justify;"}
After this step the dataset that will be used for `Modeling` looks like this:  
```
root
 |-- churn: integer (nullable = true)
 |-- gender: integer (nullable = true)
 |-- level: integer (nullable = true)
 |-- timedelta: integer (nullable = true)
 |-- nb_unique_songs: integer (nullable = true)
 |-- nb_total_songs: integer (nullable = true)
 |-- nb_unique_artists: integer (nullable = true)
 |-- total_length: double (nullable = true)
 |-- total_add_playlist: integer (nullable = true)
 |-- total_add_friend: integer (nullable = true)
 |-- total_thumbs_up: integer (nullable = true)
 |-- total_thumbs_down: integer (nullable = true)
 |-- total_ads: integer (nullable = true)
 |-- think_upgrade: integer (nullable = true)
 |-- has_upgraded: integer (nullable = true)
 |-- think_downgrade: integer (nullable = true)
 |-- has_downgraded: integer (nullable = true)
 |-- nb_404: integer (nullable = true)
 |-- Windows 8: integer (nullable = true)
 |-- iPad: integer (nullable = true)
 |-- iPhone: integer (nullable = true)
 |-- Macintosh: integer (nullable = true)
 |-- Linux: integer (nullable = true)
 |-- Unknown: integer (nullable = true)
 |-- Windows Vista: integer (nullable = true)
 |-- Windows 81: integer (nullable = true)
 |-- Windows XP: integer (nullable = true)
 |-- Windows Seven: integer (nullable = true)
```
<div class="notice--success">
	<h4>Results of the Feature Engineering process</h4>
	As you can see, a lot of features have been extracted. In few words, we have:
	<ul>
		<li>the <i>churn</i> column (our target which is binary 0/1). A user has been classified as churn as soon as he submitted cancellation (this is one specific event that can be caught in the list of given events).</li>
		<li>user's informations such as his <i>gender</i>, his <i>subscription level</i>, time elapsed between his registration and his last known action (<i>timedelta</i>)</li>
		<li>user's usage of the service (<i>nb_unique_songs, nb_total_songs, total_length</i>) or his social interactions (<i>total_add_friend, total_thumbs_up</i>)</li>
		<li>informations about how many times the user thought about upgrading or downgrading and how many times he actually did</li>
		<li>user's operating system extracted from his user-agent which could help us to identify users of a version that does not give entire satisfaction</li>
		<li>number of errors encountered which could help us to identify users who had several issues and then maybe quit</li>
	</ul>
</div>

In the end **there are more features than in the original dataset** (also due to the fact that some categorical features have been one-hot encoded in order to be passed to our models which accept only numeric values).  
**But there are far less rows** as now we have only one row per user (so 22178 unique users overall).
{: style="text-align: justify;"}

## 3. Modeling: now it's time to try to predict the churn!
<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/crystal_ball.jpg" alt="Crystal ball">
  <figcaption>Crystal ball - Credit: Muhd Asyraaf - Unsplash</figcaption>
</figure>

### 3.1. What kind of problem is it and how can we evaluate our performance?
<figure style="width: 250px; margin: 0 0 0 1em;" class="align-right">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/measures.png" alt="Measures">
  <figcaption>Credit: William Iven - Unsplash</figcaption>
</figure>
Here our goal is to **predict** the churn, which is a **qualitative value** so it is a ***classification problem***.  
{: .small style="text-align: justify;"}
As already said earlier when looking at the graphs, there are more users that stay than users who churn. So our target class is imbalanced. **This is an issue in Machine Learning** because we have less examples to learn well and if our model
often see the same value it will obviously 'learn' that and tend to predict this value.  
**That is why we have to wisely choose the performance metric!**  
{: .small style="text-align: justify;"}

### 3.2. So what will be our performance metric?
If we choose `accuracy` as the performance metric to check whether we classify well or not, a dummy classifier that always predict the most frequent class will have a good score but in the end we will have built a very poor model.
{: style="text-align: justify;"}
<figure style="width: 450px; margin:0 1em 1em;" class="align-left">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/precisionrecall.png" alt="Precision and Recall">
  <figcaption>Credit: Walber - Creative Commons Attribution-Share Alike 4.0 International license</figcaption>
</figure>
In classification problems, when data are imbalanced we prefer using other metrics such as:
{: .small style="text-align: justify;"}
* ***Precision:*** among all the positive predictions made by the model, we count how many of them were actually positive. This is a ratio and the higher the better because it means that our model is very precise. 
In other words, when the model says it is true, it is actually true (in this case there are few _"False Positives"_).
* ***Recall:*** among all the real positive values, how many of them did our model classified as positive? This metric indicates how good is our model to "catch them all".
Indeed, the model can be very precise but could still miss a lot of positive samples. And this is not good neither. Recall is also a ratio and the higher the better (when it is high there are few _"False Negatives"_).
{: .small style="text-align: justify;"}

**Precision** has to be chosen when there is a **big impact with False Positives.**  
**Recall** has to be chosen when there is a **big impact with False Negatives.**
{: .notice--success style="text-align: justify;"}
 
That is why, depending on the use case, we might want to focus on `Recall` rather than `Precision` or vice-versa:  
{: style="text-align: justify;"}
> For example in medical domain, when algorithm says the person has/does not have cancer, we want this information to be as accurate as possible (you can easily imagine the impacts when this is wrong), this is the _Precision_.  
But we also want to avoid saying someone that he does not have cancer whereas he actually has one (this is the worst case scenario).  
So perhaps in this case we want to focus more on _Recall_ (ensure we catch all of them) even if it means that it is less precise (people will pass other tests that will later confirm that in the end they do not have cancer).
{: style="text-align: justify;"}
If we do not want to choose between _Precision_ or _Recall_ because both are kind of equally important we can choose to use the ***F1-Score*** metric which is the harmonic mean of both:  
{: style="text-align: justify;"}
> ```F1 score = 2x ((Precision x Recall)/(Precision + Recall))```

**F1-Score will be the selected metric in our case.**


### 3.3. Ensure that we have enough data to train on
When data are imbalanced there is also another issue: how to ensure that in our training dataset we will have enough samples of each class to train on and then be able to learn and classify correctly?  
The split between train and test dataset cannot then be totally random. When working with scikit-learn models, there is this *[scikit-multilearn package](http://scikit.ml/index.html)*
that can help to [stratify the data](http://scikit.ml/stratification.html).  
We can also use other techniques such as:
{: style="text-align: justify;"}
* ***oversampling***: duplicate the data for classes that appears less so that in the end there are more of them and
algorithm can learn. The drawback is that depending on the use case, as it is the same data that is duplicated, the 
learning can have a big bias.
* ***undersampling***: randomly remove some occurrences of classes that appears the most. This is also a good idea but it can
lead us to a lot of data loss depending on how huge is the gap between classes.
* we could mix both oversampling and undersampling
{: .small style="text-align: justify;"}
As we are working with Spark ML, this is not possible to use this scikit-multilearn package out-of-the-box so easily. Fortunately, in our case there is a single class and it is binary (0/1), the random split between training and test datasets gave good results:
{: style="text-align: justify;"}
<figure style="width: 650px;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/train_test_split_churn_rate.png" alt="Train and test splits">
  <figcaption>Churn rate in both training and test splits of the dataset</figcaption>
</figure>
We can see that there are more or less no difference in the churn rate between those 2 datasets so they are well balanced.

### 3.4. Eligible models
Actually I will not pick one model randomly but instead I will try a few:
* 2 dummy classifiers that will return as predicted value always 0 or always 1
* another very simple statistic model: a **Logistic Regression** (it will be our reference model to compare to)
* an ensembling model based on trees such as **Random Forest**
* a more complex model (but yet still based on trees) named **Gradient-Boosted Tree**.
{: .small }
All those models are part of the Spark ML[^7] so we can use them.

***Why those models?***

Dummy classifiers
:   Motivation for those models is to compare more complex models with something very simple that does not even require machine learning and see how much we do better (or not...). We can measure the 
performance metric for those classifiers and see the impact on `accuracy` metric when class is imbalanced and have a first overview of what is the F1-Score for both of them.
{: style="text-align: justify;"}
Logistic Regression
:   It is a very basic model but which sometimes give good results and could be an outsider due to the computation time which is not very high.
{: style="text-align: justify;"}
Random Forest
:   The cool thing with trees is that it is possible to plot them, so explain them. Random Forest are also known to perform pretty well even when classes are imbalanced. Moreover, in terms of preprocessing
there are few things to do because we do not even have to scale or normalize or data, we jut have to ensure that there are no missing values.
{: style="text-align: justify;"}
Gradient-Boosted Tree
:   It is also based on trees and is a powerful machine learning technique that uses _Boosting_ approach: **trees are added over iterations in order to reduce the errors made by the combination of all the previous ones**.
{: style="text-align: justify;"}


### 3.5. Model results and confusion matrix
For a better understanding, after the split here are the count of churn vs. no churn users in the test dataset.
<figure style="width: 400px;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/churn_counts_in_test.png" alt="Churn and no churn in test split">
  <figcaption>Churn and no churn in test split</figcaption>
</figure>

Modeling started by building a pessimistic classifier that predicts each user as a churn one and an optimistic one that predicts that all users will stay (so no churn at all). Here are the scores for both of them:
{: style="text-align: justify;"}
* F1-Score for pessimistic classifier: 0.08 (accuracy: 0.23)
* F1-Score for optimistic classifier: 0.67 (accuracy: 0.77)
{: .small style="text-align: justify;"}
As we can see, it is quite easy to have 77% of accuracy with a dumb classifier so `accuracy` is definitely not the right metric to take.
{: .notice--warning}

Below is a report of the metrics results for each model. We can see that with the default parameters the **Gradient-Boosted Tree was our best model with a F1-Score=0.82**. Even if it 
is the algorithm that takes much more time to run (due to the sequence of tree addition), we can afford it because the performance is very high compared to others solutions that have been tried.  
This model has been chosen for a further tuning phase in which a range of possible values are explored for some parameters of the model. There were:
{: style="text-align: justify;"}
* **stepSize** (a.k.a. learning rate)
* **maxDepth**: to control the maximum depth of the tree
* **minInstancesPerNode**: the minimum number of instances each child must have after split.
* **subsamplingRate**: fraction of the training data used for learning each decision tree
{: .small style="text-align: justify;"}

| Model name                          | Computation time | F1-Score | Weighted Precision | Weighted Recall |
|-------------------------------------|------------------|----------|--------------------|-----------------|
| Logistic Regression                 | 23.6s            | 0.75     | 0.78               | 0.8             |
| Random Forest                       | 17.2s            | 0.79     | 0.82               | 0.82            |
| Gradient-Boosted Tree               | 1min 1s          | 0.82     | 0.83               | 0.84            |
| Tuned Gradient-Boosted Tree         | 24min 48s        | 0.82     | 0.83               | 0.84            |

But in the end, the best model found by the grid search was not better than our initial one. And there is something painful with the Spark ML package that does not retain all models but keep only the best. 
So we are not able to have a look at all combinations to adapt our parameters exploration ranges like what we often do when dealing with scikit-learn ([source](https://stackoverflow.com/questions/39529012/pyspark-get-all-parameters-of-models-created-with-paramgridbuilder): 
_"you simply cannot get parameters for all models because CrossValidator retains only the best model. These classes are designed for semi-automated model selection not exploration or experiments."_)
{: style="text-align: justify;"}

Here is the confusion matrix for this GBT classifier:
<figure style="width: 600px;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/pred_results.png" alt="Confusion matrix for GBT">
  <figcaption>Target vs. Prediction and confusion matrix for GBT</figcaption>
</figure>

Those results are the best ones but when having a deeper look **it is not so amazing, we were able to catch only 40% of the churn users**. Good point is that only 3% of the non churn users were classified as churn.
{: .notice--warning}

### 3.6. Features importance
As we are using a tree-based solution we can plot a graph with the _features importance_: it will then gives us more information about what, in the dataset, contributed the most to the churn prediction.   
{: style="text-align: justify;"}
<figure style="width: 600px;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/results_feature_importance.png" alt="Feature importance for GBT">
  <figcaption>Feature importance for GBT</figcaption>
</figure>

<div class="notice--info">
	<h4>Observations:</h4>
	<i>In this dataset and with the features I have created:</i>
	<ul>
		<li><b>the one that contributes the most to determine if a user will churn or not is the total number of ads listened</b>. That would mean that users are fed up with ads and decide to quit.</li>
		<li>The second one is the <b>number of days of the service</b> (probably the less you use the more you might churn - we have seen that during the exploration phase)</li>
		<li>Just after comes the <b>social interactions with the service (either thumbs up or down) meaning that you are satisfied or not with the songs that are suggested</b> by the service for example.</li>
		<li>On the other side, we can see that the <b>user's operating system has few importance</b>. As it was quite painful to collect I suggest that we get rid of that information as a further improvement of this model. The data collect pipeline would be much more faster.</li>
	</ul>
</div>

---
# WRAP-UP
* **We now have a model that can detect churn users** with a quite good performance (F1-Score is 0.82 with good precision and recall).
* We could use this model on the whole database of users, classify them and **give the list of users that might soon churn to a commercial/marketing service** that can contact them and propose incentives for them to stay.
* We also have a **better intuition of what contributes the most to churn detection** (keep in mind though that this is totally dependent on features that have been created).
{: .notice--success style="text-align: justify;"}

Churn prediction gave first encouraging results which still need to be improved through further investigations:

* **build new features** and/or try to remove ones that could bring more noise than useful information in the model
* **40% of churn users detected** is better than nothing but obviously when we are talking about so much money it **is not enough** and we should invest more energy in finding why others are not well classified (starting with an error analysis phase for instance).
* in our model the churn is only based on people who cancel their service but we could also **try to classify people that downgrade from paid-subscription level to free tier** as the company looses the MRR.
{: .notice--info style="text-align: justify;"}

---
<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20191031/woman_headphone.jpg" alt="Woman headphone">
  <figcaption>Credit: Alice Moore - Unsplash</figcaption>
</figure>
The world of data is a very fascinating one and you could never end to try to do something with data, interpret it. So, are you ready to give a try by yourself?  
{: style="text-align: justify;"}

Thanks for reading.  
Author: nidragedd
{: .small style="text-align: right;"}

---
If you would like to check out the whole project you can [see it from my Github repository](https://github.com/nidragedd/udadsnd-p7-churn_prediction).  
{: .small }

---

[^1]: [IFPI](https://ifpi.org/about.php) is the voice of the recording industry worldwide and represent the interests of 1,300 record companies across the globe.
[^2]: [2019 IFPI annual report](https://ifpi.org/news/IFPI-GLOBAL-MUSIC-REPORT-2019)
[^3]: [Getting started with PySpark on Amazon EMR](https://towardsdatascience.com/getting-started-with-pyspark-on-amazon-emr-c85154b6b921)
[^4]: [Use PySpark with a Jupyter notebook in an AWS EMR cluster](https://towardsdatascience.com/use-pyspark-with-a-jupyter-notebook-in-an-aws-emr-cluster-e5abc4cc9bdd)
[^5]: [User-Agent overview on Mozilla website](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)
[^6]: [List of Microsoft Windows versions](https://en.wikipedia.org/wiki/List_of_Microsoft_Windows_versions)
[^7]: [Classification algorit available within Spark ML](https://spark.apache.org/docs/latest/ml-classification-regression.html)
