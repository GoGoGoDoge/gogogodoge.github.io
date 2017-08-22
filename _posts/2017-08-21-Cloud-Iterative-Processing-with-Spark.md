---
layout: post
title: "Cloud-Iterative-Processing-with-Spark"
date: 2017-08-21
---

<!-- ## {{page.title}} ## -->
### Just Spark No Fire Very Safe ###

This is the first time I encountered Spark. I did not fall in love but just turned around to did my Page-Rank.

Although Java and Python are allowed, the Scala is more recommended when writing Spark application.

---

## Some background ##

1. Why Spark, I enjoy hangout with MapReduce? The answers are:
    * the word "iterative". The application iterate over the data again and again until it meets some condition (converge for example). Therefore, we do not want the output write to the disk first and collect it for next processing.
    * the word "stream", some data is real time, requires short response latency.

    Therefore, Spark solve the problem using in memory primitive.

2. RDD (Resilient Distributed Dataset), consider it as abstraction, not really executed unless invoked
3. Once SparkContext connects to ClusterManger. Spark then acquires executor on the worker nodes. Each application has its own executor process, good to isolate but difficult to share.

Therefore, the trick to do task faster depends on how we decide where to invoke RDD and what shall be kept into memory.

Other suggestions from course:
1. Shuffle is very expensive. Less `join`
2. Do not use RDD action unless necessary, copy a big RDD to driver could be nightmare and run out of memory `.collect()`
3. Tricks for sorting, sort by value, we can `sortBy` first and then `sortByKey`. The is sortBy is like doing sorting within the keys
4. Use `reduceByKey` instead of `groupByKey`, the latter one is slower. Like the combiner in Hadoop.

Input file size:
1. 10.4GB
2. Edge list format (Follower \\t Forllowee) Just number :)

---

First task is just warm up. Count the total vertices and edges. It shall be noted that is directed graph, (u,v) is different from (v,u)

I tried both RDD and Dataframe API of scala.

RDD:
1. `map` function
2. take order
3. print output to convert iteration to array `parallelize`

Dataframe:
1. configure `SparkSession`
2. Prepare RDD
3. do RDD and to DF (I think this is not the correct approach)

---

## Page Rank ##

The idea is the bigger the score is, the more influential the node is. Its score is contributed by its follower

There are matrix multiplication solver and for-loop solver for each iteration. In the project, I implemented the second one.

![forloop](/files/forloop.jpg)

Basically, I follow this clear [example](https://s3.amazonaws.com/15619public/webcontent/pagerank_examples.pdf).
