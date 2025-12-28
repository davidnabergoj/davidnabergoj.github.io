+++
date = '2025-12-28'
draft = false
tags = ['LeetCode', 'C++', 'Dynamic programming', 'Greedy', 'Tiling']
title = 'LeetCode 790: Domino and Tromino Tiling'
[params]
    author = 'David Nabergoj'
+++

Today, let's look at [LeetCode problem 790: Domino and Tromino Tiling](https://leetcode.com/problems/domino-and-tromino-tiling).
The instructions are as follows:
> You have two types of tiles: a `2 x 1` domino shape and a tromino shape. You may rotate these shapes.
> Given an integer \(n\), return the number of ways to tile an `2 x n` board. Since the answer may be very large, return it modulo `10^9 + 7`.
> In a tiling, every square must be covered by a tile. Two tilings are different if and only if there are two 4-directionally adjacent cells on the board such that exactly one of the tilings has both squares occupied by a tile.

Let's dive in!

## Counting the possible tilings

Let's look at all possible tilings for a few values of \(n\) in the image below.
![Tilings](/leetcode_790/tilings.png#center)

### Unique new tilings
We notice that each \(n\) has a few unique tilings that don't show up before it:
* For \(n = 1\), the last tiling is a unique new vertical domino.
* For \(n = 2\), the last tiling is a unique new pair of horizontal dominos.
* For \(n = 3\), the final two tilings are new and made up of two trominos.
* For \(n = 4\), the final two tilings are new and made up of two trominos and a domino.

I've marked these in the image below.
![Annotated tilings](/leetcode_790/annotated-tilings.png#center)

In fact, each new \(n\) introduces new unique tilings.
Let's look at these for \(n \leq 7\).
![Unique tilings](/leetcode_790/unique.png#center)

Clearly, there are always 2 new unique tilings for each \(n \geq 3\).

### How are the tilings constructed?
Aside from these new and unique tilings, we can also notice a pattern in how the other tilings are constructed.
Let's consider \(n = 4\). 
I've purposely arranged the tilings to clearly see the pattern in the image below.
![Construction for n = 4](/leetcode_790/n4_construction.png#center)

The tilings are constructed as follows:
* Take all tilings for \(n = 3\) and append the unique tiling from \(n = 1\).
* Take all tilings for \(n = 2\) and append the unique tiling from \(n = 2\).
* Take all tilings for \(n = 1\) and append the unique tilings from \(n = 3\).

We cover the edge case of new tilings by taking all the tilings for \(n = 0\) (i.e., nothing) and appending the unique tilings from \(n = 4\). The number of tilings for \(n\) are thus:

\[
    t(n) = t(n - 1) + t(n - 2) + \sum_{i = 0}^{n - 3} 2 t(i).
\]

The term \(t(n - 1)\) corresponds to tilings from \(n - 1\) and adding a vertical domino.
The term \(t(n - 2)\) corresponds to tilings from \(n - 2\) and adding a horizontal domino pair.
The remaining terms \(t(i)\) corresponds to tilings from \(i\) and adding two unique tilings (since we know each \(n \geq 3\) has two new unique tilings), which is why each term is multiplied by \(2\).

This is the dynamic programming recurrence relation.

### Simplifying the recurrence relation

We can simplify the equation above with some clever math.
First, let's construct a helper term with \(t(n - 1)\):

\[
    t(n - 1) = t(n - 2) + t(n - 3) + \sum_{i = 0}^{n - 4} 2 t(i), \;\mathrm{therefore} \\
    t(n - 2) + t(n - 3) = t(n - 1) - \sum_{i = 0}^{n - 4} 2 t(i).
\]

We will use this term as a replacement inside the square brackets in the derivation below:

\[
    t(n) = t(n - 1) + t(n - 2) + \sum_{i = 0}^{n - 3} 2 t(i) \\
     = t(n - 1) + t(n - 2) + 2 t(n - 3) + \sum_{i = 0}^{n - 4} 2 t(i) \\
     = t(n - 1) + [t(n - 2) + t(n - 3)] + t(n - 3) + \sum_{i = 0}^{n - 4} 2 t(i) \\
     = t(n - 1) + [t(n - 1) - \sum_{i = 0}^{n - 4} 2 t(i)] + t(n - 3) + \sum_{i = 0}^{n - 4} 2 t(i) \\
     = 2t(n - 1) + t(n - 3) \\
\]

## Full implementation

The solution becomes trivial to implement using the simplified formula.
We use `a, b, c` to hold \(t(i - 3), t(i - 2),\) and \(t(i - 1)\) respectively.
We store \(t(i)\) inside variable `d` at each step.
We iterate from \(i = 4\) to \(i = n\). After the loop, the final value \(t(n)\) is stored inside `d` (and `c`, because of the variable swap).
We apply modulo according to the instructions to avoid overflows.

```cpp
class Solution {
public:
    int numTilings(int n) {
        if (n == 1) {
            return 1;
        } else if (n == 2) {
            return 2;
        } else if (n == 3) {
            return 5;
        }

        size_t a = 1;
        size_t b = 2;
        size_t c = 5;
        size_t d;

        for (size_t i = 4; i <= n; ++i) {
            d = (2 * c + a) % 1000000007;

            a = b;
            b = c;
            c = d;
        }
        return d;
    }
};
```

This solution requires \(O(n)\) time and \(O(1)\) space.
LeetCode reports a runtime of 0 ms (beating 100% of other solutions) and a memory use of 7.78 MB (beating 95.07% of other solutions).
Super efficient!

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)