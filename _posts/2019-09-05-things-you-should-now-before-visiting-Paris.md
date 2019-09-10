---
title: "Things You Should Know Before Visiting Paris"
excerpt: "In this post I propose you to explore AirBnb data related to the city of Paris and thus discover what we can extract from it and to what extent we can train algorithms to learn from these data"
header:
  image: /assets/images/20190905/paris-eiffel-tower.jpg
  caption: "Photo by [Anthony DELANOIX](https://unsplash.com/@anthonydelanoix) on [**Unsplash**](https://unsplash.com/photos/QAwciFlS1g4)"
tags: 
  - Machine Learning
  - DataScience
  - XGBoost
  - Udacity
classes: wide
share: True
---

<figure style="width: 200px" class="align-left">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/paris-tertre.jpg" alt="Place Tertre, Paris">
  <figcaption>Credit: Artiom Vallat - Unsplash</figcaption>
</figure>  **Paris**, with more than 2000 years of history[^1], will host the 2024 Olympic Games in particular and, although it was the scene of a recent world drama with the Notre-Dame fire, remains one if not the most visited city in the world (and I am not saying that because I am french!)[^2][^3].  
{: style="text-align: justify;"}
If you are dying to visit the _Eiffel Tower_, stroll through the _artistic districts of Montmartre_ or even _sail on a barge on the Seine_, here is some information that might be useful to you.
{: style="text-align: justify;"}
To come and visit Paris you can look for accommodation, a hotel by your own means. Otherwise in recent years, an online service has developed considerably: [Airbnb](https://www.airbnb.com).
{: style="text-align: justify;"}
<figure class="align-center" style="width: 450px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/ml_airbnb_users_over_time.png" alt="Evolution Airbnb places to rent in Paris">
  <figcaption>Evolution of AirBnb places to rent in Paris since 2008 (note: this information has been extracted from the data)</figcaption>
</figure>

<figure style="width: 200px" class="align-right">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/poker.jpg" alt="Poker player">
  <figcaption>Credit: Michal Parzuchowski - Unsplash</figcaption>
</figure>

If you  have followed technological news recently you have necessarily heard about the resounding successes such as:
* those of DeepMind with _AlphaZero_, an AI that masters chess, shogi, and Go games through self-play[^4][^5]
* or _Pluribus_ an AI bot that masters Texas hold'em poker with 6 players[^6]!  

*The advances of AI are numerous and affect many fields* (commerce, finance, medicine, etc.).  

**Machine learning** and Artificial Intelligence are no longer a fashion trend but a real subject that **has developed quickly in recent years**:

<figure class="align-center" style="width: 600px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/google_trend_machine_learning.png" alt="Google trend machine learning">
  <figcaption>"Machine Learning" Google Trend all over the world for last past years</figcaption>
</figure>

Well, Airbnb is a company very present and active in the machine learning sector. It does not hesitate to share some of its data, publish tools it has developed in 
open source such as _Airflow_[^7] and regularly publish posts on its technical blog[^8] to share its thoughts and research areas.
{: style="text-align: justify;"}
In this post **I propose you to explore AirBnb's data relating to the city of Paris and thus discover what we can extract from it and to what extent we can train algorithms to learn from these data**.
{: style="text-align: justify;"}
**NOTE:** Everything that follows is based on personal work done as part of one project done during my self-training via [`Udacity DataScience nanodegree`](https://www.udacity.com/school-of-data-science).
The comments, interpretations and conclusions are therefore my own ones and are my sole responsibility. This work should therefore not be considered for anything other than their learning value.
{: .notice--warning}

**ACKNOWLEDGEMENT:** data come from airbnbinside and can be collected from [here](http://insideairbnb.com/get-the-data.html). I took the one from July 2019 so everything that comes next is time-based on this period.
{: .notice--success}

---
# Let's get this party started!
The remainder of this post is organized into two topics:
* The first one is related to data analysis and will aim to answer questions from this scenario:
> Let's put myself in the shoes of a tourist who would love to come and visit Paris. As a tourist/foreigner, an Airbnb service user and also someone who absolutely does not know Paris, I would like to know what is the best period or the best place (or both) in order to plan my visit/holidays. As "best" can means a lot, here:
1. I would like to have a pretty good idea of the cost variation depending on location place
2. Same for visiting period
3. I would be glad to know what are the odds to find something available for a given period so that perhaps I can adapt my holidays accordingly to places availabilities (there is an alternative for the homeowner who would like be sure to rent at the best time that will maximize the profit and so maybe adapt the location availability depending on the market as well).
{: style="text-align: justify;"}
* The second one is more related to machine learning algorithms:
> Let's see if we are able to predict price with pretty good accuracy/confidence for a given listing? Indeed, as a service user who plans to rent its home, I can spend plenty of time by looking at other places and their price to find the best but still competitive price to ensure I will be chosen. Or, more user friendly: based on information I give, the system predicts the right price!
{: style="text-align: justify;"}
## 1. At the right place...
In data, Airbnb listings are provided with latitude and longitude coordinates. When we plot them on a scatterplot, here is what we get:
<figure class="align-center" style="width: 700px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/ml_plot_per_neighbourhood.png" alt="Paris Airbnb listings per neighbourhood">
  <figcaption>Paris Airbnb listings per neighbourhood</figcaption>
</figure>
**NOTE:** we can clearly see that there are no listing in some places in neighbourhoods 12 or 16 (respectively _Reuilly_ and _Passy_), it is because there are some woods (_Vincennes_, _Boulogne_).
We are also able to guess the _Seine_ which is the river that crosses the city.
{: .notice .small style="text-align: justify;"}	

<figure class="align-left" style="width: 470px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/price_per_neighbourhood_french_study.jpg" alt="Price per neighbourhood">
  <figcaption>Mean price in euros per square meter of real estate per neighbourhood as per french study</figcaption>
</figure>
It is written in this french study published in April 2019[^9] that _"the price of real estate has reached stratospheric levels and totally disconnected from residents' incomes. But what are the prices, neighbourhood per neighbourhood?"_
{: style="text-align: justify;"}
According to this study, top 5 is:  
 -- 6e (Luxembourg) (13 944 &euro;/m<sup>2</sup>)  
-- 7e (Palais-Bourbon) (13 569 &euro;/m<sup>2</sup>)  
-- 4e (Hotel-de-Ville) (13 262 &euro;/m<sup>2</sup>)  
-- 8e (Elysee) (12 436 &euro;/m<sup>2</sup>)  
-- 16e (Passy) (11 753 &euro;/m<sup>2</sup>)  
{: .small}
Whereas 19e and 20e (respectively Buttes-Chaumont and Menilmontant) have the "cheapest" price.  
{: .small style="text-align: justify;"}
<br />
### So, has the neighbourhood a real impact on the price?
_Is it something that we can also observe in our data? Can we confirm the french study observations?_
<figure class="align-center" style="width: 600px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/ml_mean_price_per_neighbourhood.png" alt="Paris Airbnb mean price per neighbourhood">
  <figcaption>Paris Airbnb listings mean price per neighbourhood</figcaption>
</figure>

**Interesting: the top 5 from the study is within the top 6 of the mean price!**
Here are some other insights caught from data:
<figure class="align-center" style="width: 800px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/ml_plots_per_neighbourhood_1.png" alt="Paris Airbnb number of listings and their availaility per neighbourhood">
  <figcaption>Paris Airbnb number of listings and their availaility per neighbourhood</figcaption>
</figure>

**_Elysee_ is far the most expensive neighbourhood** and the one with most availability (perhaps due to the price?) even if there are few listings (only _Louvre_ has less listings to offer).  
10 neighbourhoods have mean price above the **mean value (116&euro;)** while 10 have mean price below. So it's a 50-50.  
It is though important to notice that only 2 neighbourhoods have mean price below the **median value (80&euro;)**: _Menilmontant_ and _Buttes-Chaumont_. Same result than the study! In those 2 neighbourhoods, there are a lot of listings but with few availability per year, 60 days in average.
{: .notice--primary style="text-align: justify;"}

### But of course it depends on type of room I will rent, right?
In Airbnb data, there are 3 types of room available to rent:
* Entire home/apt
* Private room
* Shared room
{: .small }
What is their share? Where are they? Does it really change something on the price?

<figure class="align-center" style="width: 700px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/ml_plot_per_roomtype.png" alt="Paris Airbnb listing location per room type">
  <figcaption>Paris Airbnb listing location per room type</figcaption>
</figure>

<figure class="align-center" style="width: 400px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/ml_plot_meanprice_per_roomtype.png" alt="Paris Airbnb listing mean price per room type">
  <figcaption>Paris Airbnb listing mean price per room type</figcaption>
</figure>

**86% of the listings are 'Entire home/apt'** whereas there are less than 1% of 'Shared room'.  
This is interesting: **choosing a private room or an entire apartment has few influence on the price**. On the other side, you can expect to divide by 2 the mean price by choosing a shared room. There are less than 500 shared rooms available but they are almost everywhere.
{: .notice--primary style="text-align: justify;"}

### Where are the most and less expensive listings?
We have seen that shared rooms are less expensive so in this section they are filtered to not catch them in the less expensive listings.

<figure class="align-center" style="width: 700px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/ml_plot_per_place.png" alt="Paris Airbnb listing location most and less expensive">
  <figcaption>Paris Airbnb listing location most and less expensive</figcaption>
</figure>

**OBSERVATIONS**: depending on your budget, it can really cost you a lot to sleep in Paris. The most expensive neighbourhoods are the one in the center of the city, you will pay less and less gradually as you go further but in those neighbourhoods, the availability rate is not very high because you will probably find apartments in which people live most of the time and rent when they are away from their home.  
***Tip:*** you can expect to divide by 2 the mean price by choosing a shared room and there are almost everywhere in the city so that you are not stucked in a specific place. So, ready to share your room?
{: .notice--success style="text-align: justify;"}

## 2. ...and at the right time!
### Choosing the right period
Are there some periods of time with higher/lower availabilty?

<figure class="align-center" style="width: 700px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/ml_plot_availability_over_time.png" alt="Paris Airbnb listing mean availability evolution over time">
  <figcaption>Paris Airbnb listing mean availability over time</figcaption>
</figure>
Note: vertical dashed red lines corresponds to french school holidays begin/end dates. I wanted to see whether availability was impacted or not. The blue ones are just here to show the end of each month.
{: .small style="text-align: justify;"}

<div class="notice--primary">
	<h4>OBSERVATIONS:</h4>
	<ul>
		<li>availability is very low at the beginning (July) but I guess this behaviour is expected as most of the listings have found guests for the next coming weeks (remember that the data are the one from July).</li>
		<li>there is a <b>peak in August which is the highest availability</b>. Again, this is summer holidays and I guess that a lot of people are leaving and then put their home/apartment as available.</li>
		<li>we then see the <b>'Back to school' impact in September</b> with another availability during autumn holidays.</li>
		<li>it then becomes more and more available with a <b>new availability peak just after Christmas holidays, for New Year</b> (want to see fireworks at Eiffel Tower?).</li>
		<li>after that it goes back to "normal" and flat rate perhaps because it is so far that people just left their calendar as it has been filled without changing anything.</li>
	</ul>
</div>

**Note**: it seems that **school holidays have not really a big impact**.
{: .notice--success style="text-align: justify;"}

### Variation of price over time
<figure class="align-center" style="width: 700px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/ml_plot_price_over_time.png" alt="Paris Airbnb listing mean price evolution over time">
  <figcaption>Paris Airbnb listing mean price evolution over time</figcaption>
</figure>

<div class="notice">
	<h4>OBSERVATIONS:</h4>
	<p>It is typically the law of supply and demand:</p>
	<ul>
		<li><b>prices are lower in August</b> (because there are a lot of rooms to share and home owners prefer offering discount than keeping the apartment and in the end earn nothing).</li>
		<li>there is a peak during Christmas holidays so even if there are more apartments available, <b>you will spend more money if you plan coming for New Year fireworks at Eiffel Tower</b>.</li>
		<li>the "small" and recurrent peaks corresponds to weekends. <b>Prices are higher during the weekends</b>. There are no small savings.</li>
	</ul>
</div>

#### Is there a big difference between neighbourhoods?
<figure class="align-center" style="width: 700px">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/ml_plot_price_over_time_per_neighbourhood.png" alt="Paris Airbnb listing mean price evolution over time per neighbourhood">
  <figcaption>Paris Airbnb listing mean price evolution over time per neighbourhood</figcaption>
</figure>

There are real differences depending on the neighbourhood: _Entrep&ocirc;t_ has high variations whereas _H&ocirc;tel-de-Ville_ is more or less at the same price over the year.
{: .notice--primary style="text-align: justify;"}
No matter the neighbourhood, they all contain, at different level of price, the same peak in january for New Year.
{: .notice--warning style="text-align: justify;"}

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/paris-fireworks.jpg" alt="Paris New Year fireworks">
  <figcaption>Paris New Year fireworks - Credit: Joe DeSousa - Unsplash</figcaption>
</figure>

<div class="notice--success">
	<h4>OBSERVATIONS:</h4>
	<p>If we do not take into account the period after Christmas (because prices are increasing with lower availability, I guess people mostly gave availabilities for 2019 but dit not yet do the same thing for 2020), we can see that:</p>
	<ul>
		<li>the <b>'Christmas effect' results in +6% (average) for prices</b> with some neighbourhoods above that (<i>Elysee</i> has +10% for example, where you can find the <i>Champs Elysees</i>).</li>
		<li>the <b>'New Year effect' has a huge impact on neighbourhoods in the center such as <i>Elysee</i>, <i>Palais-Bourbon</i>, or <i>Temple</i> with +17 to +19%!</b></li>
	</ul>
</div>

## And now it's time to try to predict the price!

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/crystal_ball.jpg" alt="Crystal ball">
  <figcaption>Crystal ball - Credit: Marc Schulte - Unsplash</figcaption>
</figure>

### What kind of problem is it and how can we evaluate our performance?
Here our goal is to **predict** the price, which is a **quantitative value**, that is why we call that a ***regression problem*** (in opposition to _classification_ problems for which we predict qualitative value, for example "Is it a dog or a cat?").  
{: style="text-align: justify;"}
<figure style="width: 450px" class="align-right">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/dartboard.jpg" alt="Dartboard">
  <figcaption>Credit: Engin Akyurt - Pexels</figcaption>
</figure>
To evaluate the performance I will use a metric called ***RMSE*** which stands for _Root Mean Squared Error_. Do not be afraid by this strange name, it is actually very simple and oftenly used as performance metric.
{: .small style="text-align: justify;"}
Basically here is how it works:
{: .small }
* we predict the price: let's call the result _y_pred_
* we compare _y_pred_ with the real price _y_ and compute the difference _y - y_pred_: this is our error. Note that it can be a positive or a negative number depending on our prediction (did we predict above or below the real price?)
* as this error can be positive or negative, when summing all errors to see how much we are wrong we do not want negative errors kind of cancel positive errors. That is why the error is squarred so that the error will always be a positive number.
* those squarred errors are then summed and this number is then divided by the number of elements for which we are making prediction, so it is in the end a mean value for overall error.
* in the end we take the square root
{: .small style="text-align: justify;"}
One of the advantages (among others) it that RMSE gives higher weightage and punishes large errors (due to the power of 2).
You can then easily understand that **the lower this RMSE value, the best our model will be**. Our model will then have for objective to minimize this value.
{: style="text-align: justify;"}
 
### OK so now what is the model to use?
Actually I will not pick one model randomly but instead I will try a few:
* a very "naive" one that will return as predicted value the mean price of the neighbourhood the listing belongs to
* another very simple statistic model: a **linear regression** (it will be our reference model to compare to)
* a model based on **decision trees**
* a more complex model (but yet still based on trees) named **XGBoost**.
{: .small }

***Why?***

Naive model
:   Motivation for the naive model is to compare more complex models with something very simple that does not even require machine learning and see how much we do better (or not...).
I could have applied the mean price of the whole dataset, that would have been even more naive but let's challenge it a little bit as we have seen that price varies from one neighbourhood to another.  
As a customer I would expect a listing to cost something around the mean in the neighbourhood. Of course there are much more features that have impacts on the price (I guess that number of beds, accomodates and so on are part of that but let's keep things simple, it's a naive predictor after all...).
{: style="text-align: justify;"}
Linear Regression
:   It is a very basic model but which sometimes give good results and could be an outsider due to the computation time which is not very high.
{: style="text-align: justify;"}
Decision tree
:   The cool thing with trees is that it is possible to plot them, so explain them. Interpretability of the results is one of the major obstacles and challenges of Machine Learning[^10].
Well, with decision trees, we can do that because the algorithm is just a sequence of binary decision (if variable is higher than this value, go to right, else go to left, and so on). By plotting the full tree in the end we are able to understand why we arrived to such a prediction.  
More than that we are also able to have a look at features (i.e parameters) importance and see what are the ones that contribute the most to the prediction value.
{: style="text-align: justify;"}
Extreme Gradient Boosting (XGBoost)
:   XGBoost is also based on trees and has proven to be a very powerful machine learning technique which is often used on Kaggle competitions to solve problems.
{: style="text-align: justify;"}

If XGBoost is also based on tree, why trying it?  
Because I would like to try an _ensemble_ method: the key principle is that it builds a lot of simple decision trees instead of building a complex model that tries to understand very well the data or even finds relationships between columns --- that would obviously lead us to something hard to understand.  
Each tree, taken independently is very simple but the strength here is that combining predictions coming from all those basic estimators will provide in the end a stronger model, so a better prediction.  
There are several ensemble technics[^11]. **With XGBoost** training is sequential and **trees are added over iterations in order to reduce the errors made by the combination of all the previous ones**.
{: .notice--info style="text-align: justify;"}

As an example, here is what we can produce with _Decision Tree_:
<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/decision_tree_sample.png" alt="Decision Tree">
  <figcaption>We can plot Decision Tree so that it is easier to interpret results</figcaption>
</figure>
***Note:*** we can specify the depth of the tree (i.e number of levels) and in the end, in leaves, you can see that <var>value</var> corresponds to the predicted price.
{: .small }

### The results (at least!)
#### Algorithms predictions
After a mandatory data preparation phase and some little transformations to handle missing values, here are the first results I got:
<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/ml_results_table.png" alt="Algorithms results">
  <figcaption>First 15 price predictions for each of the algorithms</figcaption>
</figure>
***Note:*** 1st column is the real price whereas following ones are the predicted price for each of the tried algorithm.
{: .small }

On the 15 displayed rows, **most of the time the XGBoost prediction is not very far from the truth**.  
**BUT** sometimes it is totally wrong. For example the 540&euro; prediction while the truth was 119&euro; (line 7).  
It is interesting to note that DecisionTree (_y_dt_) and LinearRegression(_y_linear_reg_) are wrong as well with also big difference (though XGBoost remains the worst).
{: .notice--warning style="text-align: justify;"}

To go further and perhaps having a better general intuition about how good or how bad are those results, I have decided to classify predictions into different groups based on the error percentage between prediction and ground truth.
The motivation for that is that it is not the same thing when you are wrong of 40&euro; when the actual price is 100&euro; or when it is 1000&euro;.  
{: style="text-align: justify;"}
Here are then the 6 categories:
* 1-very accurate (prediction is 2% less or more than the truth)
* 2-pretty good: 2 to 5%
* 3-good: 5 to 10%
* 4-acceptable: 10 to 20%
* 5-bad: 20 to 40%
* 6-awful: more than 40%
{: .small }

#### Algorithms predictions classification
<figure style="width: 800px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/ml_models_results.png" alt="Algorithms predictions classes">
  <figcaption>Algorithms predictions have been classified into 6 groups</figcaption>
</figure>

<div class="notice--info">
	<p><b>XGBoost is our best candidate for further tuning</b>. We can see how it reduced the number of elements in class 6 (worst ones) in comparison to other models.</p>
	<p>Classes 1 to 4 counts for 48.75% with XGBoost. It means than once in two our best model is at least wrong about 20% the real value of the price.</p>
</div>

I said earlier that with trees it is possible to analyze what were the features (i.e columns in the data table) that had the most importance in the tree building.  
Here is an example for XGBoost:
{: style="text-align: justify;"}
<figure style="width: 800px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/ml_xgb_feat_importances.png" alt="Feature importances for XGBoost">
  <figcaption>Most important features XGBoost found</figcaption>
</figure>
***Note:*** you can read 0.04 value by saying that 4% of the price is explained by this or that feature. For example the number of bathrooms (4th position) explains 2.5% of a listing price.
{: .small }

### Tuning phase
Next step is to select the best candidate and **tune** it.
<figure style="width: 350px" class="align-left">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/clockwork-cogs.jpg" alt="Clockwork-cogs">
  <figcaption>Credit: Pixabay - Pexels</figcaption>
</figure>
Machine Learning algorithms may have 1 to a lot of parameters (so called hyper-parameters) that the DataScientist can play with.  
The tuning phase consists in trying to change one, few or all of them and see if results are improved.  
{: .small style="text-align: justify;"}
As you can easily imagine, depending on the number of hyper-parameters this task can be very time consuming.  
Moreover we will never have the absolute warranty that in the end the hyper-parameters combination found would be THE best one, it will just be the best among all our tries.
_There are technical ways to optimize the search but it is far beyond the scope of this post._  
{: .small style="text-align: justify;"}
Fortunately (or not), XGBoost has a lot of hyper-parameters (that is one of its drawbacks) so this tuning phase can very soon turn itself into a never ending one...
{: .small style="text-align: justify;"}
<br />
Anyway, in order to improve our good but not totally satisfying results, tuning has been made with XGBoost and several techniques (that will not be detailed here but which you can retrieve in the code repository[^12]).
{: style="text-align: justify;"}

#### Tuning results
Several tuning techniques (so models) were tried. Here are the predictions classification results:
<figure style="width: 800px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/ml_models_results_tuning_phase.png" alt="Results after XGBoost tuning">
  <figcaption>XGBoost tuned models results compared to previous models</figcaption>
</figure>
In spite of all the efforts made for parameters tuning, I was unable to improve significantly the results I got with the first try.
Even if I found a combination that slightly improved the RMSE value, when analyzing in more details the first XGBoost remains the best choice as it is with it that most of the prices are near the truth.  
{: style="text-align: justify;"}

To improve results, here are some things one could do:
* Try another model, for example [Catboost](https://catboost.ai/) from the russian company Yandex (as we have a lot of categorical features) and they say that there is less parameter tuning.
* Try to reduce the number of data input features with dimensionnality reduction techniques (for example PCA).
* Perform feature engineering by building new features.
* Create an ensemble of models, stack them then average the results.
{: .notice--info style="text-align: justify;"}

---
# WRAP-UP
Based on all our observations within provided data we have seen that:
* It will cost you more or less **116&euro;/night in average** but this **varies a lot depending on the neighbourhood** where you plan to stay.
* The **most expensive neighbourhoods are the one in the center of the city**, you will pay less and less gradually as you go further but be careful because in those neighbourhoods, the availability rate is not very high. You will find apartments in which people live most of the time and rent when they are away from their home.
* If money is an important criteria for your visit then **you can divide by 2 the mean price by choosing a shared room** and there are almost everywhere (but a few) in the city so that you are not stucked in a specific place.
* There are a **lot of places to rent in August and prices are lower** but this information is merely due to:
	* the fact that the data are from July, homeowners may have offered discount prices to engage website visitors
	* a lot of people living in Paris are in holidays somewhere else => more places, lower prices.
* The **other 2 periods with more availability** are in **early October or early January** but for the second period, it will cost you much more (+6% in average during Christmas holidays and it can increase until **+19% for New Year's Day depending on the neighbourhood**).
{: .notice--success style="text-align: justify;"}

Price prediction gave first encouraging results which still need to be improved through further investigations.
{: style="text-align: justify;"}
Even if it is quite frustrating not having improved the results, let's recall that the first goal of this project was to show our ability to follow the 
Datascience process and this is what has been done through the different steps: `Data Understanding`, `Business Understanding`, `Data preparation`, `Modeling`, tuning and `Evaluation`.  
And let's say that **to see the glass half full, we were able to build a model with 80% price prediction accuracy half the time**. Naive model was 80% precision 25% of the time and linear regression 33% of the time.
{: .notice--success style="text-align: justify;"}
---
<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/20190905/architectural-design.jpg" alt="Architectural design">
  <figcaption>Credit: Philipp Birmes - Pexels</figcaption>
</figure>


The world of data is a very fascinating one and you could never end to try to do something with data, interpret it.
{: style="text-align: justify;"}
Here you have read just an example of what is possible but we could imagine a lot of other things to try:
{: style="text-align: justify;"}
* we have all data since 2015, one could grab all data per year, keep listings that exists over all years and analyze trends (seasonal, recurrents). Why not timeseries forecasting?
* we have all the reviews, one could try to do NLP (Natural Language Processing) and classify reviews as positive/negative ones, try to detect recurrent topics per neighbourhood...
{: style="text-align: justify;"}
So, are you ready to give a try by yourself? Collect the data, it's [here](http://insideairbnb.com/get-the-data.html)!

Author: nidragedd
{: .small style="text-align: right;"}

---
If you would like to check out the whole project you can [see it from my Github repository](https://github.com/nidragedd/udadsnd-p4-airbnb_inside).  
You can also directly give a look at the [Jupyter notebooks](https://github.com/nidragedd/udadsnd-p4-airbnb_inside/blob/master/notebook/0_Introduction.ipynb) if you prefer.
{: .small }

---

[^1]: See Paris History on [Wikipedia](https://en.wikipedia.org/wiki/Paris#History)
[^2]: Paris is the most visited city according to [Telegraph.co.uk website](https://www.telegraph.co.uk/travel/galleries/the-worlds-most-visited-cities-where-does-london-rank/paris/)
[^3]: Paris is the 2nd most visited city according to a ranking compiled by [Mastercard Inc.](https://newsroom.mastercard.com/press-releases/bangkok-tops-mastercards-global-destination-cities-index-for-the-fourth-consecutive-year/)
[^4]: AlphaZero mastering games on [DeepMind blog](https://deepmind.com/blog/article/alphazero-shedding-new-light-grand-games-chess-shogi-and-go)
[^5]: A general reinforcement learning algorithm that masters chess, shogi, and Go through self-play on [Science](https://science.sciencemag.org/content/362/6419/1140.full?ijkey=XGd77kI6W4rSc&keytype=ref&siteid=sci)
[^6]: AI-Bot masters Texas hold'em poker on [Science](https://science.sciencemag.org/content/365/6456/885)
[^7]: Airflow tool [was born at Airbnb and is now under Apache Software Foundation](https://airflow.apache.org/project.html)
[^8]: Airbnb [technical blog about AI](https://medium.com/airbnb-engineering/ai/home)
[^9]: Paris, what price for your neighbourhood? (in french on [seloger.com](https://edito.seloger.com/actualites/barometre-lpi-seloger/paris-quel-prix-immobilier-dans-votre-arrondissement-article-31984.html)) 
[^10]: "Please, explain the black-box" by [Olga Mierzwa-Sulima](https://appsilon.com/please-explain-black-box/)
[^11]: [Ensemble methods: bagging, boosting and stacking, understanding the key concepts of ensemble learning](https://towardsdatascience.com/ensemble-methods-bagging-boosting-and-stacking-c9214a10a205)
[^12]: [Project code repository on Github](https://github.com/nidragedd/udadsnd-p4-airbnb_inside)