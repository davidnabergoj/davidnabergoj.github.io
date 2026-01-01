+++
date = '2026-01-01T19:37:00+01:00'
draft = false
tags = ['LeetCode', 'C++', 'Array', 'Dynamic programming', 'Greedy', 'Recursion', 'Memoization']
title = 'LeetCode 55: Jump Game'
[params]
    author = 'David Nabergoj'
+++

Let's solve [LeetCode problem 55: Jump Game](https://leetcode.com/problems/jump-game/).

The instructions are as follows:
> You are given an integer array `nums`. You are initially positioned at the array's first index, and each element in the array represents your maximum jump length at that position.
> Return `true` if you can reach the last index, or `false` otherwise.
>
> Constraints:
> * `1 <= nums.length <= 10^4`
> * `0 <= nums[i] <= 10^5`

I solved this in two ways: a less efficient approach using dynamic programming, and a very fast greedy method.
Let's dive in!

## The dynamic programming approach

We can solve this via recursion and memoization, i.e., top-down dynamic programming.
We define a recursive function `reachable(nums, i)` which outputs `true` if index \(i\) is reachable and `false` otherwise.
Within the recursive function, we have a base case: \(i = 0\) is always reachable.
The general case checks if \(i\) can be reached from any \(j\) before it (going from \(j = i - 1\) to \(j = 0\)).
If yes, we return `true`. Otherwise, we scan the remaining values of \(j\).
If \(i\) is not reachable from any previous \(j\), we return `false`.

Let \(n\) be the length of `nums`.
We return the final output as `reachable(nums, n - 1)`.

We also create a cache `dp` of size \(n\), whose values are all initially \(-1\).
Within the recursive call for index \(i\), we set `dp[i] = 1` if \(i\) is reachable and `dp[i] = 0` otherwise.
Some indices will remain at their default value of \(-1\).
This cache lets us avoid recomputing reachability.

Let's see the code.

```cpp
class Solution {
public:
    vector<int> dp;  // -1, 0, or 1

    bool reachable(vector<int>& nums, int i) {
        // Can we reach index i?
        if (i == 0) {
            return true;
        } else if (dp[i] == 1) {
            return true;
        } else if (dp[i] == 0) {
            return false;
        }
        
        for (int j = i - 1; j >= 0; --j) {
            if (nums[j] + j >= i && reachable(nums, j)) {
                dp[i] = 1;
                return true;
            }
        }
        dp[i] = 0;
        return false;
    }

    bool canJump(vector<int>& nums) {
        int n = nums.size();
        dp = vector<int>(n, -1);
        return reachable(nums, n - 1);
    }
};
```

Its space complexity is \(O(n)\) due to `dp` having size \(n\).
Its time complexity is \(O(n^2)\):
* There are \(n\) possible indices, so at most \(n\) unique calls to `reachable`.
* For each index \(i\), the function potentially iterates through all indices from \(i - 1\) down to zero. That's \(O(i)\) time, which averages to \(O(n / 2) = O(n)\).
* Due to the cache `dp`, each index is computed at most once. Once `dp[i]` is set, subsequent calls return immediately in \(O(1)\) time.

However, even with memoization preventing redundant computation, each of the \(n\) calls requires \(O(n)\) time in the worst case, giving a total time complexity of \(O(n^2)\).

The code runs in 8 ms (beating 19% of other solutions) and takes 55.64 MB memory (beating 6%). Let's see the more efficient, greedy approach.

## The greedy approach

At every index \(i\), we can jump to at least index `i + nums[i]`.
We could possibly reach even further depending on the previous values of `nums`.
Let's iterate through `nums` and keep track of the maximum reachable index \(m\).
At the beginning, we have \(m = 0\).
At every index \(i\), we can set `m = max(m, i + nums[i])`.
If we find \(m \geq n - 1\) at any point in the iteration, we return `true`.
If the entire iteration finishes without this happening, we return `false`.

Let's see the code!
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int m = 0;
        int n = nums.size();
        for (int i = 0; i < n && m < n - 1 && i <= m; ++i) {
            m = max(m, i + nums[i]);
        }
        return m >= n - 1;
    }
};
```

This is now much more efficient. It takes \(O(n)\) time to loop through the array and \(O(1)\) space as we only use two integers.

The code runs in 0 ms (beating 100% of other solutions) and takes 52.21 MB memory (beating 66% -- solutions with less memory usage apply some kind of LeetCode memory reduction tricks, so just for topping the leaderboards).

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)