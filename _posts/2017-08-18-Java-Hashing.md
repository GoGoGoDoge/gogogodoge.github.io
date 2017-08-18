---
layout: post
title: "Java-Hashing"
date: 2017-08-18
---

## {{page.title}} ##

Hashing has some ways to tackle collision. Let us discuss about them.

Concept: Load Factor is the ratio of the number of data items in a hash table to the length of the array.

***

### Open Addressing ###

There are mainly linear probing, quadratic probing and double hashing.

#### Linear Probing and Quadratic Probing ####

It shall be noted that when load factor approaches 1, search time could be really slow. When collision happens, linear searching the next available slot to place data. For quadratic probing, it is jumps searching slot in 4, 9, 16 manners.

***

### Separate Chaining (Closed Addressing) ###

I append a linked list to where the collision happens in the hash array! In this way, I can theoretically exceed load factor 1. However, it is linear searching time in a linked list. We have to make sure this linked list is not too long.

### Interesting to point out umh... ###

1. Map interface in Java is not an extension of the collection interface
2. HashSet is slightly faster than HashMap in practice. HashSet implements the Set interface using HashMap instance and use a dummy object as the value part. 
