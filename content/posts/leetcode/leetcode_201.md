+++
date = '2026-01-04T11:54:00+01:00'
draft = false
tags = ['LeetCode', 'C++', 'Bit manipulation']
title = 'LeetCode 201: Bitwise AND of Numbers Range'
[params]
    author = 'David Nabergoj'
+++

Let's solve [LeetCode problem 201: Bitwise AND of Numbers Range](https://leetcode.com/problems/bitwise-and-of-numbers-range).

The instructions are as follows:
> Given two integers `left` and `right` that represent the range `[left, right]`, return the bitwise AND of all numbers in this range, inclusive.
>
> Constraints:
> * `0 <= left <= right <= 2^31 - 1`

Let's dive in!

## Looking at the common binary prefix

Let \(l\) and \(r\) denote `left` and `right`, respectively.
I'll denote binary strings with `0b...`, for example 5 can be written as `0b101`.

Performing bitwise AND on the entire range would take \(O(r - l)\) time, which is impractical.
However, we don't have to check the entire range.
Let's take the binary numbers \(l =\) `0b10011011001` and \(r = \) `0b10011011111` as an example.
These two have a common prefix: `0b10011011`. They only differ in the last three bits.
What numbers are present in the range? Let's list them:
* `0b10011011|001`: the left bound \((l)\),
* `0b10011011|010`,
* `0b10011011|011`,
* `0b10011011|100`,
* `0b10011011|101`,
* `0b10011011|110`,
* `0b10011011|111`: the right bound \((r)\).

I use a vertical line to separate the common prefix and the variable part in each number.

Clearly, the common prefix is the same for all numbers in the range, so it will also appear in the final bitwise AND result.

## Handling the variable binary suffixes

We now only have to check the bitwise AND of the suffixes.
However, this range can still be quite big.
We can make another observation: the bitwise AND of the suffixes will always be equal to zero.
How can we tell?

Consider \(l\) and \(r\). The first differing bit after the common prefix will always be different, as they would otherwise belong to the common prefix.
In fact, the leading bit of \(r\)'s suffix will be 1 and the leading bit of \(l\)'s suffix will be 0. That's because \(r \geq l\) implies that the suffix of \(r\) is strictly greater than the suffix of \(l\).
Consequently, the range \([l, r]\) contains a number \(n\) with suffix `0b100...0`.
This is a bit string of all zeros, except for `1` at the start of the suffix.
Suppose \(n\) has `1` at bit at index \(m\). Then the zeroes after it cancel out everything in the suffix at indices \([m-1, m-2, \dots, 0]\).

Now for the final step: since \(l\)'s suffix starts with `0` and \(r\)'s suffix starts with `1`, the bitwise AND on the first suffix position is necessarily `0` as well.

In conclusion: the bitwise AND of all suffix strings is equal to zero!

## Implementation

To find the bitwise AND of the entire range, it would be practical to see where the prefix ends and the suffix starts.

What happens if we apply the bitwise XOR operation on \(l\) and \(r\)?
Let's use our previous example: \(l =\) `0b10011011001` and \(r = \) `0b10011011111`.
The bitwise XOR is equal to `0b00000000101`. Since both representations have leading zeros, the bitwise XOR actually yields `0b0...0|00000000101`. This gives all prefix bits a value of `0`.
This number's [most significant bit (MSB)](https://en.wikipedia.org/wiki/Bit_numbering)  represents the first index of the suffix!

We know that the entire prefix will be retained.
We can construct a bit mask to set the suffix to zero:
* Shift `0b1` to the left by `msb + 1`.
* Subtract one.
* Apply bitwise negation.

Let's see it on our example. The MSB is equal to 2. Shifting `0b1` to the left by 3 gives `0b1000`. Subtracting one gives `0b0111` or `0b0...0111` if we include all bits. Negating this gives `0b1...1000`. The mask is equal to `1` on the prefix bits and `0` on the suffix bits.
Finally, we just have to compute the bitwise AND between (1) either \(l\) or \(r\) and (2) our newly computed mask.

Let's see the code: 
```cpp
class Solution {
public:
    int clz(int x) {
        if (x == 0) {
            return 32;
        }
        return __builtin_clz(x);
    }

    int rangeBitwiseAnd(int left, int right) {
        int msb = 31 - clz(left ^ right);
        if (msb == -1) {
            return left;
        } else if (msb == 30) {
            return 0;
        }
        return left & ~((1 << (msb + 1)) - 1);
    }
};

// Examples:
// rangeBitwiseAnd(0, 0) ... returns 0
// rangeBitwiseAnd(5, 7) ... returns 4
// rangeBitwiseAnd(26, 31) ... returns 24
// rangeBitwiseAnd(123, 99) ... returns 96
// rangeBitwiseAnd(1, 2147483647) ... returns 0
```

I computed the MSB using a function `clz` that <u>C</u>ounts the number of <u>L</u>eading <u>Z</u>eroes. GCC/Clang supports `__builtin_clz(x)`, which returns the number of leading zeros in a 32-bit integer. On other compilers or languages, you may need a custom function.

The maximum MSB index is 31 since there are 32 bits in an integer. 
The smallest MSB index is 0, corresponding to `0b1`. 

I used -1 as an indicator of where all bits are zero.
In this case, the values of `left` and `right` are exactly the same on each bit, so the range only has one element and we return `left`.
If the MSB is 30, shifting by 31 would overflow, but we can directly return 0 because all suffix bits will be zeroed.

Finally, we construct the suffix mask in one line: `~((1 << (msb + 1)) - 1)`.

The code runs in \(O(1)\) time, as we only perform a few bit operations. The function `__builtin_clz` typically runs in one CPU instruction, which is extremely fast.
We use a single integer to store `msb`, which makes our space complexity \(O(1)\) as well.
This solution avoids loops entirely, which is the main downside of other solutions.

LeetCode naturally reports a runtime of 0 ms, beating 100% of other solutions. The memory usage is 11.11 MB to run LeetCode's automated tests, but the extra space takes next to nothing.

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)