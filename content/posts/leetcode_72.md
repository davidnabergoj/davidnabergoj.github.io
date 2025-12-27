+++
date = '2025-12-27'
draft = false
tags = ['LeetCode', 'C++', 'Dynamic programming', 'Edit distance', 'Levenshtein distance', 'Wagner-Fischer algorithm']
title = 'LeetCode 72: Edit distance'
[params]
    author = 'David Nabergoj'
+++

Today, let's look at [LeetCode problem 72: Edit distance](https://leetcode.com/problems/edit-distance).
The instructions are as follows:
> Given two strings `word1` and `word2`, return the minimum number of operations required to convert `word1` to `word2`. You have the following three operations permitted on a word:
> * Insert a character
> * Delete a character
> * Replace a character

Let's dive in!

## The Wagner-Fischer algorithm

There are several types of [edit distance](https://en.wikipedia.org/wiki/Edit_distance).
This LeetCode problem defines it as the [Levenshtein distance](https://en.wikipedia.org/wiki/Levenshtein_distance).
The most common algorithm to compute it is the [Wagner-Fischer algorithm](https://en.wikipedia.org/wiki/Wagner%E2%80%93Fischer_algorithm).

Suppose `word1` has length \(m\) and `word2` has length \(n\).
The Wagner-Fischer computes a matrix \(D\) of size \((m+1) \times (n+1)\) that holds the edit distances between all prefixes of `word1` and all prefixes of `word2`.
Computing each edit distance reuses the adjacent edit distances in the matrix, making this a dynamic programming algorithm.

Suppose `word1[:i]` is the length-\(i\) prefix of `word1` and `word2[:j]` is the length-\(j\) prefix of `word2`.
Then \(D_{i,j}\) holds the edit distance between `word1[:i]` and `word2[:j]`.

We initialize the first row with values from 0 to \(n\) and the first column with values from 0 to \(m\).
That's because converting an empty string to `word2[:j]` requires \(j\) insertions.
Similarly, converting `word1[:i]` to an empty string requires \(i\) deletions.

Here's the key rule to compute the edit distance:

\[
  D_{i,j} = \min (D_{i-1, j} + 1, D_{i, j - 1} + 1, D_{i-1, j-1} + s),
\]
where \(s = 0\) if `word1[i - 1] == word2[j - 1]` and \(s = 1\) otherwise.

The three terms in the minimum respectively correspond to deletion, insertion, and substitution.
If we are in cell \(D_{i,j}\), then:
* \(D_{i-1, j} + 1\) corresponds to deleting `word1[i - 1]` from `word1`,
* \(D_{i, j - 1} + 1\) corresponds to inserting `word2[j - 1]` into `word1` to match the longer prefix of `word2`,
* \(D_{i - 1, j - 1} + s\) corresponds to substituting `word1[i - 1]` with `word2[j - 1]` (or doing nothing if they are equal).

After filling the matrix in this way, the final edit distance is \(D_{m,n}\).

Here's an example for `word1 = "kitten"` and `word2 = "sitting"`.
The true edit distance is three: substitute `"k"` with `"s"`, substitute `"e"` with `"i"`, add `"g"` to the end.

|   |   | k | i | t | t | e | n |
| - | - | - | - | - | - | - | - |
|   | 0 | 1 | 2 | 3 | 4 | 5 | 6 |
| s | 1 | 1 | 1 | 2 | 3 | 4 | 5 |
| i | 2 | 2 | 1 | 2 | 3 | 4 | 5 |
| t | 3 | 3 | 2 | 1 | 2 | 3 | 4 |
| t | 4 | 4 | 3 | 2 | 1 | 2 | 3 |
| i | 5 | 5 | 4 | 3 | 2 | 2 | 3 |
| n | 6 | 6 | 5 | 4 | 3 | 3 | 2 |
| g | 7 | 7 | 6 | 5 | 4 | 4 | 3 |

The bottom right value in the matrix is indeed the true distance: 3.

The full code is shown below.
Before running the Wagner-Fischer algorithm, we check if the two words are equal, which only takes
\(O(\max(m, n))\) time.
The full algorithm takes \(O(mn)\) time and space.

```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        if (word1 == word2) {
            return 0;
        }

        // Wagner-Fischer algorithm
        size_t m = word1.size();
        size_t n = word2.size();

        vector<vector<size_t>> d(m + 1, vector<size_t>(n + 1, 0));
        for (size_t i = 1; i <= m; ++i) { 
            d[i][0] = i;
        }
        for (size_t j = 1; j <= n; ++j) { 
            d[0][j] = j;
        }

        for (size_t j = 1; j <= n; ++j) {
            for (size_t i = 1; i <= m; ++i) {
                bool substituted = (word1[i - 1] != word2[j - 1]);
                d[i][j] = min(
                    d[i - 1][j] + 1,  // deletion
                    min(
                        d[i][j - 1] + 1,  // insertion
                        d[i - 1][j - 1] + substituted  // substitution
                    )
                );
            }
        }
        return d[m][n];
    }
};
```

Running the code takes 4 ms (beating 81.58% of other solutions) and needs 15 MB of memory (beating 12.96% of other solutions).

## Optimized version with reduced space complexity

The Wagner-Fischer algorithm can be optimized to use less space.
The code above has to fill the matrix column-by-column.
However, filling each cell only needs the values of the cells to the left, above, and to the top-left.
This means we only need to keep:
* the current column (namely the values above the current cell), and
* the column to the left of the current one.

Now the space complexity will only be \(O(m)\), substantially less than \(O(nm)\).
The time complexity remains the same.

It's equivalent to keep two rows instead of two columns, as it's essentially working with a transposed matrix \(D\).
We'll use the rows strategy so we can easily keep track of rows with the `swap` function.
We'll have a `top` row and a `bottom` row.
Each row will have \(n + 1\) elements.
After processing all characters in both words, we will implicitly have filled up all rows in the matrix.
The result is stored in the last element of the bottom row.

Let's look at the code below.
There's a small optimization: if `word1` with length \(m\) is shorter than `word2` with length \(n\), we swap them.
This brings down the space complexity to \(O(\min (m, n))\) because \(m < n\) and we now only have \(m+1\) elements in each row.

```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        if (word1 == word2) {
            return 0;
        }

        if (word1.size() < word2.size()) {
            swap(word1, word2);
        }

        // Wagner-Fischer algorithm
        size_t m = word1.size();
        size_t n = word2.size();

        // Only store two rows
        vector<size_t> top(n + 1, 0);
        vector<size_t> bottom(n + 1, 0);
        
        for (size_t j = 1; j <= n; ++j) { 
            top[j] = j;
        }

        for (size_t i = 1; i <= m; ++i) {
            bottom[0] = i;
            for (size_t j = 1; j <= n; ++j) {
                bool substituted = (word1[i - 1] != word2[j - 1]);
                bottom[j] = min(
                    min(
                        top[j] + 1,  // deletion
                        bottom[j - 1] + 1  // insertion
                    ),
                    top[j - 1] + substituted  // substitution
                );
            }
            swap(top, bottom);
        }
        return top[n];  // because of the swap
    }
};
```

Running the code now takes 0 ms (beating 100% of other solutions) and needs 10.4 MB of memory (beating 98.46% of other solutions).
The used space in the optimized version is about 2/3 of the space in the basic version of the algorithm.

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)