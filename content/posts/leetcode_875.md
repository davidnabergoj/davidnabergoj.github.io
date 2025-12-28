+++
date = '2025-12-28'
draft = false
tags = ['LeetCode', 'C++', 'Binary search']
title = 'LeetCode 875: Koko Eating Bananas'
[params]
    author = 'David Nabergoj'
+++

Today, let's look at [LeetCode problem 875: Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas).
The instructions are as follows:
> Koko loves to eat bananas. There are `n` piles of bananas, the \(i\)-th pile has `piles[i]` bananas. 
> The guards have gone and will come back in `h` hours.
> Koko can decide her bananas-per-hour eating speed of `k`. 
> Each hour, she chooses some pile of bananas and eats `k` bananas from that pile. 
> If the pile has less than `k` bananas, she eats all of them instead and will not eat any more bananas during this hour.
> Koko likes to eat slowly but still wants to finish eating all the bananas before the guards return.
> Return the minimum integer `k` such that she can eat all the bananas within `h` hours.
> 
> Constraints:
> * `1 <= piles.length <= 10^4`  
> * `piles.length <= h <= 10^9`  
> * `1 <= piles[i] <= 10^9`

Let's dive in!

## Checking if a given rate is valid

To check if a rate `k` is valid, we have to first compute the total time spent eating bananas across all piles with rate `k` and then ensure that this time is at most `h`.
Let's write this as helper function:

```cpp
int ceilDivision(int a, int b) {
    // return ceil(a / b) where a and b are integers
    return (a + b - 1) / b;
}

bool validRate(vector<int> *piles, int h, int k) {
    int total = 0;
    for (int i = 0; i < piles->size(); ++i) {
        total += ceilDivision(piles->at(i), k);
    }
    return total <= h;
}
```

If `ceilDivision` appears too complicated, think about this:
the time spent eating pile \(i\) is equal to `piles[i] / k` if `k` evenly divides `piles[i]`. If it doesn't, then there are still some bananas leftover and Koko has to spend an hour eating them. We can use the one-liner below.

```cpp
total += piles->at(i) / k + (piles->at(i) % k > 0);
```

This is equivalent to our `ceilDivision(piles->at(i), k)` function.
More on the function in this [StackOverflow discussion](https://stackoverflow.com/a/2745086).

## Binary search strategy

We're trying to find the minimum integer rate `k` such that Koko can still eat all of the bananas in time `h`.
The maximum possible rate is \(m\); the number of bananas on the biggest pile.
Since `h` is at least the number of piles, using rate \(m\) means spending one hour for each pile.
The minimum possible rate is \(1\).

We can simply apply [binary search](https://en.wikipedia.org/wiki/Binary_search) on the set \(\{1, 2, \dots, m - 1, m\}\).
For a candidate rate in the middle between the minimum and maximum rate, we check if it's valid with the `validRate` function.
If the rate is valid, we shift the maximum rate to the candidate rate (since the rate can't be bigger).
If it's invalid, we shift the minimum rate to the candidate rate and add \(1\) (that's the first rate that could possibly be valid).

The implementation is straightforward. Below is the full code.
```cpp
class Solution {
public:
    int ceilDivision(int a, int b) {
        // return ceil(a / b) where a and b are integers
        return (a + b - 1) / b;
    }

    bool validRate(vector<int> *piles, int h, int k) {
        int total = 0;
        for (int i = 0; i < piles->size(); ++i) {
            total += ceilDivision(piles->at(i), k);
        }
        return total <= h;
    }

    int minEatingSpeed(vector<int>& piles, int h) {
        int minRate = 1;
        int maxRate = *max_element(piles.begin(), piles.end());

        while (minRate < maxRate) {
            int midRate = (minRate + maxRate) / 2;
            if (validRate(&piles, h, midRate)) {
                maxRate = midRate;
            } else {
                minRate = midRate + 1;
            }
        }

        return minRate;
    }
};
```

This solution requires \(O(n \log m)\) time and \(O(1)\) extra space.
The time complexity derives from using \(O(\log m)\) binary search steps, each requiring us to check \(n\) piles.
LeetCode reports a runtime of 11 ms and a memory use of 22.83 MB (beating 76.17% of other solutions).

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)