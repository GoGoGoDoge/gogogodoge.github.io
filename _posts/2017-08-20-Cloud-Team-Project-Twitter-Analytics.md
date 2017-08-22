---
layout: post
title: "Cloud-Team-Project-Twitter-Analytics"
date: 2017-08-20
---

<!-- ## {{page.title}} ## -->

This is a blog to introduce a team project in CMU with Fan and Eugene.

Background: We are given a large data set of twitter data.

## Query 1 ##

This is the first query to help set up heart beat and web service.

Requirement:
1. Set up two frontend server on AWS using m3.medium and not better than m4.large
2. Write a program to decrypt the message with key provided.
3. No ETL process for this query

Budget restricted:
1. m3.medium is $0.067/hour and elastic load balancer is $0.0225/hour

REST server (Connection maintained by the client side):
1. Pretty lightweight, "Undertow" (We focused on Undertow)
2. Jetty

Reason to select undertow:
1. Very easy to use
2. It is Java based.
3. Built-in handler: most important Path. where I can delegate to a handler based on the path of request.

Load Balancer Selections:
1. HAProxy 9000 rps
2. Nginx 6000 rps
3. ELB 26000 rps

For unit test: using curl
Tools to monitor: htop and iftop to check CPU and network utilizations

Outcome:
1. Around 30000 request per second (rps)

## Query 2 ##

Query Request:
1. One hashtag
2. number N
3. keywords

Response:
1. Top frequencies of user ids
2. frequencies of keyword provided

ETL Requirement (Hadoop Streaming):
1. Total Data Size is 1 TB, 150MB compressed, 1G for each json. Total 1000 json files.
2. [Mapper] Removed malformed tweets and remove not in the preset language setting
3. [Reducer] Remove in duplicate, and output formated data to be loaded into MySQL
4. Output dataset size is around 8GB.
5. It shall be noted that for HBase, we have special requirement, have the hashtag#user_id as the row key
6. [Loading] for mysql, scripting a sql in a file and use `time mysql -u root --password=tartans@1 --local-infile=1 < create_twitter_db.sql`
7. [Loading] for hbase, we use import tsv tool provided by hadoop `hbase org.apache.hadoop.hbase.mapreduce.ImportTsv`to convert it as hdfs file and then `hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles` into hbase

We worked on 7 m3.medium AWS instances.

Some optimization:
1. Pre-counting the frequencies
2. Preprocessing of sensitive words
3. [Hop Spot Problem] for HBASE! We noticed this using iftop. Hash rowkey (MD5). More distributed evenly.
4. Impractical input query! N = 1 million users...
5. Shorten the family qualifier
6. Force split region `create 'tweets','f',{NUMREGIONS => 4, SPLITALGO => 'HexStringSplit', DURABILITY => 'ASYNC_WAL'}`
7. load local data infile for my sql can be troublesome with unicode | may use mysqlimport instead
8. Choose the engine to be MYISAM (read)
9. Default char for MYSQL changed to UTF8mb4 (4 bytes UTF-8 unicode formating), otherwise emoji crash...
10. LRU caching :) Java can extends LinkedHashMap :) save a lot of methods

Main SQL used:
> SELECT hashtag_num,userid,text,hashtag_text FROM tweets WHERE hashtag_text = BINARY "banaSevmeyiAnlat";

## Query 5 ##

Requirement graph relationship:
user_id -> tid -> another user_id -> tid -> user id

We need to figure out the shortest path between two users.

Tricks to optimize:
1. Re-design schema, TID: lots of UID schema
2. Enable MYSQL cache function by increase query cache size `query_cache_size`
3. BFS
4. Two END BFS
5. LRU cache again
6. Multi-threading - though may not be super efficiency context switching overhead
