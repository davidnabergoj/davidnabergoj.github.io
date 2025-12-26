+++
date = '2025-12-26'
draft = false
tags = ['LeetCode', 'C++', 'Greedy', 'Interval scheduling', 'Earliest deadline first']
title = 'LeetCode 435: Non-overlapping Intervals'
[params]
    author = 'David Nabergoj'
+++

Today, let's look at [LeetCode problem 435: Non-overlapping intervals](https://leetcode.com/problems/non-overlapping-intervals).
The instructions are as follows:
> Given an array of intervals intervals where `intervals[i] = [start_i, end_i]`, return the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping.
> Note that intervals which only touch at a point are non-overlapping. For example, `[1, 2]` and `[2, 3]` are non-overlapping.

Let's dive in!

## Interval scheduling reformulation

[Interval scheduling](https://en.wikipedia.org/wiki/Interval_scheduling) is a class of problems that involve a set of tasks, represented by their start and end times.
The *interval scheduling maximization problem* (ISMP) is about finding the largest set of non-overlapping tasks.
This is highly related to our interval removal problem.
In fact, the following statements are equivalent:
* `n` is the total number of tasks and `k` is the maximum number of non-overlapping tasks that can be executed;
* the minimum number of intervals to make the rest non-overlapping is `n - k`.

Let's solve a variant of the well-known ISMP and use the result to answer the removal question.
This is the single-interval scheduling problem (SISP), where we create an interval schedule in which no intervals overlap.
A greedy algorithm called *Earliest deadline first* (EDF) finds the optimal solution to SISP.
It's described as follows:
1. Select the interval `x` with the earliest finishing time.
2. Remove `x` and all intervals intersecting it from the set of candidate intervals.
3. Repeat until there are no more candidate intervals.

EDF starts by sorting the intervals by their end time in non-decreasing order.
It then processes intervals from first to last.

This problem is also related to a similar interview question: [maximum number of meetings in one room](https://www.geeksforgeeks.org/dsa/maximum-meetings-in-one-room/).

## Solving the counting problem

Since we only need to count the number of compatible intervals, we don't need to actually perform any removals. 
Instead, we start by sorting the intervals according to their end times.
We initialize a counter `k = 1` that counts the number of non-overlapping intervals.
The first non-overlapping interval is simply the first one in the sorted list, i.e., the interval at index `0`.
We store its index in a variable `prev = 0`.
Then loop over the rest of the intervals, starting at index `i = 1`.
At each index, we check if interval at index `i` overlaps with the previously executed one.
If so, we increment `k` and set `prev = i`.
Otherwise, we simply move forward, as the interval at index `i` was caught in an overlap.
We finally return `n - k`.

Let's look at the full code below.
```cpp
class Solution {
public:
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        // Sort by end times
        sort(
            intervals.begin(), 
            intervals.end(), 
            [](const vector<int>& left, const vector<int>& right) {
                return left[1] < right[1];
            }
        );

        // Apply *Earliest deadline first* (EDF)
        size_t prev = 0;
        size_t k = 1;
        for (size_t i = 1; i < intervals.size(); ++i) {
            if (intervals[i][0] >= intervals[prev][1]) {
                k++;
                prev = i;
            }
        }

        // Return the number of intervals to remove
        return intervals.size() - k;
    }
};
```


## Complexity and empirical performance
Our counting variant of EDF takes \(O(n \log n)\) time to sort the intervals and \(O(n)\) to count the number of compatible intervals, so the total time complexity is \(O(n \log n)\).
Ignoring the input data, the space complexity is \(O(1)\) as we only need to use two integers: `prev` and `k`.
LeetCode reports a runtime of 35 ms, which beats 95.23% of all solutions, as well as memory usage of 93.79 MB, which beats 98.60% of all solutions.

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)