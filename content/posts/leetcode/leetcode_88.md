+++
date = '2025-12-29'
draft = false
tags = ['LeetCode', 'C++', 'Array']
title = 'LeetCode 88: Merge Sorted Array'
[params]
    author = 'David Nabergoj'
+++

Let's solve [LeetCode problem 88: Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/).
This problem is quite short and straightforward.

The instructions are as follows:
> You are given two integer arrays `nums1` and `nums2`, sorted in non-decreasing order, and two integers \(m\) and \(n\), representing the number of elements in `nums1` and `nums2` respectively.
> Merge `nums1` and `nums2` into a single array sorted in non-decreasing order.
> The final sorted array should not be returned by the function, but instead be stored inside the array `nums1`. To accommodate this, `nums1` has a length of \(m\) + \(n\), where the first \(m\) elements denote the elements that should be merged, and the last \(n\) elements are set to 0 and should be ignored. `nums2` has a length of \(n\).
>
> Constraints:
> * `nums1.length == m + n`
> * `nums2.length == n`
> * `0 <= m, n <= 200`
> * `1 <= m + n <= 200`
> * `-10^9 <= nums1[i], nums2[j] <= 10^9`

Let's dive in!

## Strategy and implementation

We have to write the result to `nums1`.
If we process the numbers from highest to lowest, we can avoid overwriting the first \(m\) values of `nums1`.
Let's create two indices `i, j` and initialize them to `i = m - 1, j = n - 1`.
We will decrement these indices until they both reach 0.
At each step, we do the following:
* If `nums1[i] > nums2[j]`, then we write `nums1[i]` to the next slot and decrement `i`.
* Otherwise, we write `nums2[j]` to the next slot and decrement `j`.

The index of the next slot is simply `i + j + 1`.

When `i` reaches 0, we write all the remaining values in `nums2` to the start of `nums1`.
When `j` reaches 0, we *could* write all the remaining values in `nums1` to the start of `nums1`. However, this is already the case! Thus, we don't need to do anything at all.

Let's look at the code below.
I usually use `size_t` as the index data type, however I opted for `int` as decrementing past 0 causes an overflow and breaks the program.

```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int i = m - 1;
        int j = n - 1;

        while (i >= 0 && j >= 0) {
            if (nums1[i] >= nums2[j]) {
                nums1[i + j + 1] = nums1[i];
                i--;
            } else {
                nums1[i + j + 1] = nums2[j];
                j--;
            }
        }

        while (j >= 0) {
            nums1[j] = nums2[j];
            j--;
        }
    }
};
```

LeetCode reports a runtime of 0 ms (beating 100% of other solutions) and memory usage of 12.17 MB (beating 95.41% of other solutions).
I'm pretty sure this is effectively optimal and the memory usage is the lowest it could be.

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)