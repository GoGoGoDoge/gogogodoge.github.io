---
layout: post
title: "Java-Sorting"
date: 2017-08-18
---

### {{page.title}} ###

Java uses quick sort for primitive data type while using merge sort for collection sort.

The reason is because merge sort is stable while quick sort does not guarantee.

***
#### Quick Sort ####

There are two ways to use it based on situation. One of them is invented by Dijkstra to tackle repeated elements.

By adding two more variables including the lt and gt, this turns out to be very fast.
---

Find the kth largest element in an array O(n)
```
import java.util.*;

// partition
// Very classical solution, must understand and write it out
public class Kth_Largest_Element_in_an_Array_threePartition {
    public int findKthLargest(int[] nums, int k) {
        if (nums == null || nums.length == 0) return Integer.MIN_VALUE;
        // Do shuffle first
        Random random = new Random();
        for (int i = nums.length - 1; i >= 1; i--) {
            int j = random.nextInt(i);
            swap(nums, i,j);
        }

        k = nums.length - k;
        int lo = 0, hi = nums.length - 1;
        int i = 0;

        return threePartition(nums, lo, hi, k);
    }

    private int threePartition(int[] nums, int lo, int hi, int k) {
        if (lo >= hi) return nums[lo];
        int lt = lo, gt = hi;
        int i = lo;
        int pivot = nums[lo];
        while (i <= gt) {
            if (nums[i] < pivot) {
                swap(nums, i++, lt++);
            } else if (nums[i] > pivot) {
                swap(nums, i, gt--);
            } else {
                i++;
            }
        }
        if (k >= lt && k <= gt) return nums[lt];
        else if (k < lt) return threePartition(nums, lo, lt - 1, k);
        else return threePartition(nums, gt + 1, hi, k);
    }

    private void swap(int[] nums, int i, int j) {
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }
}
```
