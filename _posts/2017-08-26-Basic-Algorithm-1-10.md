---
layout: post
title: "2017-08-26-Basic-Algorithm-1-10"
date: 2017-08-21
---

## Two Sum ##
1. 采用一个map记住index，然后扫后面的时候发现前面的first，match的时候，返回结果
2. 带上index以及value的class 进行sortting 然后用two pointer找到target值
```
这里用的是map的方法
public int[] twoSum(int[] nums, int target) {
        // 1. build a hash map to store index first
        Map<Integer, Integer> map = new HashMap<>();

        // 2. keep searching until I find a match
        for (int i = 0; i < nums.length; i++) {
            int first = target - nums[i];
            if (map.containsKey(first)) {
                return new int[]{map.get(first), i};
            }
            map.put(nums[i], i);
        }

        return new int[0];
    }
```
---
## AddTwoNumber ##
1. 有点类似merge，先历遍两个，如果一个linkedlist还有再历遍，还有carry继续历遍
```
这道题由于顺序是反过来的，所以比较好做，要注意处理carry的情况，因为最后carry可能是1
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        int carry = 0;
        ListNode dummy = new ListNode(0);
        ListNode cur = dummy;
        while (l1 != null && l2 != null) {
            int result = l1.val + l2.val + carry;
            cur.next = new ListNode(result % 10);
            carry = result / 10;
            cur = cur.next;
            l1 = l1.next;
            l2 = l2.next;
        }

        while (l1 != null) {
            int result = l1.val + carry;
            cur.next = new ListNode(result % 10);
            carry = result / 10;
            cur = cur.next;
            l1 = l1.next;
        }

        while (l2 != null) {
            int result = l2.val + carry;
            cur.next = new ListNode(result % 10);
            carry = result / 10;
            cur = cur.next;
            l2 = l2.next;
        }

        if (carry > 0) {
            cur.next = new ListNode(carry);
            carry = 0;
        }

        return dummy.next;
    }
```
---
## Longest Substring Without Repeating Characters ##
1. 这道题主要在于lastvalid start index
2. 还有采用map去记住上次更新的index

```
// 注意map用来存last index，在更新这个值值钱判断len，这个last index可能作为最新的last index，也有可能不产生影响
public int lengthOfLongestSubstring(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        int[] map = new int[256];
        Arrays.fill(map, -1);
        int lastIdx = -1;
        int len = 0;

        for (int i = 0; i < s.length(); i++) {
            // 与上次的值对比，其实有两种情况，上次的值并不比last valid start idx 大，上次的值比last valid start 大需要更新
            lastIdx = Math.max(lastIdx, map[(int) s.charAt(i)]);
            // 更新长度，如果可能的话
            len = Math.max(len, i - lastIdx);
            map[(int) s.charAt(i)] = i;
        }

        return len;
    }
```
---
## Median of Two Sorted Arrays ##
1. 方法一： 采用merge sort的思想来找，这个方法比较不容易出错，要注意的地方是计算median的时候需要总大小加一，如果是偶数要再找一个数然后求平均
```
方法一
// Adape merge sort idea, keeps finding until reach half
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        if (nums1.length == 0 && nums2.length == 0 ) {
            return 0.0;
        }
        int size1 = nums1.length;
        int size2 = nums2.length;
        int[] pos = new int[]{0, 0};
        int mid = (size1 + size2 + 1) / 2;
        int val = 0;
        for (int i = 0; i < mid; i++) {
            val = findNext(nums1, nums2, pos);
        }

        // if sum of size is odd, then val is the median otherwise find another and get average
        if ((size1 + size2) % 2 == 1) {
            return val;
        } else {
            return (val + findNext(nums1, nums2, pos)) / 2.0;
        }
    }

    private int findNext(int[] nums1, int[] nums2, int[] pos) {
        int res = 0;
        if (pos[0] == nums1.length) {
            res = nums2[pos[1]];
            pos[1]++;
            return res;
        }
        if (pos[1] == nums2.length) {
            res = nums1[pos[0]];
            pos[0]++;
            return res;
        }

        // both not reach the end just compare and return
        if (nums1[pos[0]] <= nums2[pos[1]]) {
            res = nums1[pos[0]];
            pos[0]++;
            return res;
        } else {
            res = nums2[pos[1]];
            pos[1]++;
            return res;
        }
    }
```
2. 采用分治的思想加上binary search的思想，这个方法比较tricky，巧妙的利用了乘以2以后的优点

```
方法二
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int m = nums1.length;
        int n = nums2.length;
        // 1: Make sure the m <= n
        if (m > n) {
            return findMedianSortedArrays(nums2, nums1);
        }

        // 2: Binary search for the result
        int k1 = 0; // represent the index in nums 1
        int k2 = 0; // represent the index in nums 2
        int l1 = 0; // the largest number from [0, k1] in nums1
        int l2 = 0; // the largest number from [0, k2] in nums2
        int r1 = 0; // the smallest number from [k1, nums1.length - 1] in nums1
        int r2 = 0; // the smallest number from [k2, nums2.length - 1] in nums2
        int lo = 0; // tricky part instead of searching from [0, m - 1], we search from [0, 2m] (2m + 1) range
        int hi = 2 * m; // tricky part cont. and then the left part happen to be (k1 - 1) / 2, the right part (k1) / 2 which also split the same number if it is odd length, edge case can also be tackled as 0 shall be impossible
        while (lo <= hi) {
            k1 = (lo + hi) / 2;
            k2 = n + (m - k1); // if k1 is decided, then what we can get from nums2 is m - k1 from start of nums2
            l1 = k1 == 0 ? Integer.MIN_VALUE : nums1[(k1 - 1) / 2]; // when k1 == 0, k1 cannot move to left any more, therefore always make the less condition satisfy
            l2 = k2 == 0 ? Integer.MIN_VALUE : nums2[(k2 - 1) / 2];
            r1 = k1 == 2 * m ? Integer.MAX_VALUE : nums1[k1 / 2];
            r2 = k2 == 2 * n ? Integer.MAX_VALUE : nums2[k2 / 2];
            if (l1 <= r2 && l2 <= r1) {
                // condition stands
                return (Math.max(l1, l2) + Math.min(r1, r2)) / 2.0;
            } else if (l1 > r2) {
                // we shall move to the left
                hi = k1 - 1;
            } else {
                lo = k1 + 1;
            }
        }

        return -1.0;
    }
```
