+++
date = '2026-01-01T16:36:00+01:00'
draft = false
tags = ['LeetCode', 'C++', 'Array', 'Dynamic programming', 'Greedy']
title = 'LeetCode 122: Best Time to Buy and Sell Stock II'
[params]
    author = 'David Nabergoj'
+++

Let's solve [LeetCode problem 122: Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/).

The instructions are as follows:
> You are given an integer array `prices` where `prices[i]` is the price of a given stock on the \(i\)-th day.
> On each day, you may decide to buy and/or sell the stock. You can only hold at most one share of the stock at any time. However, you can sell and buy the stock multiple times on the same day, ensuring you never hold more than one share of the stock.
> Find and return the maximum profit you can achieve.
>
> Constraints:
> * `1 <= prices.length <= 3 * 10^4`
> * `0 <= prices[i] <= 10^4`

Let's dive in!

## General idea

My first idea in solving this was to try and find a rule to apply at each step.
Let \(r_i\) be maximum attainable profit on day \(i\) and \(p_i\) the stock price on day \(i\).

We don't initially own a stock, so we can't sell it for potential profit on day 0. Therefore, \(r_0 = 0\).
If doing nothing is optimal, then \(r_i = r_{i - 1}\). We neither buy nor sell.
If it's optimal to sell our stock on day \(i\), then the question is: when did we buy the stock we're about to sell?
I arrived at the answer for an efficient solution in three stages, which I'll present below.

## Slow solution: checking all previous prices

To determine when we bought the stock, we can check all prices from \(j = 0\) to \(j = i - 1\).
If we decide to have bought the stock on day \(j\), then the profit is: \(-p_j + p_i + r_{j - 1}\):
* \(-p_j\) corresponds to buying the stock on day \(j\).
* \(+p_i\) corresponds to selling the stock on day \(i\) (today).
* \(+r_{j-1}\) corresponds to the maximum profit achieved before buying the stock on day \(j\).

Then, the maximum profit on \(i\) is simply the maximum over all these \(j\), as well as \(r_{i-1}\) (if we do nothing).
The code is shown below:

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        size_t n = prices.size();
        vector<int> profits(n, 0);

        for (size_t i = 1; i < n; ++i) {
            profits[i] = profits[i - 1];
            for (size_t j = 0; j < i; ++j) {
                int prevMaxProfit;
                if (j == 0) {
                    prevMaxProfit = 0;
                } else {
                    prevMaxProfit = profits[j - 1];
                }

                int buyValue = -prices[j] + prices[i] + prevMaxProfit;
                if (buyValue > profits[i]) {
                    profits[i] = buyValue;
                }
            }
        }
        return profits[n - 1];
    }
};
```

We keep a vector of profits with size \(n\) (also the size of `prices`).
We initially set `profits[i] = profits[i - 1]`, which corresponds to doing nothing.
However, we potentially overwrite this in the inner for loop using our rule.

The time complexity of this algorithm is \(O(n^2)\), while the space complexity is \(O(n)\).
The code requires about 1000 ms of time to run. We can improve it.

## Minor improvement: checking only some of the previous prices

With a minor modification, we can achieve a better practical runtime.
The idea is: instead of checking all \(j\) starting from 0, we start from the index that corresponds to the minimum price seen so far.
After all, it's better to buy the smallest price stock than the more expensive ones before it.
The code is shown below.

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        size_t n = prices.size();
        vector<int> profits(n, 0);
        
        int minPrice = prices[0];
        size_t minPriceIndex = 0;

        for (size_t i = 1; i < n; ++i) {
            profits[i] = profits[i - 1];
            for (int j = minPriceIndex; j < i; ++j) {
                int prev;
                if (j == 0) {
                    prev = 0;
                } else {
                    prev = profits[j - 1];
                }

                int buyValue = -prices[j] + prices[i] + prev;
                if (buyValue > profits[i]) {
                    profits[i] = buyValue;
                }
            }
            if (prices[i] < minPrice) {
                minPrice = prices[i];
                minPriceIndex = i;
            }
        }
        return profits[n - 1];
    }
};
```

Even though the time and space complexities remain the same, the code now requires only 340 ms to run.

## Major improvement: selling and buying whenever reasonable

The biggest improvement comes from understanding a key part of the instructions: we may buy or sell however many times during each day.
This means that if the stock price today is greater than the stock price on the day before, we can immediately sell it for a profit.
We could also buy it back and sell it later if the price continues to increase.

This greedy approach simplifies the code a lot.
At each step, we simply increase our total profit by the change in stock price if the stock is more valuable today than on the previous day.

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        size_t n = prices.size();
        int total = 0;
        for (size_t i = 1; i < n; ++i) {
            if (prices[i] > prices[i - 1]) {
                total += prices[i] - prices[i - 1];
            }
        }
        return total;
    }
};
```

If this strategy worked in the real world, then getting rich would be easy.
The latest solution has a time complexity of \(O(n)\) and a space complexity of \(O(1)\).
It runs in 0 ms (beating 100% of other solutions) and takes 17.04 MB space (beating 84% of other solutions).

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)