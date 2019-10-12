---
title: "Customer segmentation"
excerpt: "Unsupervised learning clustering K-Means and PCA"
header:
  image: /assets/images/portfolio/customer-segmentation.jpg
  caption: "Photo by [Ryoji Iwata](https://unsplash.com/@ryoji__iwata) on [**Unsplash**](https://unsplash.com/photos/IBaVuZsJJTo)"
  teaser: assets/images/portfolio/customer-segmentation-th.jpg
sidebar:
  - title: "Problem type"
    text: "Unsupervised learning clustering"
  - title: "Models"
    text: "Principal Component Analysis (PCA) and K-Means"
  - title: "Project realisation date"
    text: "August 2019"
tags: 
  - Machine Learning
  - Principal Component Analysis (PCA)
  - K-Means
  - Clustering
  - Udacity
classes: wide
---

_This project is the third of a series of seven projects to be delivered as part of the `Udacity DataScience Nanodegree` self-study._  
{: .small style="text-align: justify;"}
### Context
In this project, we are given real-life data provided by a german company (note that as those data are confidential they are not provided within the repository).  
The data concerns a company that performs mail-order sales in Germany. Their main question of interest is to identify facets of the population that are 
most likely to be purchasers of their products for a mailout campaign. 
{: style="text-align: justify;"}
### Objective
Our job is to use **unsupervised learning techniques** to organize the general population into **clusters**, then use those clusters to see which of them comprise
the main user base for the company.  
But, as there are many features, prior to applying the machine learning methods, we need to assess and clean the data in order to convert the data into a 
usable form and then apply **Principal Component Analysis (PCA)** to find the vectors of maximal variability.
{: style="text-align: justify;"}
Finally, on our transformed data, we can apply clustering techniques to identify groups in the general demographic data. And then we are able to apply the 
same clustering model to the customers dataset to see how market segments differ between the general population and the mail-order sales company. **K-Means method**
is used to cluster the demographic data into groups.
{: style="text-align: justify;"}
_For more details, please refer to my [Github repository](https://github.com/nidragedd/udadsnd-p3-identify_customer_segments) for this project._