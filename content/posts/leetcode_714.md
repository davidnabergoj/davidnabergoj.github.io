+++
date = '2025-12-14'
draft = false
tags = ['LeetCode', 'C++', 'Dynamic programming']
title = 'LeetCode 714: Best Time to Buy and Sell Stock with Transaction Fee'
[params]
    author = 'David Nabergoj'
+++

Welcome back to another LeetCode walkthrough! Let's look at [LeetCode problem 714](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/).
We're given an array of stock prices. Each day, we can either buy a stock if we have none or sell a stock we own (but not both).
When we sell a stock, we have to pay a transaction fee.
We want to find the maximum profit we can achieve.

We approach this using dynamic programming.
Let's dive in!

## Visualizing possible actions

When tackling dynamic programming problems, I instantly think: how can I reuse results I've already computed? 
I found it very useful to visualize what actions I can take every day.
Let's look at a tree of options:
![Tree of all possible actions in three days](/leetcode_714/option-tree.png#center)

After taking a path of actions, we arrive at a particular node. The value in the node is our balance after all the actions we took along the way.
We can see that some nodes give us a higher value than others.
The best outcome in this case is a balance of 10, while the worst is a balance of -10.

If we simulate all options, we'll clearly end up with exponentially many possibilities.
That would take too long to compute in reasonable time.
Could we simplify this tree a bit?

Yes! And it's very intuitive!

## Simplifying the action tree
We'll simplify the action tree in two ways: 
1. If we have no stock to sell on a particular day, then it doesn't really matter *how* we got to that day. What matters is the balance we have. We might as well only continue with the highest possible balance.
2. If we have a stock to sell on a particular day, then it doesn't really matter *how* we got there. After all, we paid for the stock (including the fee) on a previous day. What we have right now is our balance and an option to sell. We might as well only continue with the highest possible balance in this case too.

This completely removes the need to simulate all options! Let's just keep the highest balance based on if we have a stock or not.
Let's say \(H_i\) is the balance on day \(i\) if we are holding a stock that we can sell.
Let's call \(F_i\) the balance on day \(i\) if we have no stock to sell.
We will denote the buying fee with \(f\) and the stock on day \(i\) with \(S_i\).

What happens on day \(i+1\)?

\(H_{i+1}\) will be the maximum of two values: 
- The previous balance \(H_i\), indicating that we haven't sold the stock on day \(i+1\), or
- A new balance \(F_i - f - S_{i+1}\), indicating that we paid \(f\) to buy the stock valued at \(S_{i+1}\) while the previous balance was \(F_i\). This is like overwriting \(H_i\) with a new, better path in the action tree.

Similarly, \(F_{i+1}\) will be the maximum of two values:
- The previous balance \(F_i\), indicating that we haven't bought the stock on day \(i+1\), or
- A new balance \(H_i + S_{i+1}\), indicating that we sold the stock valued at \(S_{i+1}\) while the previous balance was \(H_i\). This is like overwriting \(F_i\) with a new, better path in the action tree.

We can represent the step with two formulas:
\[
    H_i \mapsto \max (H_i, F_i - f - S_{i+1}, \\
    F_i \mapsto \max (F_i, H_i + S_{i + 1}).
\]

What are the initial values? If we buy on day 1, we have \(H_1 = -S_1 - f\). If we don't we have \(F_1 = 0\).
If there are  \(n\) days, then our output will be \(F_n\). After all, it's better to have sold our last stock than to still be holding it.

## Full solution and time complexity analysis

The implementation is super straightforward. We simply apply the formula every day:

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int holdBalance = -fee - prices[0];  // H_i
        int freeBalance = 0;  // F_i
        for (size_t i = 1; i < prices.size(); ++i) {
            int newHoldBalance = max(holdBalance, freeBalance - fee - prices[i]);
            int newFreeBalance = max(freeBalance, holdBalance + prices[i]);
        
            holdBalance = newHoldBalance;
            freeBalance = newFreeBalance;
        }
        return freeBalance;
    }    
}
```

That's it!
LeetCode says this solution takes 0 ms, beating 100% of other solutions in terms of runtime.
It takes 58.98 MB memory, but this is only due to the input array and LeetCode's overhead. We're only using four integers after all.

The time complexity is \(O (n)\) as we only have to loop through the `prices` array once.
The space complexity is \(O(1)\) as we only use four integers regardless of \(n\).

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)