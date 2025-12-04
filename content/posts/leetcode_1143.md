+++
date = '2025-12-04'
draft = false
tags = ['Leetcode', 'C++', 'Dynamic programming', 'Recursion']
title = 'Leetcode 1143: Longest Common Subsequence'
[params]
    author = 'David Nabergoj'
+++

In this post, we'll be solving [LeetCode problem 1143](https://leetcode.com/problems/longest-common-subsequence).
We have two strings `text1` and `text2` with sizes \(n\) and \(m\), respectively.
We want to find the length of their longest common subsequence (LCS).
A subsequence of a string `s` is obtained by deleting zero or more characters from `s`.

## Strategy

Let's assume we have two strings: `s1` with size `n1` and `s2` with size `n2`.
Let's also assume we know their LCS length.
What can we say about LCS length when we append a character `c1` to `s1` and a character `c2` to `s2`?

### The new characters are the same

If `c1` and `c2` are the same, then the new LCS is the previous LCS plus the new character `c1` (or `c2`, they are the same after all). The new LCS length is thus one plus the previous LCS length. 

For example, say `s1 = subjec` and `s2 = objec`. 
The LCS is `bjec` and has length 4.
We now add `t` to both, so `c1 = t` and `c2 = t`. The new strings are `s1 = subject` and `s2 = object`.
The new LCS is `bject` and has length 5.

![equal-chars](/leetcode_1143/char-equal.png#center)

### The new characters are different

What if they're not the same?

In this case, just adding `c1` to `s1` and keeping `s2` unchanged *may* be enough to increase LCS length. We can add `c2` to `s2` afterwards, even though it won't increase LCS length. The same goes for the reverse case: we may increase LCS length by adding `c2` to `s2`. We can add `c1` to `s1` afterwards.
We're interested in the longer of these two lengths.

The new LCS length is thus the maximum of two LCS lengths: one where we only add `c1` to `s1` and one where we only add `c2` to `s2`. Let's look at an example below, where `s1 = factor` and `s2 = stay`. We try to add `c1 = y` and `c2 = s`.

![equal-chars](/leetcode_1143/char-diff.png#center)

## Basic implementation

We will implement our approach using recursion. We'll write a function `lcsLength(string *text1, string *text2, int i, int j)`. This function will return LCS length of `text1` up to index `i` (inclusive) and `text2` up to index `j` (inclusive). This will be like adding `text1[i]` to `s1` and `text[j]` to `s2`. We'll pass in pointers to strings to avoid copying entire strings in each recursive call. The basic function can be implemented as follows:

```cpp
int lcsLength(string *text1, string *text2, int i, int j) {
    if (i < 0 || j < 0) {
        return 0;
    }
    if (text1->at(i) == text2->at(j)) {
        return lcsLength(text1, text2, i - 1, j - 1) + 1;
    } else {
        return max(
            lcsLength(text1, text2, i - 1, j),
            lcsLength(text1, text2, i, j - 1)
        );
    }
}
```

## Implementation with dynamic programming

The issue with the above code is that we call `lcsLength` multiple times with the same values of `i` and `j`. This wastes a lot of computation time, and causes a combinatorial explosion of the number of computations across recursive calls. We fix this by creating a matrix `lcsLengthCache` of size `n x m` which holds the computed values. Whenever we call `lcsLength`, we first check if the result is already in our matrix and attempt to return that instead of recomputing everything. We initialize the matrix with `-1` in all cells, which indicates that the value had not yet been computed. Reusing computations in such a way is called [dynamic programming](https://www.geeksforgeeks.org/competitive-programming/dynamic-programming/).

```cpp
vector<vector<int>> lcsLengthCache;  // must be initialized to size n x m

int lcsLength(string *text1, string *text2, int i, int j) {
    if (i < 0 || j < 0) {
        return 0;
    }
    if (lcsLengthCache[i][j] == -1) {
        if (text1->at(i) == text2->at(j)) {
            lcsLengthCache[i][j] = lcsLength(text1, text2, i - 1, j - 1) + 1;
        } else {
            lcsLengthCache[i][j] = max(
                lcsLength(text1, text2, i - 1, j),
                lcsLength(text1, text2, i, j - 1)
            );
        }
    }
    return lcsLengthCache[i][j];
}
```

## Time complexity analysis

Since the sizes of `text1` and `text2` are `n` and `m`, we want to compute `lcsLength(&text1, &text2, n - 1, m - 1)`. This will be the LCS length across the entirety of both strings. Below is the full code.

```cpp
class Solution {
public:
    vector<vector<int>> lcsLengthCache;
    int lcsLength(string *text1, string *text2, int i, int j) {
        if (i < 0 || j < 0) {
            return 0;
        }
        if (lcsLengthCache[i][j] == -1) {
            if (text1->at(i) == text2->at(j)) {
                lcsLengthCache[i][j] = lcsLength(text1, text2, i - 1, j - 1) + 1;
            } else {
                lcsLengthCache[i][j] = max(
                    lcsLength(text1, text2, i - 1, j),
                    lcsLength(text1, text2, i, j - 1)
                );
            }
        }
        return lcsLengthCache[i][j];
    }

    int longestCommonSubsequence(string text1, string text2) {
        int n = text1.size();
        int m = text2.size();
        lcsLengthCache = vector<vector<int>>(n, vector<int>(m, -1));
        return lcsLength(&text1, &text2, n - 1, m - 1);
    }
};
```

## Time complexity analysis
The code passes all tests with a runtime of 55ms and 27.66 MB memory usage. There are a bunch of avenues for optimization, but the solution clearly highlights the approach and benefits of dynamic programming.

The total space complexity is \(O(nm + n + m)\) to hold the matrix and both inputs. It can be simplified to \(O(nm)\).
The total time complexity is \(O(nm)\) as we need at most \(nm\) evaluations of `lcsLength` to compute the final output by filling the entire matrix.

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)
