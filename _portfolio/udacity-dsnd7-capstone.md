---
title: "Churn prediction on Big Data"
excerpt: "Predict when digital music service users might churn"
header:
  image: /assets/images/20191031/goodbye.jpg
  caption: "Photo by [Jan Tinneberg](https://unsplash.com/@craft_ear) on [**Unsplash**](https://unsplash.com/photos/gJJhG4gM7NA)"
  teaser: assets/images/portfolio/goodbye-th.jpg
sidebar:
  - title: "Problem type"
    text: "Supervised learning classification"
  - title: "Models"
    text: "Gradient-Boosted Tree"
  - title: "Project realisation date"
    text: "October 2019"
tags: 
  - Machine Learning
  - Gradient-Boosted Tree
  - PySpark
  - Churn prediction
  - Udacity
classes: wide
---

_This project is the last of a series of seven projects to be delivered as part of the `Udacity DataScience Nanodegree` self-study._  
{: .small style="text-align: justify;"}
In this project our goal is to predict user churn. We work for a fictitious company called _"Sparkify"_, a digital music service similar to famous Spotify or Deezer.
{: style="text-align: justify;"}
We are provided 2 datasets in JSON format:
<ul>
	<li>a "tiny" one (128MB although)</li>
	<li>a full dataset (12GB)</li>
</ul>
Note that if the dataset can be easily used on a single machine to start the exploration phase, it is not the case for the full one as it contains millions of events.
We are in big data context and so we have to use appropriate tools.<br />
<b>To use the full dataset one has to build a Spark cluster on the cloud (AWS, IBM, whatever).

You cand find here [my blog post](/churn-prediction-keep-your-customers-by-your-side/) about this project.

_For more details, please refer to my [Github repository](https://github.com/nidragedd/udadsnd-p7-churn_prediction) for this project._