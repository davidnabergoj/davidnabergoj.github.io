+++
date = '2025-12-27'
draft = false
tags = ['LeetCode', 'C++', 'Dynamic programming', 'Greedy']
title = 'LeetCode 746: Min Cost Climbing Stairs'
[params]
    author = 'David Nabergoj'
+++

Today, let's look at [LeetCode problem 746: Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs).
The instructions are as follows:
> You are given an integer array `cost` where `cost[i]` is the cost of \(i\)-th step on a staircase. Once you pay the cost, you can either climb one or two steps.
> You can either start from the step with index 0, or the step with index 1.
> Return the minimum cost to reach the top of the floor.

Let's dive in!

## Strategy and solution

Let's think about the minimum cost required to reach stair \(i\). We could have arrived there from:
* stair \(i-2\), costing `cost[i - 2]` plus the minimum cost to arrive at stair \(i - 2\), or
* stair \(i-1\), costing `cost[i - 1]` plus the minimum cost to arrive at stair \(i - 1\).

The cheapest path means taking the stair with the lower cost.
If \(m_i\) is the minimum cost to arrive at stair \(i\) and \(c_i\) is `cost[i]`, we can write the recurrence as:

\[
    m_i = \min(c_{i - 2} + m_{i - 2}, c_{i - 1} + m_{i - 1}).
\]

Since we can start at either stair 0 or stair 1, arriving there costs nothing. Therefore \(m_0 = m_1 = 0\).
We could program this recursively and memoize the results to avoid recomputation.
However, it's much easier to simply use an iterative approach.

Let's look at the code below.
We use `minCost1` to hold \(m_{i-2}\) and `minCost2` to hold \(m_{i-1}\). We store \(m_i\) inside `minCost3`.
After each iteration, we set `minCost1` to `minCost2` and `minCost2` to `minCost3`.
The final result is in `minCost2`.
This is similar to iteratively computing a number from the [Fibonacci sequence](https://www.geeksforgeeks.org/dsa/program-for-nth-fibonacci-number/).

```cpp
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        size_t minCost1 = 0;  // cost to reach first stair
        size_t minCost2 = 0;  // cost to reach second stair
        size_t minCost3;
        for (size_t i = 2; i <= cost.size(); ++i) {
            minCost3 = min(
                minCost1 + cost[i - 2],
                minCost2 + cost[i - 1]
            );
            minCost1 = minCost2;
            minCost2 = minCost3;
        }
        return minCost2;
    }
};
```

Given \(n\) stairs, the code requires \(O(n)\) time and \(O(1)\) space, which looks to be optimal.
LeetCode reports a runtime of 0 ms (beating 100% of other solutions) and memory use of 17.56 MB, which is effectively just from the input data.
Our actual code only uses a few integers.
A very efficient solution!

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)