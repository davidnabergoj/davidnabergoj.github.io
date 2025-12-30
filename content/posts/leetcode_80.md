+++
date = '2025-12-30'
draft = false
tags = ['LeetCode', 'C++', 'Array']
title = 'LeetCode 80: Remove Duplicates from Sorted Array II'
[params]
    author = 'David Nabergoj'
+++

Today, let's look at [LeetCode problem 80: Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii).
The instructions are as follows:
> Given an integer array `nums` sorted in non-decreasing order, remove some duplicates in-place such that each unique element appears at most twice. The relative order of the elements should be kept the same.
>
> Since it is impossible to change the length of the array in some languages, you must instead have the result be placed in the first part of the array `nums`. More formally, if there are \(k\) elements after removing the duplicates, then the first \(k\) elements of `nums` should hold the final result. It does not matter what you leave beyond the first \(k\) elements.
>
> Return \(k\) after placing the final result in the first \(k\) slots of `nums`.
> Do not allocate extra space for another array. You must do this by modifying the input array in-place with \(O(1)\) extra memory.
>
> Constraints:
> * `1 <= nums.length <= 3 * 10^4`
> * `-10^4 <= nums[i] <= 10^4`
> * `nums` is sorted in non-decreasing order.

Let's dive in!

## Strategy and implementation

This one is quite straightforward. Let's maintain two indices:
* A *read index* \(i\) that will determine the next element to read.
* A *write index* \(j\) that will determine where the next element will be written.

We'll increase \(i\) until we've reached the end of the array.
We'll increase \(j\) depending on how many elements we write.

When increasing \(i\), let's ensure that `nums[i]` is always a new number.
If we that's true, then we have two possibilities at every step:
1. `nums[i]` and `nums[i+1]` are different.
2. `nums[i]` and `nums[i+1]` are the same.

In the first case, we set `nums[j]` to `nums[i]` and increase both \(i\) and \(j\) by one.
In the second case:
* We set `nums[j]` to `nums[i]`.
* We set `nums[j + 1]` to `nums[i + 1]`.
* We increase \(j\) by two (as we've written two elements).
* We increase \(i\) until we find a new number.

After the code finishes, \(j\) will be the count of written numbers.
That's exactly what we want to return, i.e., \(k = j\).
Let's implement this! The full code is shown below.

```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        size_t j = 0;  // j: write index
        size_t i = 0;  // i: read index
        while (i < nums.size()) {
            if (i + 1 < nums.size() && nums[i + 1] == nums[i]) {
                // Two in a row
                nums[j] = nums[i];
                nums[j + 1] = nums[i + 1];
                j += 2;
                i += 2;

                // Skip remaining duplicates
                while (i < nums.size() && nums[i] == nums[i - 1]) {
                    ++i;
                }
            } else {
                // Only one
                nums[j] = nums[i];
                j += 1;
                i += 1;
            }
        }
        return j;
    }
};
```

The extra space complexity is \(O(1)\) as we only create two new indices.
The time complexity is \(O(n)\), where \(n\) is the size of `nums`.
Each element of `nums` is read exactly once. Even though there's an inner while loop, it only skips duplicates and advances \(i\). Hence, the total number of operations is proportional to \(n\).

Running the code takes 3 ms (beating 95.64% of other solutions) and needs 19.54 MB of memory (beating 56.92% of other solutions). This seems to be due to LeetCode's testing system overhead, as there's not much that could be improved in terms of memory.

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)