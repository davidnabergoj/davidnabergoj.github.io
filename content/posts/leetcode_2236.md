+++
date = '2025-12-01'
draft = false
tags = ['Data structures', 'LeetCode', 'C++', 'Priority queue']
title = 'LeetCode 2336: smallest number in infinite set'
[params]
    author = 'David Nabergoj'
+++

In this post, we'll be solving [LeetCode problem 2236](https://leetcode.com/problems/smallest-number-in-infinite-set).
We start with an infinite set containing all positive integers.
We may remove and return the smallest integer using `int popSmallest()` or add a positive integer back into the set using `void addBack(int num)`.

## Strategy

If we only focus on popping smallest integers, then we only need to keep track of the smallest number in the set with a variable `n`.
We start with `n = 1`.
Each time we pop, we increment `n` by one.

We only add `num` back into the set if `num < n`, as it is otherwise already in the set.
If we successfully add `num` back, it will surely be smaller than `n`, so any further `popSmallest` calls should get rid of such numbers first.
What's more, we want to pop the smallest of the added `num` values first.

We implement this with a [priority queue](https://www.geeksforgeeks.org/dsa/priority-queue-set-1-introduction/), which allows us to retrieve the smallest (alternatively, the largest) number inside it in \(O(1)\) time, pop it in \(O(\log m)\) time, and insert a new value in \(O(\log m)\) time. Here, \(m\) is the number of elements in the priority queue.

All numbers placed into the set via `addBack` are thus put into the priority queue. Whenever we call `popSmallest`, we first attempt to return the smallest value in the priority queue. If the queue is empty, we instead increment `n`.

There is a small catch: we may add the same `num` back several times. The priority queue will contain multiple copies. Such duplicates cannot appear in the infinite set. We handle this in `popSmallest` by observing the smallest element in the queue and store it into a variable `output`. We then pop from the queue until the smallest element changes. We finally return `output`.

## Full solution

Below is the full solution for the LeetCode problem. Some notes:

- To create the `minPriorityQueue` object in C++, we need specify the data type (`int`), the base data container (`vector<int>`), and the comparator which will ensure that the top element of the queue is always the smallest one inside it.
- In `popSmallest`, we write `return n++;`, which first returns `n` to a caller function, then increments its value inside the `SmallestInfiniteSet` instance. We could equivalently write `n++; return n - 1;`

During my submission, the code needed 10ms of runtime and 43.1 MB of memory.

```cpp
#include <queue>

class SmallestInfiniteSet {
public:
    int n = 1;
    priority_queue<int, vector<int>, greater<int>> minPriorityQueue;

    SmallestInfiniteSet() {

    }
    
    int popSmallest() {
        int output;
        if (!minPriorityQueue.empty()) {
            output = minPriorityQueue.top();
            while (!minPriorityQueue.empty() && output == minPriorityQueue.top()) {
                minPriorityQueue.pop();
            }
            return output;
        } else {
            return n++;
        }
    }
    
    void addBack(int num) {
        if (num < n) {
            minPriorityQueue.push(num);
        }
    }
};
```

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)