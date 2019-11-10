---
title: "Recommender system"
excerpt: "User Collaborative Filtering or Content-Based recommendations"
header:
  image: /assets/images/portfolio/recommender.jpg
  caption: "Photo by [Frame Harirak](https://unsplash.com/@framemily) on [**Unsplash**](https://unsplash.com/photos/Ei_lQ6kTwiI)"
  teaser: assets/images/portfolio/recommender-th.jpg
sidebar:
  - title: "Problem type"
    text: "Recommender system"
  - title: "Machine Learning"
    text: "Singular Value Decomposition (SVD)"
  - title: "Project realisation date"
    text: "October 2019"
tags: 
  - Machine Learning
  - Recommender system
  - User Collaborative Filtering
  - Content-based recommendation
  - SVD
  - Udacity
classes: wide
---

_This project is the sixth of a series of seven projects to be delivered as part of the `Udacity DataScience Nanodegree` self-study._
{: .small style="text-align: justify;"}
In this project we analyze the interactions that users have with articles on the IBM Watson Studio platform, and make 
recommendations to them about new articles we think they will like.
{: style="text-align: justify;"}
This project contains different kind of recommendations:  
1. **Rank Based Recommendations**: find the most popular items simply based on the most interactions. These are then the articles we might recommend
to new users (or anyone depending on what we know about them).  
2. **User-User Based Collaborative Filtering**: look at users that are similar in terms of the items they have interacted with.
These items could then be recommended to the similar users. This would be a step in the right direction towards more personal
recommendations for the users.
3. **Content Based Recommendations**: use NLP to develop a content based recommendation system.
{: style="text-align: justify;"}
In this project we also implement **Matrix Factorization (SVD)**, a machine learning approach to building recommendations.  
Using the user-item interactions, we build out a matrix decomposition and using this decomposition, we can get an idea of how well we can predict new items an individual might interact with.
{: style="text-align: justify;"}

**[spaCy](https://spacy.io/)** is the python package that has been used in order to perform some NLP preprocessing tasks such as **lemmatization**.  

_For more details, please refer to my [Github repository](https://github.com/nidragedd/udadsnd-p6-recommendation_engine) for this project._