+++
date = '2025-12-14'
draft = false
tags = ['LeetCode', 'C++', 'Binary search']
title = 'LeetCode 162: Find Peak Element'
[params]
    author = 'David Nabergoj'
+++

Very quick post about finding the peak element in an array. 
This is [LeetCode problem 162](https://leetcode.com/problems/find-peak-element).
We have an array `nums` with \(n\) integers and want to find the index of one of its peaks in \(O(\log n)\) time.
The important detail is this: no two neighboring elements have the same value.

Let's dive in!

## Solution

To solve this in logarithmic time, we will use binary search.
We start with a `left` index and a `right` index.
We then compute a mid point `mid = (left + right) / 2`.
Now we investigate what the local behavior around `mid` is.
If `nums[mid] > nums[mid + 1]`, it means that there's no point searching for the peak at `mid + 1` or to its right, so we set `right = mid`.
Otherwise, there's no point in searching for the peak at `mid` or to its left, so we set `left = mid + 1`.

We keep repeating this until `left` is greater or equal to `right`.
Here's the code:

```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        int left = 0;
        int right = nums.size() - 1;
        int mid;

        while (left < right) {
            int mid = (left + right) / 2;
            if (nums[mid] > nums[mid + 1]) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
};
```

The code beats 100% of other solutions in terms of runtime.
The time complexity is \(O(\log n)\), because we narrow down the search to half of the array in each loop iteration.
Even more precisely, the number of iterations is at most \(\lceil \log_2 n \rceil\).


Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)