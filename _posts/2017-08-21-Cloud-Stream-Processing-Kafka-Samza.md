---
layout: post
title: "Cloud-Stream-Processing-Kafka-Samza"
date: 2017-08-21
---

<!-- ## {{page.title}} ## -->

Stream processing is designed mainly for real time data, i.e. extreme latency and throughput requirement.

How the traditional data processing works:
1. Collected data stored offline
2. ETL job to multiple systems with output format defined

## Apache Kafka ##

Kafka is a distributed publish subscribe messaging system.

> "The main advantage of Kafka is the fact that it provides a unifying data backbone from which all systems in the organization can consume data independently and reliably."

> "Kafka being a streaming data source"

1. Topics

    Topics represent a user-defined category to which messages are published.

2. Producers

    Producers are processes that publish messages to one or more topics in the Kafka cluster. Actually, they are talking to brokers

3. Consumers

    Consumers are processes that subscribe to topics and read messages from the Kafka cluster by talking to brokers

4. Partitions

    Topics are divided into partitions. A partition represents the unit of parallelism in Kafka. In general, a higher number of partitions means higher throughput. Within each partition each message has a specific offset that consumers use to keep track of how far they have progressed through the stream. Consumers may use Kafka partitions as semantic partitions as well.

5. Brokers

    Brokers in Kafka are responsible for message persistence and replication. Producers talk to brokers to publish messages to the Kafka cluster and consumers talk to brokers to consume messages from the Kafka cluster.

6. Replication

    Kakfa uses replication for fault-tolerance. For each partition of a Kafka topic, Kafka will choose a leader and zero or more followers from servers in the cluster and replicates the log across them. The number of replicas including the leader is determined by a replication factor. The leader of the partition will handle all the reads and writes while the followers consume messages from the leader in order to replicate the log. Since both leader and follower may fail when a server in the cluster fails, the leader keeps track of alive followers (in-sync replicas (ISR)) and removes unhealthy ones. In this case, if the leader dies, an alive follower will become the new leader of this partition. This mechanism allows Kafka to remain functional when failures exist.

## Apache Samza ##

Samza is a distributed stream processing framework developed by LinkedIn. Samza is designed to continuously compute data as it becomes available and provides sub-second response times. Samza is a key component in a three-tiered stream processing architecture:

1. Streaming Layer

    The streaming layer provides the input in the form of partitioned streams. As you can imagine, this streaming layer that we will use in this project is Apache Kafka.

2. Execution Layer

    The execution layer schedules and coordinates tasks across machines. In this project, we will use YARN as the execution layer.

3. Processing Layer

    Finally, the processing layer processes the input stream and applies transformations on the input stream to produce some type of output. This is where Samza comes in.

Like Kafka, there is some terminology to define in Samza:

1. Streams

    A stream is composed of partitioned sequences of messages belonging to a user defined category. As mentioned previously, Kafka can be our stream provider and a Samza stream and a Kafka topic are equivalent.

2. Jobs

    A job is written using the Samza API to read and process data from one or more streams. A job may be broken into smaller units of execution called tasks. Each task may consume data from one or more partitions in the input stream.

3. Stateful stream processing

    Stream processing can be broadly classified into two types: stateful and stateless. Based on the existence of some type of state which gets mutated as the stream is processed. Stateless stream processing is fairly simple as there is no synchronization of state required as stream partitions are processed in parallel. However, Samza allows for stateful stream processing. The typical approach to support stateful stream processing is to have a remote data store (such as MySQL, HBase) and retrieve state from the database in the Samza job. However this creates an unacceptable bottleneck in the system.

## Course Work Project ##

It is called cab ride share project. Drivers and Customers real time share theirs location and preference. Design a system to provide matching based on the provided information.

Two topics (or streams in Samza terminology):
1. driver-location
    1. Stream Block id
    2. Driver id
    3. Latitude and Longitude

2. events
    1. RIDE_REQUEST
    2. ENTERING_BLOCK
    3. LEAVING_BLOCK
    4. RID_COMPLETE

The idea is to process the two stream and produce score stream.

Mainly class to implement `StreamTask, InitableTask, WindowableTask`

1. There are `init`, `process` functions
2. Some core steps:
    1. Get Stream
    2. Get message (which is json) can be cast to Map<Key String, Value Object>
3. Partition by block id
