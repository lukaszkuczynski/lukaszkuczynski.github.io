---
layout: post
category: scala
tags: [scala, tour, introduction, learning]
title: Tour of scala started for me
---

## Motivation
I attended [Geecon 2018, this year's IT conference](https://2018.geecon.org).  There are 2 main points I took into consideration.  First, this is JVM, and as I can see there is a lot of that stuff running all around the world, so even if whole world would be magically converted to Python and stuff, it will take ages. And someone has to keep it running.  Second, this is the language of  [Spark](https://spark.apache.org/) and [Kafka](https://kafka.apache.org/), two of the projects of my interest in area of stream processing in Java world.  Therefore I would like to be more fluent and write these processing pipelines in the suggested language so I can make use of all of the features.

## First snacks
Scala is nice.  It is less verbose.  And as I can see it learned some nice features from Java applying in its syntax as a built-in (f.e. immutability).  This makes my first steps nice. Especially I like following:
- inferred types
- immutability
- in-line functions

## Learning
I decided to take [a tour of Scala](https://docs.scala-lang.org/tour/tour-of-scala.html).  And you can see the way I am learning in my repo at [github here](https://github.com/lukaszkuczynski/tour-of-scala). Learning is done by writing the unit test and checking its results with `sbt test`. Then I proceed to another point.  It helps me to:
1. understand points better
2. have fun while learning
3. make a step-by-step report, easy to review

This is nice.  There was a while to find the best tool, and to make it all up and running on my Windows PC, and to have it running on Intellij, but finally I succeeded.

This is a sample how do I play with that,
I write spec in Scala:
```scala
    it should "make Fenix singleton by it being an Object" in {
        Fenix.createOrRecreateFromAshes()
        val historyOfFenix = Fenix.createOrRecreateFromAshes()
        assert(historyOfFenix == 2)
    }
```
that uses following `object`:
```scala
object Fenix {
    var livesCount = 0
    def createOrRecreateFromAshes() ={
        livesCount += 1
        livesCount
    }
}
```
This and much more at my github, [you can check it, now worries](https://github.com/lukaszkuczynski/tour-of-scala).
