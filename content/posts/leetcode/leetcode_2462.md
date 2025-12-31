+++
date = '2025-12-29'
draft = false
tags = ['LeetCode', 'C++', 'Priority queue', 'Heap']
title = 'LeetCode 2462: Total Cost to Hire K Workers'
[params]
    author = 'David Nabergoj'
+++

Today, let's look at [LeetCode problem 2462: Total Cost to Hire K Workers](https://leetcode.com/problems/total-cost-to-hire-k-workers/).
The instructions are as follows:
> You are given a 0-indexed integer array `costs` where `costs[i]` is the cost of hiring the \(i\)-th worker.
> You are also given two integers \(k\) and `candidates`. We want to hire exactly \(k\) workers according to the following rules:
> * You will run \(k\) sessions and hire exactly one worker in each session.
> * In each hiring session, choose the worker with the lowest cost from either the first candidates workers or the last candidates workers. Break the tie by the smallest index.
>   * For example, if `costs = [3,2,7,7,1,2]` and `candidates = 2`, then in the first hiring session, we will choose the 4th worker because they have the lowest cost `[3,2,7,7,**1**,2]`.
>   * In the second hiring session, we will choose 1st worker because they have the same lowest cost as 4th worker but they have the smallest index `[3,**2**,7,7,2]`. Please note that the indexing may be changed in the process.
> * If there are fewer than candidates workers remaining, choose the worker with the lowest cost among them. Break the tie by the smallest index.
> * A worker can only be chosen once.
>
> Return the total cost to hire exactly \(k\) workers.
> 
> Constraints:
> * `1 <= costs.length <= 10^5` 
> * `1 <= costs[i] <= 10^5`
> * `1 <= k, candidates <= costs.length`

Let's dive in!

## Strategy

Let \(c\) denote `candidates` for brevity.

A naive strategy is to find the minimum from the first and last \(c\) options during each round.
Each such round requires \(2c\) checking operations, which would give a time complexity of \(O(kc)\) across \(k\) rounds.

We can do better.
Let's maintain two priority queues: `front` and `back`. `front` will hold the first \(c\) unprocessed workers and `back` will hold the last \(c\) unprocessed workers.
Each queue will grant access to its cheapest worker in \(O(1)\) time.

During each round, we decide whether to choose the worker from `front` or `back` according to their cost.
If we choose a worker from `front`, we add the next unprocessed worker from the left to `front`.
If we choose a worker from `back`, we add the next unprocessed worker from the right to `back`.
Each time we choose a worker, we increase the running cost to hire our workers.

There are two edge cases:
1. If there are at most \(k\) workers, then all workers will be accepted.
2. If there are at most \(2c\) workers (i.e., \(n \leq 2c\)), then we don't need to maintain two priority queues. We can simply sort the workers by their cost in non-decreasing order and select the first \(k\).

## Implementation

Let's look at the code below.
We maintain two indices: `left` and `right`. `left` tells us which worker we should add next to `front`. `right` tells us which worker we should add next to `back`.
We also consider a technical edge case: if `front` is empty, we choose from `back` and vice-versa.

```cpp
class Solution {
public:
    long long totalCost(vector<int>& costs, int k, int candidates) {
        long long total = 0;
        size_t n = costs.size();
        
        // Edge case 1
        if (n <= k) {
            for (size_t i = 0; i < k; ++i) {
                total += costs[i];
            }
            return total;
        }

        // Edge case 2
        if (n <= 2 * candidates) {
            sort(costs.begin(), costs.end());
            for (size_t i = 0; i < k; ++i) {
                total += costs[i];
            }
            return total;
        }

        priority_queue<int, vector<int>, greater<int>> front;
        priority_queue<int, vector<int>, greater<int>> back;

        for (size_t i = 0; i < candidates; ++i) {
            front.push(costs[i]);
            back.push(costs[n - 1 - i]);
        }

        size_t left = candidates - 1;
        size_t right = n - candidates;
        for (size_t i = 0; i < k; ++i) {
            if (front.empty() || back.top() < front.top()) {
                // Hire worker from the back
                total += back.top();
                back.pop();
                --right;
                
                if (left < right) {
                    // Add new worker to the back
                    back.push(costs[right]);
                }
            } else {
                // Hire worker from the front
                total += front.top();
                front.pop();
                ++left;

                if (left < right) {
                    // Add new worker to the front
                    front.push(costs[left]);
                }
            }
        }
        return total;
    }
};
```

We break down the time complexity as follows:
* Edge case 1 requires \(O(k)\) time to sum the costs of all workers. This is equal to \(O(n)\) since this case only occurs when \(k \geq n\).
* Edge case 2 requires \(O(n \log n)\) time to sort the workers. Summing the first \(k\) costs takes \(O(k)\) time, but this is subsumed by the sorting time.
* The general case:
  * We first make \(c\) insertions to each priority queue, with each insertion requiring \(O(\log c)\) time. The time complexity for queue construction is thus \(O(c \log c)\).
  * We then make \(k\) iterations. In each iteration, we perform exactly one removal in \(O(\log c)\) time and at most one insertion in \(O(\log c)\) time. Overall, we need \(O(k \log c)\) time for queue processing.

The worst-case time complexity over all inputs is \(O(n\log n\).
However, time complexity becomes \(O(c\log c + k \log c)\) for inputs that are not edge cases.

Since each priority queue holds at most \(c\) elements, the auxiliary space complexity is \(O(c)\).

LeetCode indeed reports a runtime of 15 ms (beating 99.48% of other solutions) and a memory use of 74.50 MB (beating 100% of other solutions).

## Minor optimization

Instead of constructing the heaps via incremental insertions into a `priority_queue` (which costs \(O(c\log c)\) time), we can build them in linear time using `make_heap` (see the [documentation](https://en.cppreference.com/w/cpp/algorithm/make_heap.html)).
We construct `front` and `back` as `vector` objects and not priority queues.
We then push the unsorted worker costs into each one in \(O(c)\) time.
Finally, we call `make_heap`, which only requires \(O(c)\) time.
We also have to modify the push and pop calls, though the high-level logic stays the same.

Let's look at the modified code for the general case.
The actual runtime and space usage are pretty much the exact same, though this method is theoretically slightly faster, requiring \(O(c)\) time to construct each heap instead of the previous \(O(c \log c)\).

```cpp
vector<int> front, back;
front.reserve(candidates);
back.reserve(candidates);

for (int i = 0; i < candidates; ++i) {
    front.push_back(costs[i]);
    back.push_back(costs[n - 1 - i]);
}

// Build min-heaps
make_heap(front.begin(), front.end(), greater<int>());
make_heap(back.begin(), back.end(), greater<int>());

size_t left = candidates - 1;
size_t right = n - candidates;
for (int i = 0; i < k; ++i) {
    if (front.empty() || (!back.empty() && back.front() < front.front())) {
        // Take from back
        total += back.front();
        pop_heap(back.begin(), back.end(), greater<int>());
        back.pop_back();
        --right;

        if (left < right) {
            back.push_back(costs[right]);
            push_heap(back.begin(), back.end(), greater<int>());
        }
    } else {
        // Take from front
        total += front.front();
        pop_heap(front.begin(), front.end(), greater<int>());
        front.pop_back();
        ++left;

        if (left < right) {
            front.push_back(costs[left]);
            push_heap(front.begin(), front.end(), greater<int>());
        }
    }
}
```

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)

Side note: with this blog post, I've finished the [LeetCode 75 problem list](https://leetcode.com/studyplan/leetcode-75/)! 🥳🎉🎉🎉