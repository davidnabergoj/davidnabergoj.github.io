+++
date = '2025-12-13'
draft = false
tags = ['Leetcode', 'C++', 'Sorting', 'Autocompletion']
title = 'Leetcode 1268: Search Suggestions System'
[params]
    author = 'David Nabergoj'
+++

Welcome back to another LeetCode walkthrough! This time, we'll be tackling [LeetCode problem 1268](https://leetcode.com/problems/search-suggestions-system).
We're given an array of strings called `products`, as well as a string `searchWord`.
Our goal is to suggest three products after typing each character of `searchWord`.
This is a tiny autocompletion method!
We could solve this problem with a Trie, like the one we implemented to solve [LeetCode problem 208](/posts/leetcode_208.md). Check it out!

But today, I felt like solving this without writing hyper-optimized or over-engineered code.
Our solution will be simple and straightforward... but still efficient!
Let's dive in.

## Strategy

Let's first sort the `products` array.

Then let's cut off the right part of `searchWord` and get a string called `prefix`.
For example, we can take `searchWord = "mouse"` and get `prefix = "mous"`.
After `products` is sorted, we can traverse it from left to right with index `i`.
One of two things may happen:

1. `products[i]` could start with `prefix` for some `i`, OR
2. No such `i` is found.

In the second case, there's nothing to suggest!
But in the first case, we can simply check the next two elements: `products[i+1]` and `products[i+2]`.
If they also start with `prefix`, we've found the three products!
It's possible that we only find one or two, in which case we return those.

### The `find` function

First let's implement a function to find a prefix `p` inside the sorted array:
```cpp
int find(vector<string> *a, string *p) {
    for (int index = 0; index < a->size(); ++index) {
        if (a->at(index).substr(0, p->size()) == *p) {
            return index;
        }
    }
    return -1;
}
```

I mentioned there won't be optimizations, but we can make a small one.
You'll see later why this is useful.
The first change: we don't compute the entire substring. Instead, we traverse `prefix` and `a->at(index)` with `i`. As soon as the characters `prefix[i]` and `a->at(index)[i]` do not match, we return `-1`. If this never happens, we return `index`.
The second change: we only start looking through the sorted array at index `start`. This will allow us to skip some entries later on and save time for long prefixes.

```cpp
int find(vector<string> *a, string *p, size_t start) {
    for (int index = start; index < a->size(); ++index) {  // begin with `start`
        bool found = true;
        for (size_t i = 0; i < p->size(); ++i) {
            if (a->at(index)[i] == p->at(i)) {
                found = false;
                break;
            }
        }
        if (found) {
            return index;
        }
    }
    return -1;
}
```

Ok, one last tiny optimization: let's pass the prefix size into `find`.
Now we won't need to create substrings of `searchWord` at all, saving some time and memory if `searchWord` is long!

```cpp
int find(vector<string> *a, string *p, size_t start, size_t prefixSize) {
    for (int index = start; index < a->size(); ++index) {
        bool found;
        for (size_t i = 0; i < prefixSize; ++i) {
            if (a->at(index)[i] == p->at(i)) {
                found = false;
                break;
            }
        }
        if (found) {
            return index;
        }
    }
    return -1;
}
```

### The `suggestedProducts` function

Now what's left is to apply `find` for different prefixes.

As stated before, we first sort the array of products.
Then we try to match increasingly bigger prefixes of `searchWord` within `products` using `find`.
Two things may happen:
1. If successful, we look for at most two extra products. Afterwards, we store the suggestions for this prefix into an array called `output`.
2. If unsuccessful, typing further characters won't change anything. We can simply skip looking for further products.

Here's the `suggestedProducts `code:

```cpp
vector<vector<string>> suggestedProducts(vector<string>& products, string searchWord) {
    std::sort(products.begin(), products.end());

    vector<vector<string>> output;
    int index = 0;

    // Check all prefixes of searchWord
    for (size_t i = 0; i < searchWord.size(); ++i) {
        // Skip the search if a previous prefix was not found
        if (index != -1) {
            // Look for searchWord's prefix inside the products array
            index = find(&products, &searchWord, index, i + 1);
        }

        vector<string> tmp;  // products for this prefix
        if (index != -1) {
            // Add the first product
            tmp.push_back(products[index]);

            // Find one or two more products
            for (size_t offset = 1; offset < 3 && (index + offset < products.size()); ++offset) {

                // Check the prefixes with size i + 1 match
                bool found = true;
                for (size_t j = 0; j < i + 1; ++j) {
                    if (products[index + offset][j] != searchWord[j]) {
                        found = false;
                        break;
                    }
                }

                if (found) {
                    // Add the product to tmp
                    tmp.push_back(products[index + offset]);
                } else {
                    // No more checking needed - lets us skip checking for the third product if the second was not found
                    break;
                }
            }
        }

        // Append the products to the full output
        output.push_back(tmp);
    }
    return output;
}
```

## Runtime, memory usage, and time complexity analysis
This code passes all tests with a runtime of 7ms and 32.2 MB memory usage.
That's better than 93 percent of all solutions in terms of runtime and better than 82 percent in terms of memory!

Let's say:
* `products` has size \(n\),
* `searchWord` has size \(m\).
* the longest product has size \(k\)

We break down the time complexity as follows:
- We perform \(O(n \log n)\) element-level comparisons during sorting. To compare the strings, we perform \(O(k)\) comparison operations. We thus spend \(O (k n \log n)\) time on sorting.
- We call `find` \(m\) times. There are fewer elements to check each time. However, each call may still scan at most \(n\) products, and the prefix length grows from \(1\) to \(m\). Each product may therefore be compared against increasingly longer prefixes. The total time spent on the `find` function is thus \(O (nm^2) \).
- Checking if the two additional products takes at most \(2m\) comparisons. Since we check this for at most \(m\) prefixes, the additional search takes \(O(m^2)\), noting that the \(2\) does not affect asymptotic complexity.

The total time complexity is thus \(O(kn \log n + nm^2) = O(n (k \log n + m^2))\).

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)