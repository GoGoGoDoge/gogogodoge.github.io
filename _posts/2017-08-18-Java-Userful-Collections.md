---
layout: post
title: "Java-Userful-Collections"
date: 2017-08-18
---

## {{page.title}} ##

There are some useful collection utilization:
* TreeMap
* TreeSet
* Deque (ArrayDeque)

---
### TreeMap ###
---

[Java Doc](https://docs.oracle.com/javase/7/docs/api/java/util/TreeMap.html)

TreeMap is implemented by Red-Black tree. Map is sorted according to natural order. Therefore, it is log(n) for retrieval.

It can actually return a navigable map. The following codes are credit by tutorial point.

The normal two apis are "headMap(keys strictly less than)" and "decendingMap"

```
package com.tutorialspoint;

import java.util.*;

public class TreeMapDemo {
   public static void main(String[] args) {
   // creating maps
   TreeMap<Integer, String> treemap = new TreeMap<Integer, String>();
   SortedMap<Integer, String> treemaphead = new TreeMap<Integer, String>();

   // populating tree map
   treemap.put(2, "two");
   treemap.put(1, "one");
   treemap.put(3, "three");
   treemap.put(6, "six");
   treemap.put(5, "five");

   // getting head map
   treemaphead=treemap.headMap(3);

   System.out.println("Checking values of the sorted map");
   System.out.println("Value is: "+ treemaphead);
   }    
}
```
Single Element API retrival "firstKey" and "lastKey". (They are not O(1))

Another four important apis are "floorKey(inclusive less than or equal to key)", "lowerKey(strictly less)", similar we have "ceilingKey()" and "higherKey()"

---
### TreeSet ###
---

Very similar apis just without key:
* higher
* lower
* floor
* ceiling
* (Careful) pollFirst
* (Careful) pollLast

---
### ArrayDeque ###
---

Not thread safe! Not allow NULL elements. Linear collection support insert and remove at both ends.

Must use following to avoid misunderstanding:
* getFirst() //do not remove
* getLast() //do not remove
* peekFirst()
* peekLast()
* pollFirst() //remove can return null
* pollLast() //remove can return null
* offerFirst()
* offerLast()
