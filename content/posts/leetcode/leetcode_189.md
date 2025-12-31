+++
date = '2025-12-31T10:00:00+01:00'
draft = false
tags = ['LeetCode', 'C++', 'Array']
title = 'LeetCode 189: Rotate Array'
[params]
    author = 'David Nabergoj'
+++

Happy new year! 🥳🎉🎉🎉 Let's make 2026 awesome!

Let's solve [LeetCode problem 189: Rotate Array](https://leetcode.com/problems/rotate-array).
The instructions are as follows:
> Given an integer array `nums`, rotate the array to the right by \(k\) steps, where \(k\) is non-negative.
>
> Constraints:
> * `1 <= nums.length <= 10^5`
> * `-2^31 <= nums[i] <= 2^31 - 1`
> * `0 <= k <= 10^5`


This problem is fairly straightforward. I'll present three solutions in C++.
Let's dive in!

Note: rotations have a sort of cyclical property. If \(k\) is greater or equal to \(n\) (the length of `nums`), then rotating `nums` by with \(k\) is the same as rotating it with \(k\) modulo \(n\) -- the remainder after dividing \(k\) by \(n\).
We will always work with the remainder instead of the original \(k\).

## Solution 1: the built-in rotate function
The simplest solution is to simply call `rotate` from `algorithm`.
Its declaration is `rotate(ForwardIt first, ForwardIt middle, ForwardIt last)`, while its [documentation](https://en.cppreference.com/w/cpp/algorithm/rotate.html) states:
>  `std::rotate` swaps the elements in the range `[first, last)` in such a way that the elements in `[first, middle)` are placed after the elements in `[middle, last)` while the orders of the elements in both ranges are preserved.

The middle point is \(k\) steps from the end.
Let's look at the code below.

```cpp
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        // Use std::rotate from <algorithm>
        std::rotate(
            nums.rbegin(), 
            nums.rbegin() + (k % nums.size()), 
            nums.rend()
        );
    }
};
```

Let's check what happens on an example: `nums = [1, 2, 3, 4, 5, 6, 7, 8]` and \(k = 3\).
The correct solution is `[6, 7, 8, 1, 2, 3, 4, 5]`.

The iterators `rbegin()` and `rend()` provide a reverse view of the vector, creating the following ranges:
* `[first, middle) = [8, 7, 6)`.
* `[middle, last) = [5, 4, 3, 2, 1)`.
`std::rotate` would create then the sequence `[5, 4, 3, 2, 1, 8, 7, 6]`.
However, this is only a view through reverse iterators. 
The actual result is reversed: `[6, 7, 8, 1, 2, 3, 4, 5]`.

While the C++ standard does not define how `std::rotate` is implemented, it does run in \(O(n)\) time and \(O(1)\) space.

## Solution 2: using an auxiliary vector

Another possible solution is to create a short vector `u` with size \(k\).
We store the last \(k\) elements of `nums` inside `u`.
We then shift all elements of `nums` by \(k\) to the right (except for the last \(k\)).
Finally, we overwrite the first \(k\) elements of `nums` with `u`.

Let's see the code.
I use `copy_backward` to shift the values. However, we could easily implement this with a for/while loop.

```cpp
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        k %= nums.size();
        if (k == 0) {
            return;
        }

        // Store the last k values in an auxiliary vector
        vector<int> u(
            nums.end() - k, 
            nums.end()
        );

        // shift the first n-k values to the right by k
        copy_backward(
            nums.begin(),
            nums.end() - k,
            nums.end()
        );

        // Copy the last k values to the start
        copy_backward(
            u.begin(),
            u.end(),
            nums.begin() + k
        );
    }
};
```

The construction of `u` takes \(O(k)\) time and space, while `copy_backward` takes \(O(n)\) time and \(O(1)\) space.
The total is thus \(O(n)\) time (since \(k < n\)) and \(O(k)\) space.

## Solution 3: reversing parts of the vector

The last option we'll consider is this:
1. Reverse `nums`.
2. Reverse the first \(k\) elements of `nums`.
3. Reverse the last \(n - k\) elements of `nums`.

Let's check what happens on an example: `nums = [1, 2, 3, 4, 5, 6, 7, 8]` and \(k = 3\).
The correct solution is `[6, 7, 8, 1, 2, 3, 4, 5]`.

1. The first reversal creates `[8, 7, 6, 5, 4, 3, 2, 1]`.
2. The second reversal makes `[6, 7, 8, 5, 4, 3, 2, 1]`.
3. The third reversal makes `[6, 7, 8, 1, 2, 3, 4, 5]`, which is the correct solution.

Let's look at the code.
I use `std::reverse` from `algorithm`, but we could easily implement it in linear time with a for/while loop.

```cpp
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        k %= nums.size();
        if (k == 0) {
            return;
        }

        // O(1) memory and O(3n) = O(n) time
        reverse(nums.begin(), nums.end());
        reverse(nums.begin(), nums.begin() + k);
        reverse(nums.begin() + k, nums.end());
    }
};
```

This solution needs \(O(1)\) extra space. The three reversals take \(O(n)\), \(O(k)\), and \(O(n-k)\) time, respectively. Since \(k < n\), we can simply say the time complexity is \(O(n)\).

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)