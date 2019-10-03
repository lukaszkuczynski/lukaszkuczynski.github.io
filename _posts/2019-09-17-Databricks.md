---
layout: post
title: Spark is Pandas on steroids
category: data
tags: data,pandas,spark,databricks,azure
featured-img: panda
---

Read it if you fell in love in Pandas, and started to think about whether parallel processing can be easy

## Who are you
Let me guess.  You are a data scientist who runs preprocessing and modeling on your machine using pandas and other cool libraries.  Or you are just a mature software engineer who once heard about parallel processing but have never seen the real use-case for that.  Even if you don’t belong to either group think of it: 
> why do I need parallel processing? 

Why it may be of your concern, even if you did not realize that before?  Let me share with you my experience.
Lately, we had a pipeline when `fbprophet` was used to predict several thousand of series.  It took ages to complete the whole process.  A thought: maybe running it on some Azure fast computing set will speed it up?  Yes, but still it is hours of waiting.  So what is the bottleneck here?  `fbprophet` and all of that processing is one-machine solution.  You cannot just spread it through nodes to be processed in parallel. But it would be nice, huh?  You say: these 10k rows go to compute #1, the other 10k rows go to #2 and so on.  Want to make it happen?
## Spark is the answer
Apache Spark was created to make parallel processing possible for you and me.  But what was before?
The era of Hadoop was there.  The big ecosystem of tools to process big data.  Maybe now some of the experts claim: it is out of date, but it was a big leap in the future - you can **split your big job into smaller chunks and send them to be processed on several machines**.  Then the result is collected and here you go!  

You can utilize the commodity machines you have without a need to invest in a top-class machine, which can be very expensive.  You can scale out (as opposed to scaling up)!  But to use Hadoop there were some limitations involved. You had to stick to MapReduce and Java language which was the only building and API language.  Moreover, all your computation steps were using disk heavily, which was one of the biggest bottlenecks of the architecture.

So this is why Spark was introduced.  The main advantage of this engine is - as compared to Hadoop - processing all of its data in memory.  And you do not think in terms of the machine which drives the process (*driver*).  You can instantiate as many *workers* as you need.  All of these - making up a *cluster* - are quite a powerful tool in your hands.  What is more, you are not glued to Java-based MapReduce, but there is nice DataFrame API at hand, having support for multiple languages, like Python, Scala, Java, R, and even SQL.
Sounds expensive and hardly available?  On the contrary!
## How can I get it
### Spark or hosted Spark?
You could install Spark on your machine.  But why if you may try it online?  For free!  No more installing binaries on your machine to try something out.  Even there is no need to have docker daemon running somewhere in the background.  Why?  There is a blessing (or a curse) of PaaS and SaaS abundance all around you.  So how about you. Would you like to try Spark yourself?
### Databricks
Let me introduce Databricks.  This is a product available online, created for widely-understood analytics. It is also hosted by Azure (Microsoft cloud offering), where you can just try it and run.  I mentioned Azure here because it was the first place when I encountered Databricks. 

Especially for the sake of this article I created a new account on community version of Databricks to check how easy it is.  And yes, it took me roughly 2 minutes to create and start my first Databricks account without any credit card information and so on, just fill in the form [on this page](https://databricks.com/signup/signup-community).When you have your cluster ready, just may play with it using Spark API.
## use-case
I do not like learning for learning.  I like to notice how some tools empower me to solve the problem faster.  Maybe as an IT-person, you were traversing long, boring log files to find out what happened to the application.  You have Gigabytes of logs and you wished to see patterns in it.  You may continue grepping, but no… how are you going to make it happen with Spark?  

You don’t have to (however you may, if you wish) to upload your own log files to start playing with it in Community version of Databricks.  There are many datasets already available on Databricks to use them, including sample log files.
First, we will read them all to the structure called RDD:

```python
rdd_original = sc.textFile('/databricks-datasets/sample_logs/')
rdd_original.take(10)
```
The previous command just reads all files from the location provided to an abstraction called RDD.  This one can be used for further transformations (map/reduce/filter) or transformed into a data frame.  There were some operations done to parse data out of file rows.  For the sake of simplicity, I skip it in this article - the full notebook however [is also available there](https://lukaszkuczynski.github.io/assets/logs-databricks.dbc). To create a data frame, we just need to provide the schema.

```python
from pyspark.sql.types import StructField, StructType, IntegerType, StringType, TimestampType

schema = StructType([
  # your data types definitions go here
])

df = rdd_mapped.toDF(schema)
```
With a data frame ready we may start doing some aggregations and draw conclusions.  For example, you may be interested in how often different errors occurred for specific users.  We may learn about it using Python API or SQL.
```python
df_identified = df[df['user'] != '-']
df_grouped = df_identified.groupby(['user','status']).count()
```
For you, my dear Pandas developer, these operations should look pretty familiar, am I right?
Of course, you may express your query with SQL syntax.
```python
df.createOrReplaceTempView('log')
```
```sql
select user, status, count(*)
from log
where user <> "-"
group by user, status
```
Databricks gives you not only Spark on Cloud experience, but also contains basic visualization capabilities.  I encourage you to use them for your scientific purposes, not as a tool to present data to your customers.

## Summary
The data analysis done here is available publicly for some period of time under [this public link](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/3276244303656844/2182517269869031/287949548447532/latest.html).  I also uploaded that as [a downloadable asset here](https://lukaszkuczynski.github.io/assets/logs-databricks.dbc).
With Spark features and DataFrame API we can do really a lot. What is important the full advantage you can see when dealing with big amount of data that is hard to ingest by a single machine.

