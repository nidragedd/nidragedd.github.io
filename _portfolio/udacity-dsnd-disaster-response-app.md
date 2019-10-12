---
title: "NLP on disaster response messages"
excerpt: "Analyze and classify messages with NLP pipeline"
header:
  image: /assets/images/portfolio/disaster-pipeline.jpg
  caption: "Photo by [Yosh Ginsu](https://unsplash.com/@yoshginsu) on [**Unsplash**](https://unsplash.com/photos/qexZLgMcbPc)"
  teaser: assets/images/portfolio/disaster-pipeline-th.jpg
sidebar:
  - title: "Problem type"
    text: "Supervised learning classification"
  - title: "Models"
    text: "Logistic Regression"
  - title: "Project realisation date"
    text: "September 2019"
tags: 
  - Machine Learning
  - Natural Language Processing (NLP)
  - Spacy
  - TF-IDF
  - Logistic Regression
  - Pipelines
  - Flask
  - Udacity
classes: wide
---

_This project is the fifth of a series of seven projects to be delivered as part of the `Udacity DataScience Nanodegree` self-study._
{: .small style="text-align: justify;"}
Machine learning is critical to helping different organizations understand which messages are relevant to them and which
messages to prioritize.  
During these disasters is when they have the least capacity to filter out messages that matter, and find basic methods 
such as using key word searches to provide trivial results.
{: style="text-align: justify;"}
In this project our goal is to analyze thousands of real messages provided by [Figure 8](https://www.figure-eight.com/),
sent during natural disasters either via social media or directly to disaster response organizations.  
{: style="text-align: justify;"}
We have several steps to follow:  
1. **Build an ETL pipeline** that processes message and category data from csv files and load them into a SQLite database  
2. **Build a Machine Learning pipeline** that will then read from SQLite DB to create and save a multi-output supervised 
learning model (yes, this is multi-class classification problem). Goal is to categorize these events so that we can send
the messages to an appropriate disaster relief agency.  
3. Use **[Flask](https://palletsprojects.com/p/flask/)** framework to build a **webapp** that will:
* be able to first launch both pipelines in order to populate everything that needs to be
* provide some data visualizations
* use our trained and saved model to classify new messages for 36 categories.
{: style="text-align: justify;"}

**[spaCy](https://spacy.io/)** is the python package that has been used in order to perform some NLP preprocessing tasks such as **lemmatization**.  
Then a sklearn [TF-IDF vectorizer](https://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html) and some other transformers are gathered within a single [sklearn pipeline](https://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html).

In the given use case, this webapp is used by an emergency worker: he gives a new message and gets classification results in several categories.
{: style="text-align: justify;"}

_For more details, please refer to my [Github repository](https://github.com/nidragedd/udadsnd-p5-disaster_response_pipelines) for this project._