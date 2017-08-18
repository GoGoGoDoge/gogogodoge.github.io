---
layout: post
title: "Java-Algorithm-Binary-Search"
date: 2017-08-17
---

### {{page.title}} ###
It was dated back when I sat in the class to learn some algorithm stuff. Discover the computer world is so amazing and full of wit everywhere.

***
#### The stop matters! ####
Let us solve a puzzle. Where the lo stops?
```
    int[] arr = new int[]{1,3,5,7};
    int lo = 0, hi = arr.length - 1;
    int target = 2;
    while (lo < hi) {
        // Normal practise to avoid overflow
        int mid = lo + (hi - lo) / 2;
        if (arr[mid] < target) {
            lo = mid + 1;
        } else {
            hi = mid;
        }
    }
    // Puzzle what will lo be printed?
```
In the first case lo will be index 1, where we expect higher or equals to target

---

Any difference?
```
    int[] arr = new int[]{1,3,5,7};
    int lo = 0, hi = arr.length - 1;
    int target = 2;
    while (lo < hi) {
        // Normal practise to avoid overflow
        int mid = lo + (hi - lo + 1) / 2;
        if (arr[mid] > target) {
            hi = mid - 1;
        } else {
            lo = mid;
        }
    }
    // Puzzle what will lo be printed?
```

In the second case lo will be index 0, where we expect lower or equals to target

***
#### Let us go further ####
How about the target becomes 0. Things start to mess up. Both cases return 0 index.
But for the second case, the assumption lower or equals to target cannot stand...
It can be so error prone..

#### Not only index matter ####
Maybe we can use it to search for values? To be continue...

Try this out...

[Worker and Workload Pattern](https://leetcode.com/problems/split-array-largest-sum/description/)
