+++
date = '2025-12-03'
draft = false
tags = ['Data structures', 'Leetcode', 'C++', 'Priority queue']
title = 'Leetcode 2542: Maximum Subsequence Score'
[params]
    author = 'David Nabergoj'
+++

In this post, we'll be solving [LeetCode problem 2542](https://leetcode.com/problems/maximum-subsequence-score).
We have two integer arrays `nums1` and `nums2` with size \(n\), as well as an integer \(k\).
Let's denote `nums1` with \(A\) and `nums2` with \(B\).
If we pick a set of indices \(S\) with exactly \(k\) elements, we obtain a score as follows:
\[
    \min \left\{ B_i | i \in S \right\} \sum_{i \in S} A_i
\]
There are many different sets \(S\) that give different scores.
We want to find the maximum possible score.

## Strategy

The total number of possible sets is \(n! / (k! (n-k)!)\), which is too big to make a brute-force solution feasible.
We want an more effective way of picking the indices.
If we can somehow sort the two arrays, then we may be able to observe \(k\) elements from left to right in a sliding window manner.

Let's try sorting \(B\) in non-increasing order. Because the two arrays are connected, we sort \(A\) using the same order.
Each value of \(B\) will now be less or equal to the previous one. What's more, it will be less than the previous \(k - 1\) values. This will be our minimum term for the score.

We will work with a vector of pairs to make sorting easy. This requires an extra \(O(n)\) space, but the overall space complexity is \(O(n)\) anyway for the input arrays. The sort function will sort \(A\) and \(B\) together according to values of \(B\) first. This is because they are the first in each pair.
We use `rbegin` and `rend` to get a reversed ascending-order output, which is equivalent to a standard descending-order output.

```cpp
vector<pair<int, int>> pairs(nums1.size());
for (size_t i = 0; i < pairs.size(); i++) {
    pairs[i] = {nums2[i], nums1[i]};
}
std::sort(rbegin(pairs), rend(pairs));
```

From this point, we treat \(A\) and \(B\) to be sorted as discussed above.

If we scan the arrays from left to right, we could impose a size \(k\) sliding window over \(A\) to keep track of the sum term. But there's a catch! Say we're at index \(i\). It's possible that there's an element \(A_j\) at index \(j < i\) that greatly contributes to the score, but is not within the window \((j \leq i - k)\). The corresponding index \(j\) could still be in the solution set \(S\) and the minimum term would not change, as \(B_j\) cannot be less than \(B_i\).

Instead of a sliding window, we can keep a [priority queue](https://www.geeksforgeeks.org/dsa/priority-queue-set-1-introduction/) of size \(k\). The first element is the smallest element of \(A\) up to \(i\) -- the current index.
As we loop through the sorted array, we fill the priority queue until it's too large, at which point we pop the smallest element.
This effectively defines \(S\) as indices between \(0\) and \(i\), with \(i\) always being included.
At the same time, \(B_i\) is always the minimum term for the score computation.
We keep track of the current sum term value, making sure to subtract values that we pop from the priority queue.

```cpp
priority_queue<int, vector<int>, greater<int>> pq;  // min-heap (smallest element on top)
long long currentSum = 0;
for (size_t i = 0; i < k - 1; i++) {
    pq.push(pairs[i].second);
    currentSum += pairs[i].second;
}

// Start the second loop at k - 1, after the priority queue
long long bestScore = 0;
for (size_t i = k - 1; i < pairs.size(); i++) {
    pq.push(pairs[i].second);
    currentSum += pairs[i].second;  // Update the sum term

    // Ensure we are always observing at most k elements
    if (pq.size() > k) {
        currentSum -= pq.top();  // Correct the sum term
        pq.pop();
    }

    // Update the best score after the priority queue has exactly k elements
    bestScore = max(bestScore, currentSum * pairs[i].first);
}
```

## Full solution and time complexity analysis
Below is the full code! It passes all tests with a runtime of 71ms and 94.8 MB memory usage.

We break down the time complexity as follows:
- we need \(O(n \log n)\) time for sorting.
- we perform \(n\) priority queue insertions, each with complexity \(O(\log k)\), leading to \(O (n \log k) \) time for insertions.
- we perform \(n - (k - 1)\) priority queue pops, each with complexity \(O(\log k)\), leading to \(O (n \log k) \) time for popping since \(n \geq k\).

The total runtime is dominated by the initial sorting process.
If \(k\) is much less than \(n\), then overall complexity is \(O(n \log n)\).
Otherwise, we can more precisely say the time complexity is \(O(n\log n + n\log k)\).

```cpp
#include <algorithm>
#include <queue>

class Solution {
public:
    long long maxScore(vector<int>& nums1, vector<int>& nums2, int k) {
        vector<pair<int, int>> pairs(nums1.size());
        for (size_t i = 0; i < pairs.size(); i++) {
            pairs[i] = {nums2[i], nums1[i]};
        }
        std::sort(rbegin(pairs), rend(pairs));

        priority_queue<int, vector<int>, greater<int>> pq;
        long long currentSum = 0;
        for (size_t i = 0; i < k - 1; i++) {
            pq.push(pairs[i].second);
            currentSum += pairs[i].second;
        }

        long long bestScore = 0;
        for (size_t i = k - 1; i < pairs.size(); i++) {
            pq.push(pairs[i].second);
            currentSum += pairs[i].second;

            if (pq.size() > k) {
                currentSum -= pq.top();
                pq.pop();
            }
            
            bestScore = max(bestScore, currentSum * pairs[i].first);
        }

        return bestScore;
    }
};
```

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/david-nabergoj). More LeetCode solutions coming soon :)