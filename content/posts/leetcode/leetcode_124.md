+++
date = '2026-01-05T17:20:00+01:00'
draft = false
tags = ['LeetCode', 'C++', 'Dynamic programming', 'Tree', 'Depth first search', 'DFS', 'Binary tree']
title = 'LeetCode 124: Binary Tree Maximum Path Sum'
[params]
    author = 'David Nabergoj'
+++

Let's solve [LeetCode problem 124: Binary Tree Maximum Path Sum](https://leetcode.com/problems/jump-game/).

The instructions are as follows:
> A path in a binary tree is a sequence of nodes where each pair of adjacent nodes in the sequence has an edge connecting them. A node can only appear in the sequence at most once. Note that the path does not need to pass through the root.
> The path sum of a path is the sum of the node's values in the path.
> Given the `root` of a binary tree, return the maximum path sum of any non-empty path.
>
> Constraints:
> * The number of nodes in the tree is in the range `[1, 3 * 10^4]`.
> * `-1000 <= Node.val <= 1000`

I'll show my thought process and how I arrived at a practically fast and asymptotically efficient solution.
Let's dive in!

## Initial thoughts and reasoning

Speaking from experience with programming problems, this one seems like it will admit a solution in \(O(n)\) time and \(O(1)\) space (where \(n\) is the number of nodes in the tree).
Why? Because we can probably come up with a recursive function that propagates its result from the leaves of the tree back to the root, at which point we return the computed result.

To this end, let's think about the maximum path sum (MPS) in a recursive sense:
* If we have no root, then MPS is trivially 0.
* If we have a single node, then MPS is at most the value of that node.  If the node value is negative, then MPS is 0, otherwise it is the node's value.

## Thoughts on the general case in recursion

What about a general subtree, rooted at node `R`?
The MPS will either contain `R` or not. It will be the maximum of:
1. All path sums not containing `R`.
2. All path sums containing `R`.

We can break down the first point further by observing (1) paths that pass through `R`, and (2) paths that start with `R`.
So the MPS is the maximum of:
1. All path sums not containing `R`,
2. All path sums passing through `R`,
3. All path sums starting with `R`.

Let's observe the paths that don't contain `R`: they are either on the left or the right side of R. Therefore, MPS is the maximum of:
1. All path sums on `R->left`,
2. All path sums on `R->right`,
3. All path sums passing through R,
4. All path sums starting with R.

We can break down points 3 and 4 even further. MPS can be the maximum of:
1. All path sums on `R->left`,
2. All path sums on `R->right`,
3. All path sums containing `R, R->left`, and `R->right`,
4. All path sums containing `R` and `R->left`, but not `R->right`,
5. All path sums containing `R` and `R->right`, but not `R->left`,
6. All path sums containing `R` and neither `R->right`, nor `R->left` (trivially `R->val`).

Points 1 and 2 can be computed recursively.
Given their results, we can trivially compute points 3, 4, and 5.
Point 6 can be computed immediately.

## Initial solution

I used these observations to first precompute MPS for each node in the tree, and then traverse the entire tree to get the highest MPS value.
My solution takes \(O(n)\) time and space. While not optimal, it is a great starting point.

I defined a function `fill` that traverses the tree and fills a cache of size \(n\).
For each node, the cache contains its MPS conditional on the paths starting in that node.
Afterwards, the function `mps` goes through applies rules 1-6 to compute the full MPS.

```cpp
class Solution {
public:
    unordered_map<TreeNode*, int> cache;  // MPS ending in TreeNode* (i.e., other nodes on the path are below TreeNode*)

    void fill(TreeNode* n) {
        if (!n) {
            return;
        }

        fill(n->left);
        fill(n->right);

        int mpsLeft = 0;
        if (n->left) {
            mpsLeft = cache[n->left];
        }

        int mpsRight = 0;
        if (n->right) {
            mpsRight = cache[n->right];
        }

        cache[n] = n->val + max({
            0,  // Ignore the path below
            mpsLeft,
            mpsRight,
        });
    }

    int mps(TreeNode* n) {
        if (!n) {
            return INT_MIN;
        }

        int tmp = n->val;
        if (n->left && n->right) {
            tmp = cache[n->left] + cache[n->right] + n->val;
        }

        return max({
            mps(n->left),  // Rule 1
            mps(n->right), // Rule 2
            tmp,           // Rule 3
            cache[n],      // Rule 4, 5, and 6
        });
    }

    int maxPathSum(TreeNode* root) {
        fill(root);
        return mps(root);
    }
};
```

The `fill` function takes \(O(n)\) time, as it visits each node once.
The same goes for `mps`.
The time complexity is thus \(O(n)\).
The space complexity is also \(O(n)\) due to the `cache` variable of size \(n\).

## Simplified solution

We don't actually need such an intricate procedure.
Let's only think about MPS of paths that start with a given node (and thus necessarily contain it).
Let's call that `RMPS` (rooted MPS).
How would we compute `RMPS` of a node `R`?

If `R` is null, then `RMPS` is zero.
Otherwise, let's compute `RMPS` for the left child and the right child of `R`.
We can say (with a caveat):
> `RMPS(R)` is `R->val` plus the maximum of `RMPS(R->left)` and `RMPS(R->right)`.

The caveat is that the `RMPS` of either child may be negative.
We can clamp those values to zero and say:
> `RMPS(R)` is `R->val` plus the maximum of `max(RMPS(R->left), 0)` and `max(RMPS(R->right), 0)`.

We can then write a function `rootedMaximumPathSum`:
```cpp
int rootedMaximumPathSum(TreeNode* n) {
    if (!n) return 0;
    int left = max(rootedMaximumPathSum(n->left), 0);
    int right = max(rootedMaximumPathSum(n->right), 0);
    return n->val + max(left, right);
}
```

This function takes \(O(n)\) time if applied at the root, since it has to traverse all nodes.
It allocates two integers per node visited. If we are in the root, then the recursive call will at some point be using \(2n\) integers. This makes the space complexity \(O(n)\) as well.

How do we get the MPS?
Say we have a global variable `mps`.
After visiting each node `n`, we can potentially update `mps` with a new maximum: the path sum containing `n`. This path can include just `n->left`, just `n->right`, both, or neither.
If it contains just `n->left`, then `n->right` must be negative and we ignore it.
If it contains just `n->right`, then `n->left` must be negative and we ignore it.
If it contains neither, then both child MPS values must be negative and we ignore them.
Otherwise, we add them.
It turns out this information is already encoded in `left` and `right` since we clamped them to zero!
We only have to declare a global `mps` variable and apply a single line:

```cpp
int mps = INT_MIN;

int rootedMaximumPathSum(TreeNode* n) {
    if (!n) return 0;
    int left = max(rootedMaximumPathSum(n->left), 0);
    int right = max(rootedMaximumPathSum(n->right), 0);
    mps = max(mps, n->val + left + right);
    return n->val + max(left, right);
}
```

Now we can call our main function and return the global variable.
The full code is given below:

```cpp
class Solution {
public:
    int mps = INT_MIN;

    int rootedMaximumPathSum(TreeNode* n) {
        if (!n) return 0;
        int left = max(rootedMaximumPathSum(n->left), 0);
        int right = max(rootedMaximumPathSum(n->right), 0);
        mps = max(mps, n->val + left + right);
        return n->val + max(left, right);
    }

    int maxPathSum(TreeNode* root) {
        rootedMaximumPathSum(root);
        return mps;
    }
};
```

Adding the intermediate `mps` update does not change the time or space complexity.
On LeetCode tests, the final code takes 0 ms (beating 100% of other solutions) and 27.91 MB of memory (beating 75%, though better solutions use the same algorithm, only some LeetCode-specific data parsing optimizations).

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)