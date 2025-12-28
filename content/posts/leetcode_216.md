+++
date = '2025-12-28'
draft = false
tags = ['LeetCode', 'C++', 'Backtracking', 'Recursion']
title = 'LeetCode 216: Combination Sum III'
[params]
    author = 'David Nabergoj'
+++

Today, let's look at [LeetCode problem 216: Combination Sum III](https://leetcode.com/problems/combination-sum-iii).
The instructions are as follows:
> Find all valid combinations of \(k\) numbers that sum up to \(n\) such that the following conditions are true:
> * Only numbers 1 through 9 are used.
> * Each number is used at most once.
>
> Return a list of all possible valid combinations. The list must not contain the same combination twice, and the combinations may be returned in any order.
> 
> Constraints:
> * `2 <= k <= 9`
> * `1 <= n <= 60`

Let's dive in!

## Strategy and implementation

We can approach this problem by trying to fill up an empty vector `v` to size \(k\).
Once `v` has length \(k\) and sums to \(n\), we add it to the vector of results.
If it has length \(k\), but does not sum to \(n\), then we replace some elements in it.

A systematic way of doing this is via [backtracking](https://en.wikipedia.org/wiki/Backtracking).
We create a recursive function `void solve(size_t total)`, where `total` is the current sum of `v`. The function `solve` also has access to a global vector `v`, a global vector of vectors `results`, a target vector length variable, and a target sum variable.

During each recursive call, we apply one of these three rules:
1. If `v` has length \(k\) and `total` equals \(n\), then we add `v` to `results`.
2. If `v` is shorter than \(k\) and `total < n`, then we add recursively call `solve` several times. Before each call, we add a new number `j` to the end of the vector (`j` has to be bigger than the last element of `v`, but at most 9). We then call the function with `solve(total + j)` to indicate the change in the vector sum. During this call, solutions may be added to `results`. After the call finishes, we pop the last element of `v` and apply the procedure for the next value of `j`.
3. If neither of the two rules above applies, we do nothing.

Rule 1 and rule 3 are base cases for recursion. If rule 1 applies, we've identified `v` as one possible solution. If rule 3 applies, we did not.
Rule 2 is the general case, during which we try to recursively construct solution vectors. In rule 2, backtracking occurs when we pop the last element of `v`.

Let's look at the code below.
In `combinationSum3`, we set up two global variables and call `solve` with an empty vector `v` that has sum 0.
Rule 2 is the most interesting part of `solve`.
We start adding digits from `j0` onward. If `v` is empty, we set `j0` to 1, as that's the smallest permissible number.
If not, we set `j0` to be one bigger than the last element of `v`.
Then we add digits `j` from `j0` to 9 inclusive.
We apply the recursive call to `solve` and backtrack after the call finishes.

```cpp
class Solution {
public:
    vector<vector<int>> results;
    vector<int> v;

    size_t targetLength;
    size_t targetSum;

    void solve(size_t total) {
        // Rule 1
        if (v.size() == targetLength && total == targetSum) {
            results.push_back(v);
            return;
        }

        // Rule 2
        if (v.size() < targetLength && total < targetSum) {
            size_t j0;
            if (v.size() == 0) {
                j0 = 1;
            } else {
                j0 = v.back() + 1;
            }

            for (size_t j = j0; j <= 9; j++) {
                v.push_back(j);
                solve(total + j);
                v.pop_back();
            }
            return;
        }

        // Rule 3: nothing
    }

    vector<vector<int>> combinationSum3(int k, int n) {
        targetLength = k;
        targetSum = n;
        solve(0);
        return results;
    }
};
```

We can bound the number of solutions to check with \(9^k\).
A tighter bound is the [number of strictly increasing sequences](https://math.stackexchange.com/a/658266) of length \(k\) with elements from \(\{1, \dots, 9\}\).
There are \(\binom{9}{k} = 9! / ((9-k)!k!)\) such sequences.
The maximal number of sequences is 126 and occurs at \(k = 4\) or \(k = 5\).
The total number of candidates explored (including those shorter than \(k\)) is bounded by \(\sum_{i=0}^k \binom{9}{i}\), which is maximized when \(k = 9\) and equals 512.

The space complexity is \(O(mk)\) where \(m\) is the number of solutions.
We can again say \(m \leq 126 \) per the discussion above.
Since \(k \leq 9\), we need to store at most \(9 \cdot 126 = 1134 \) digits, which is tiny.

The total runtime is effectively constant.
LeetCode indeed reports a runtime of 0 ms (beating 100% of other solutions) and a memory use of 8.68 MB (beating 78.86% of other solutions).

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)