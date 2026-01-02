+++
date = '2026-01-02T22:25:00+01:00'
draft = false
tags = ['LeetCode', 'C++', 'Array', 'Bucket sort']
title = 'LeetCode 347: Top K Frequent Elements'
[params]
    author = 'David Nabergoj'
+++

Let's solve [LeetCode problem 347: Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements).

The instructions are as follows:
> Given an integer array `nums` and an integer \(k\), return the \(k\) most frequent elements. You may return the answer in any order.
>
> Constraints:
> * `1 <= nums.length <= 10^5`
> * `-10^4 <= nums[i] <= 10^4`
> * \(k\) is in the range `[1, the number of unique elements in the array]`.
> * It is guaranteed that the answer is unique.

I solved this problem on [NeetCode](https://neetcode.io/problems/top-k-elements-in-list) as well. The goal was to find a solution in \(O(n)\) time and space, where \(n\) is the length of `nums`.
I'll be showing this solution here. 
Even though some solutions that take \(O(n\log n)\) time are faster for LeetCode leaderboards, this solution will scale a bit better.
Let's dive in!

## Strategy

The basic strategy is as follows:
1. Store the count of each element in a vector `counts`.
2. Create a vector `buckets`. Store each element of `nums` inside its bucket. The bucket is determined based on the element's frequency.
3. The last buckets hold the most frequent elements. Iterate buckets from right to left and return the top \(k\) values.

The bucketing idea is inspired by the [Bucket sort](https://en.wikipedia.org/wiki/Bucket_sort) algorithm, which can achieve an average time complexity of \(O(n)\) when the number of buckets is proportional to \(n\).

## Counting elements
Since the value domain contains at most \(U = 20001\) possible integers, we can count frequencies using a fixed-size vector `counts`. Initially, all its values are zero. As we loop through `nums`, we increment `counts[nums[i] + 10000]` for all indices `i`.
We add 10000 as the offset. This maps numbers from `[-10000, 10000]` to the desired index range `[0, 20000]`.

```cpp
int n = nums.size();
vector<int> counts(20001, 0);
int maxCount = 0;
for (int i = 0; i < n; ++i) {
    int index = nums[i] + 10000;
    counts[index]++;
    maxCount = max(maxCount, counts[index]);
}
```

This part of code takes \(O(n)\) time. It takes \(O(U)\) space. According to problem constraints, this could be treated as \(O(1)\), though \(U\) is useful if we increase the permissible range of `nums`.

## Bucketing elements

Let's make a vector `buckets`, whose elements are empty vectors.
We can take an element's count and place the element into a bucket based on its count.
If its count is high, it should be placed towards the end of `buckets`.
If it's low, it should be at the start.
Suppose we can ensure that elements with greater counts are placed into later buckets than elements with smaller counts.
Then we only have to iterate `buckets` from right to left, scanning all buckets for the first \(k\) elements.

How do we ensure such an ordering?
We first note the maximum count of all elements `maxCount`.
We can determine the bucket index as `bin = floor(n * count / (maxCount + 1))`, where `count` is the number of occurrences of a particular element.
After computing `bin`, we can push the corresponding element into `buckets[bin]`.
This ensures that elements with greater counts appear later in the `buckets` vector.

```cpp
int m = 1 + maxCount;
vector<vector<int>> buckets(n);
for (int i = -10000; i <= 10000; ++i) {
    if (counts[i + 10000] > 0) {
        int bin = n * counts[i + 10000] / m;
        buckets[bin].push_back(i);
    }
}
```

This takes \(O(n + d)\) space: we first allocate space for \(n\) buckets, then we push \(d\) distinct elements into selected buckets. 
Although buckets may be uneven, the total number of inserted elements is \(d\), so the overall traversal remains linear.
We can also simplify \(O(n + d)\) to \(O(n)\) since \(d \leq n\).
The time complexity of this snippet is \(O(U)\).

Finally, we iterate the buckets from right to left.
We keep a vector `output` that holds our result.
For each non-empty bucket, we push its entries into `output` until we've obtained \(k\) elements.

```cpp
vector<int> output;
for (int i = n - 1; i >= 0 && k > 0; --i) {
    if (buckets[i].size() > 0) {
        for (int j = 0; j < buckets[i].size() && k > 0; ++j) {
            output.push_back(buckets[i][j]);
            --k;
        }
    }
}
```

It takes \(O(k)\) space to create the output vector, which is bounded above by \(O(n)\) as \(k \leq n\).
This snippet takes \(O(n + d)\) time: we iterate through at most \(n\) buckets. There are at most \(d\) elements in all buckets combined. Therefore, the inner loop does not cause a quadratic time complexity. We can again simplify: \(O(n + d) = O(n)\) as \(d \leq n\).

## Full solution and final complexity analysis

Let's see the full code.

```cpp
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        // Compute element counts
        int n = nums.size();
        vector<int> counts(20001, 0);
        int maxCount = 0;
        for (int i = 0; i < n; ++i) {
            int index = nums[i] + 10000;
            counts[index]++;
            maxCount = max(maxCount, counts[index]);
        }

        // Apply bucketing
        int m = 1 + maxCount;
        vector<vector<int>> buckets(n);
        for (int i = -10000; i <= 10000; ++i) {
            if (counts[i + 10000] > 0) {
                int bin = n * counts[i + 10000] / m;
                buckets[bin].push_back(i);
            }
        }

        // Retrieve top k elements
        vector<int> output;
        for (int i = n - 1; i >= 0 && k > 0; --i) {
            if (buckets[i].size() > 0) {
                for (int j = 0; j < buckets[i].size() && k > 0; ++j) {
                    output.push_back(buckets[i][j]);
                    --k;
                }
            }
        }

        return output;
    }
};
```

The total time and space complexities are \(O(n + U)\), and both reduces to \(O(n)\) under the problem constraints.

The code runs in 0 ms (beating 100% of other solutions) and takes 21.84 MB memory (beating 10%). I think this is an asymptotically optimal solution to this problem, so it will scale well with \(n\). We could have counted the elements using a hashmap, which would bring down the space complexity for counting from \(O(U)\) to \(O(d)\). Other efficient solutions include using a priority queue which holds `(count, element)` pairs and can thus output the top \(k\) elements in \(O(k \log n)\) time. However, its construction takes \(O(n \log n)\) time, making it asymptotically slower than our solution.

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)