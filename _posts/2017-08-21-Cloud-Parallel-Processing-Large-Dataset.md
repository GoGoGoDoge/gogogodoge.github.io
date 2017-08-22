---
layout: post
title: "Cloud-Parallel-Processing-Large-Dataset"
date: 2017-08-21
---

<!-- ## {{page.title}} ## -->

I have a lot of fun doing this project. Not only do I know the entry-level of doing data processing but also find those powerful tools.

What conditions need distributed batch processing
1. Static data
2. Independent to each other
3. Not real time
4. unstructured data otherwise just data warehousing

## Batch Processing with MapReduce ##
Two tasks:
1. N-gram
2. Probabilistic Language Model  

Figure explains the procedure:

![Map Reduce](/files/MapRed.png)

Treats to save speed and space: use combiner which is identical to reducer but right after mapper. Therefore, it is like preprocessing.

Some background knowledge for fault-tolerant:
1. Data replication: underlying HDFS handle this
2. Task failure or slowdown
3. Node failures: slave node dies, lost heartbeat to master node. Primary node dies, use secondary master node (i think it is only available in yarn (hadoop 2.0))

This task was to Ngram Count Model:
1. 8 GB wikipedia 1 day dataset in XML format
2. I should not use hadoop stream but write our own batch processing program
3. Only need 5 grams, 1 gram : 'this' count 1, 2 gram : "this is" count 1 ...

Some special handling for the input data, I did it in the Mapper Class mainly:
1. Use utf-8 as encoding
2. Escape character <ref> like this... blahblah, i use `org.apache.commons.lang.StringEscapeUtils` to help me with the task
3. Removed html links

The idea of writing batch processing program for Mapper and Reducer:
1. A static class to extends Mapper class <"Input Key Format, Input Value Format, Output Key Format, Output Key Value Format">
    1. Override map() method
2. A static class to extends Reducer class <"Input Key Format, Input Value Format, Output Key Format, Output Key Value Format">

Instance used on AWS:
1. 6 large instances

---

Probabilistic Model:

Actually it is based on conditional probability.
![prob1](/files/prob1.png)

Tools used:
1. Hbase 1.3.0
2. Hadoop 2.7.3
3. AWS EMR (5.4.0)

Recommended schema for Hbase:
1. Phrase as row key
2. Words that possibly appear as columns and store the possibility in the cell

Some notes:
1. It was recommended use `TableMapReduceUtil` class to direct the output of Reducer class to HBase
2. I had hard time to connect to Hbase
    ```
    Configuration conf = HBaseConfiguration.create();
    conf.set("hbase.master", "your master node:16000");
    conf.set("hbase.zookeeper.quorum", "your zookeeper node normally same as master node");
    conf.set("hbase.zookeeper.property.clientport", "2181");

    // After we set up the configuration, get a job instance
    Job job = Job.getInstance(conf, "probability table");
    job.setJarByClass(ProbabilityTable.class);
    //I read from HDFS (mapper) (Use the defualt identity mapper class direct to output)
    job.setMapperClass(TokenizerMapper.class);

    // And then we can use table map reduce utilization job
    TableMapReduceUtil.initTableReducerJob(
            "words",        // output table
            TablePredictReducer.class,    // reducer class
              job);
    job.setReducerClass(TablePredictReducer.class);

    // Configure Map output part
    job.setMapOutputKeyClass(Text.class);
    job.setMapOutputValueClass(Text.class);
    ```
3. I use heap sort to get the most possible answers

---

Another exciting part of the project is I can use the expensive Redis. I directly write the whole database into Redis (key value data structural store).

Amazon ElasticCache is used for Redis.

I have to design my output format class for Redis after the reducer part. The difficulty also comes from here.
1. Write a class to extent `FileOutputFormat`, this is similar to HBase, I did not modify the key value type for the Reducer class but define my own FileOutputFormat then, hadoop will not output to hdfs but directly to Redis 
2. Override the getRecordWriter
3. Also requires a protected to extend RecordWriter
