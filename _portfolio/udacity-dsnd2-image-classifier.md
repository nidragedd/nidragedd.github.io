---
title: "Flower image classifier"
excerpt: "Image classification with neural networks and PyTorch"
header:
  image: /assets/images/portfolio/image-classifier.jpg
  caption: "Photo by me"
  teaser: assets/images/portfolio/image-classifier-th.jpg
sidebar:
  - title: "Problem type"
    text: "Supervised learning classification"
  - title: "Models"
    text: "Neural networks (CNN)"
  - title: "Project realisation date"
    text: "July 2019"
tags: 
  - Machine Learning
  - PyTorch
  - Neural networks
  - Image classification
  - Transfer learning
  - CNN
  - Udacity
classes: wide
---

_This project is the second of a series of seven projects to be delivered as part of the `Udacity DataScience Nanodegree` self-study._
{: .small style="text-align: justify;"}
It is a **supervised learning classification problem** in which we have to develop an image classifier with **[PyTorch](https://pytorch.org/)**. For that we use **Tranfer Learning technique**: we build a new classifier by using already existing models and replacing the last classifier layer by a new one we train for our specific task.
This model has been trained on **GPU** and is able to classify flowers images among 102 possible classes (the flowers chosen to be flower commonly occuring in the United Kingdom).
{: style="text-align: justify;"}
The output is something like:
<img src="/assets/images/portfolio/uda-imageclassifier-prediction.png" />


The project contains 2 python scripts:
* one to train the model
* one to used a previously trained model to perform prediction


_For more details, please refer to my [Github repository](https://github.com/nidragedd/udadsnd-p2-image_classifier) for this project._

_PS: Before enrolling myself in the Udacity nanodegree, I previously made an image classifier with **[Keras](https://keras.io/)** in order to be able to classify dogs vs. cats: [here](https://github.com/nidragedd/image_classification)._
{: .small}