---
layout: post
title: Regression and Outliers and Beer
category: ml
tags: ml,machine learning,udacity,training,regression
---
## Distribution
It is all about data distribution. Before applying any machine learning algorithm you have to answer the question. Do I know what I want to know?  Having this question clear in mind is required when one wants to stay focused.   So what is your data distribution?  In lesson no 7 of [Introduction to Machine Learning]( https://mena.udacity.com/course/intro-to-machine-learning--ud120) a student is given several introductory tasks to comprehend is data distribution continuous or discrete one.  The discrete distribution can be likened to a categorical dataset with elements like human names or car brands.  The continuous one is connected to variables that can have any value within a range, like age or volume of something.
## Regression Parameters
Regression is a way to find the pattern in a continuous distribution.  Given X values (one or more features) you are finding what outcome it is producing.  People sometimes talk about a regression when saying: the more something happens the more some value grows.  The linear regression is about having the relationship between the two figures.  Say you want to know how much the air temperature affects the beer consumption.  For this study please check the next sections of this entry.    There are extensive and nice explanations what does a regression mean mathematically (f.e. [this blog]( https://eli.thegreenplace.net/2016/linear-regression/) or even simpler [here]( http://www.stat.yale.edu/Courses/1997-98/101/linreg.htm) ), so I will not go into details in this post.  But to be concise I can tell that I was taught about two main parameters of regression.  These are **slope** and **intercept**.  Because the regression is a mathematic formula it can be represented as the following figure shows
```
Y = a + bX
```
In `scikit-learn` this two parameters can be easily fetched, what I will show in the following section.
## Sklearn is **fit**ting
### Simple Example
Having data imported to a data science toolbox you can leverage the power of ‘scikit-learn’.  It has major machine learning algorithms already built-in, so you just have to follow a simple flow of:
- data preparation
- choosing X and y
- fitting your data
- the algorithm evaluation
- visualization (optional)

With sklearn doing so is as simple as the following (imports excluded):
```python
x = np.array([1,2,3,6])
y = np.array([2,4,6,12])
plt.scatter(x,y)
reg = LinearRegression()
X = x.reshape(-1,1)
reg.fit(X, y)
print("Score is %f" % reg.score(X, y))
a = reg.coef_
b = reg.intercept_
print("Regression calculated for equation y=ax+b. Params are a (slope)=%.1f, b (intercept)=%.1f" % (a,b))
```
For the complete notebook please refer to my [Kaggle kernel]( https://www.kaggle.com/panlukaszk/the-simpliest-linear-regression-ever).  Normally we will not use the training values (X and y) for scoring, but this snippet works just for a presentation purpose.
### The real story: Kaggle and Beer consumption
I was thinking which dataset will be a nice visualization of linear regression.  And this is how I reached *Beer consumption in Sao Paolo* [dataset]( https://www.kaggle.com/dongeorge/beer-consumption-sao-paulo). I did some calculations harnessing `scikit-learn` and visualization given by `matplotlib` integration built-in into `pandas`.  If you are interested in how was a data prepared and my steps please refer to my [Kaggle notebook]( https://www.kaggle.com/panlukaszk/beer-in-saopaolo).
## Outliers
Lesson no 8 from the course teaches about outliers.  It is an important factor, especially when calculating a linear regression.  Most times you can just take a look at data doing the scatter plot and you’re done.  It was the way in the *beer consumption* dataset.  I didn’t see any major anomalies so no *outliers removal* technique was used.  But it is good to remember that we do have them, to make use of this when needed.   During this lesson, I saw how practical outlier removal is when dealing with *Enron* data.
## Summary
Lessons 7 and 8 of Udacity’s Intro to Machine learning focused my attention to continuous distributions.  I had a chance to work with real-world data and the outcome was refreshing my [Kaggle account]( https://www.kaggle.com/panlukaszk).  It was another proof of viewing Python as a primary language for data analysis.  Of course, I continue update of [GitHub repo]( https://github.com/lukaszkuczynski/ud120-projects ) that serves as an insight into my coding while studying this course.
