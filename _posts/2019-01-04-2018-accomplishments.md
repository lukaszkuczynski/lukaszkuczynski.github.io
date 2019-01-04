---
layout: post
title: 2018 Accomplishments
tags: accomplishment,resolution
---

## what is in the past
It is good to 'find enjoyment for all your hard work'.  I can see many people doing their year summaries.  Obviously, there are areas more important for than job matters, but here I will focus on business only.  What has changed?


### machine learning
There was a lot of learning this year.  I always thought this is too much for me.  I always liked Math, but when I started to dig into the math behind the machine learning the task seem to be daunting.  But the Udacity and Pluralsight brought much to the table.  Especially the [Udacity one]( https://dev.to/lukaszkuczynski/machine-learning-with-udacity-549e).  With simple examples and lots of pictures, they were able to explain difficult matter easily.  I immediately thought about the business use cases.  So there was a need and motivation.  I took some data and I was successful to apply this knowledge in my 2 projects.  Both were about Text Analysis.  The first was supervised machine learning with automatic team assignment for ticketing tool we have.  I was able to achieve pretty high accuracies for teams that had distinct areas of responsibility.  The second was applying clustering as an example of unsupervised learning with the famous K-Means algorithm.  Of course, I cannot share this data.  But when playing with my machine learning basics I am happy to share some of insights in my Kaggle account based on public datasets.  You can see some of my kernels [here]( https://www.kaggle.com/panlukaszk).  By the way, learning about Kaggle is another big event for me this year as this is a great place to share my knowledge and progress.  But not only this, I can gain inspiration from others, too.  Some of the posts are proof of this, check [this about beer in Brazil]( https://dev.to/lukaszkuczynski/regression-and-outliers-and-beer-57k8).


### cloud
This year I understood some of the good aspects of “everything into the cloud” hype.  When thinking of one’s local or on-premises environment I can see problems with scalability and lack of fluency.  Everything seems to be so hard to get when for every change you have to raise the incident to a dedicated team.  How different it is when using cloud infrastructure!  This year I had a chance to work with Openshift as PaaS.  All you need is to close your dependencies inside of a Docker image and reuse it whenever you need.  

I also played with big guys on the market: AWS and Azure.  I even made a little comparison of their services deploying similar infrastructure on both of them, because I wanted to compare their Serverless support.  I built a simple notification engine leveraging both [Azure Functions]( https://dev.to/lukaszkuczynski/serverless-monitoring-of-weather-with-azure-4d89) and [AWS Lambdas]( https://dev.to/lukaszkuczynski/monitoring-wrocaw-weather-with-aws-903). 

The main part of my current billable assignment was to conduct several data analysis tasks.  I will take a closer look at it the next section but I can tell that without Azure Databricks this journey would not be that easy. 

In the cloud these my 3 favorites:
- it is unlimited
- pay as you go
- don’t care about the infrastructure


### data analysis
As I mentioned before I started to use Spark some years ago, but that time it was – to put it mildly – not so justified.  So what has changed lately?  I was given several tasks to make use of big data we have in our log files when it comes to user activity and I noticed when does the parallel computing unleashes its power: when the volume is greater than Mbytes.  Of course, it is not yet the TB of processed data but we are getting there.  Spark is cool when you run it somewhere where the platform is available.  In my current assignment, I can leverage the power of Databricks, as we have some Azure account to utilize.  Personally, Databricks make things really easy and fast.  Why?

First, I love playing with Python notebooks.  It makes your job so descriptive, I think the idea of mixing code with some textual explanations is great.  

Second, it is so nice to use the flexibility of fast provisioning available there.  You just choose the cluster you need and it is ready within minutes.  If you are dissatisfied – or you are running out of money! – just dismiss the one.  Simple as that.

I was able to gain really nice insights that were hidden before the stakeholders, so then we can do the decision process better.  We could answer the following questions:
what are the areas are a user’s favourite?
how a user is responding to a new feature?
what are locations of users, and does it affect their choices?

Gaining insights from data is important and I think data analysis with a help of Azure Databricks play is crucial here.  This work can open the eyes of decision-makers to some challenges or successes.  It can make complicated data an easy one.

### more blogging 
This year I could do some blogging.  Only at dev.to I published 11 entries starting May 2018.  This year I left my [Wordpress account](http://lukcreates.pl/) for the sake of [Github pages]( http://lukaszkuczynski.github.io) portal which I Ioved because I can write markdown only, and I like git-pushing of my blog entries.  Not only I was able to write about my progress but also I could report 2 nice events I was part of, [Python Conference in August PyConPL]( https://dev.to/lukaszkuczynski/pyconpl-2018-report-2loo) and the internal [AI days at Volvo]( https://dev.to/lukaszkuczynski/innovation-day-at-volvo-1l64).
Of course, dev.to plays a very important role here because the community there is great and I like the User Experience when dealing both with content and its design.

## what comes next?
What about my business-related plans for this year?  In the 2019 year I do **not** plan to learn any new language.  I am **not** to going to change my direction.  Rather I would like to continue the progress I started 2018 year.  The year that I understood even more my change from Java to Python was a good choice.  With Python I feel I am young again, I like programming and writing the code I feel like a poet now, not like a journalist forced to write a few articles before the end of the month.

I am going to read new books, I don’t know which yet... But for sure there is a goal to finish the great one I started to read lately [about storytelling with data]( https://www.amazon.com/Storytelling-Data-Visualization-Business-Professionals-ebook/dp/B016DHQSM2).  Wow, it really opens my mind to the fields that are so green.  I want to finish the Udacity in February and start playing with text data, seriously.  By the way, you can check my progress of it on a [dedicated git repo]( https://github.com/lukaszkuczynski/ud120-projects/commits/master).

To keep things short:

I want to be a help and use the help of others.  
I want to communicate to bring results.
I want to make things simpler with visualization
I want to automatize to make things faster.