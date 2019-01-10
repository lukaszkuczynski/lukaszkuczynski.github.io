---
layout: post
title: Clustering snacks and vegetables
category: ml
tags: ml,udacity,clustering,training,kmeans
---

## Supervised vs Unsupervised:
Lesson no 9 of Udacity's [Introduction to Machine Learning](https://eu.udacity.com/course/intro-to-machine-learning--ud120) showed another aspect of machine learning to me.  I could strengthen my knowledge in an area of clustering.  This is topic particularly interesting as is it reveals insights, which may inspire you to further analysis.  As I am lately into NLP, one possible use case is that it can group similar documents together and then you can discover the connections between them.  Supervised learning is about having labels and then checking if these labels fit features of newly acquired data.  Unsupervised learning has no comparison phase, as no labels are known at the beginning.  
## K-Means and sklearn
Clustering using K-means algorithm is one of the most widely used unsupervised techniques.  It is about finding such cluster centres that will allow the whole system to be in a “harmony”.  Distances are calculated using Euclidean distance.  
There is a nice visualization that was advertised during the training, you can take a look at fantastic work was done [at naftaliharris blog]( https://www.naftaliharris.com/blog/visualizing-k-means-clustering/)
As with every algorithm we have to be aware of its limitations.  One of these is the fact that K-means is a hill climbing algorithm.  This very fact has its own [Wikipedia page, that you can check]( https://en.wikipedia.org/wiki/Hill_climbing).  So the algorithm is very sensitive to local minima.  Thus, some specific way of choosing initial points (centroids) can lead to clusters we would like not to have.  This is why in sklearn implementation you are encouraged to do the clustering several times and then the best clustering is chosen.
## Use case : veggies vs snacks.
The Kaggle for the following [you can find here]( https://www.kaggle.com/panlukaszk/vegetables-vs-snacks).  I thought: maybe I could use clustering somewhere?  Maybe a computer can be smart enough to distinct junk food from a nice one? So I found this dataset, that is [the Australian Food Nutrient Database]( http://www.foodstandards.gov.au/science/monitoringnutrients/ausnut/ausnutdatafiles/Pages/foodnutrient.aspx).   I played around a bit, and you know what?  It works.  Of course, if I spent more time on the careful assignment of snack and vegetable categories it would be more meaningful, without that I have some outliers, like tomatoes with a lot of fat, as an example.  Anyhow KMeans was able to mark a clear distinction between high calories, low Vitamin C snacks and low calories, healthy vegetables.