+++
date = '2025-11-30T13:45:28+01:00'
draft = false
tags = ['Data structures', 'Leetcode', 'C++', 'Recursion', 'Trie']
title = 'Leetcode 208: Trie implementation'
[params]
    author = 'David Nabergoj'
+++

A [trie](https://en.wikipedia.org/wiki/Trie) is data structure which holds strings. 
It allows fast search and insertion of strings, as well as fast search of string prefixes.
This can be especially useful for text autocompletion and spellcheck.

In this post, we implement a trie in C++ with three basic operations: `search`, `insert`, and `startsWith`.
This is the solution to [LeetCode 208](https://leetcode.com/problems/implement-trie-prefix-tree/description/).
The problem assumes that all words use characters a-z in the English alphabet, but our solution can be extended to include more characters.

# `Trie` class 
We implement the trie as a a tree.
Each node contains a fixed vector of pointers (called `next`) to tries below it.
Each pointer corresponds to a different character.
For example, `next['f']` corresponds to words with the substring.

We use a boolean `wordEnd` indicating that there exists a word that ends in the current trie root.
An example where this is useful: the trie contains the word "apple", but not "app". Searching for "app" would otherwise be possible, as there exists a path from the root that contains all characters of the word "app".
This boolean lets us avoid the ambiguity.

We create a helper method `getIndex` that takes a character of

```cpp
class Trie {
private:
    vector<Trie*> next = vector<Trie*>(26, nullptr);
    bool wordEnd = false;
    
    int getIndex(char query) {
        return query - 'a';
    }
public:
    Trie() {}  // Class constructor
}
```

# Recursive solution

## `search`
To search for a word in the trie, we start at the root and check if the pointer `next[c]`, corresponding to the first character `c`, is not null.
If it is null, the word is not present and we return `false`.
If it isn't, we recursively search if the remainder of the word is found in `next[c]`.
If the word has no characters, we return `true`. This is the base case for recursion.

```cpp
bool search(string word) {
    if (word.size() == 0) {
        return wordEnd;
    }

    int index = getIndex(word[0]);
    if (!next[index]) {
        return false;
    }
    return next[index]->search(word.substr(1));
}
```

It's important to note that `word.substr(1)` creates another string object with just one element less than the original `word`. If \(m\) is the length of the `word`, this makes the recursion's time complexity to \(O(m^2)\) and results in \(O(m^2)\) total allocated space. We could avoid allocating new memory by either:

- treating word as a character array and incrementing the pointer to its beginning,
- passing in the index of the current word character, or
- using `std::string_view` from C++17 and greater.

This would reduce the time complexity to the much more preferable \(O(m)\) and requires no additional allocated space. However, we keep this recursive implementation as it does not modify LeetCode's framework. We'll see later that an iterative solution makes this easy and avoids pointer manipulation.

## `insert`
Inserting a word into the trie is conceptualy similar to searching for it.
We check if there is a trie, corresponding to the first character of `word`.
If not, we create a trie for that character.
Now that the trie surely exists, we insert the remainder of `word` into it.
If the word has no characters, it has been fully inserted into the trie.
At that point, we set `wordEnd = true` to indicate that the word belongs to the trie (separating it from its substrings).

```cpp
void insert(string word) {
    if (word.size() == 0) {
        wordEnd = true;
        return;
    }

    int idx = getIndex(word[0]);
    if (!(next[idx])) {
        // If the Trie does not exist yet
        next[idx] = new Trie();
    }
    next[idx]->insert(word.substr(1));
}
```

## `startsWith`

The `startsWith` function is very similar to the `search` function.
The key difference is that the prefix may not have been inserted into the trie, but the path to it still exists.
The only difference between `search` and `startsWith` is thus not checking if the word is contained in the trie during the recursive base case.

```cpp
bool startsWith(string prefix) {
    /* Returns `true` if there is a previously inserted word
       that starts with `prefix`, and `false` otherwise. */
    if (prefix.size() == 0) {
        return true;
    }
    int index = getIndex(prefix[0]);
    if (!next[index]) {
        return false;
    }
    return next[index]->startsWith(prefix.substr(1));
}
```

## Full recursive solution

We provide the full recursive solution below.

```cpp
class Trie {
    vector<Trie*> next = vector<Trie*>(26, nullptr);
    bool wordEnd = false;
    
    int getIndex(char query) {
        return query - 'a';
    }
public:
    Trie() {
    }
    
    void insert(string word) {
        if (word.size() == 0) {
            wordEnd = true;
            return;
        }

        int idx = getIndex(word[0]);
        if (!(next[idx])) {
            // If the Trie does not exist yet
            next[idx] = new Trie();
        }
        next[idx]->insert(word.substr(1));
    }
    
    bool search(string word) {
        if (word.size() == 0) {
            return wordEnd;
        }

        int index = getIndex(word[0]);
        if (!next[index]) {
            return false;
        }
        return next[index]->search(word.substr(1));
    }
    
    bool startsWith(string prefix) {
        if (prefix.size() == 0) {
            return true;
        }

        int index = getIndex(prefix[0]);
        if (!next[index]) {
            return false;
        }
        return next[index]->startsWith(prefix.substr(1));
    }
};
```

# Iterative solution
The recursive solution is valid, but contains two sources of avoidable overhead:
- For even moderately long words, we risk stack overflow and add considerable CPU overhead.
- Recursive calls use stack memory proportional to recursion depth (word length).

We can avoid recursively entering a new stack frame when inserting a new word.
Instead, we start with the root trie pointer and iteratively change it within a loop over word characters.
Staying in a single frame like this is faster and uses less space. 
We modify the `search` and `startsWith` functions in a similar manner.

Below is the fully modified iterative solution.

```cpp
class Trie {
    vector<Trie*> next = vector<Trie*>(26, nullptr);
    bool wordEnd = false;

    int getIndex(char query) {
        return query - 'a';
    }
public:
    Trie() {}  // Class constructor
    
    void insert(string word) {
        // Insert `word` into the trie
        Trie* t = this;
        for (int i = 0; i < word.size(); i++) {
            int idx = getIndex(word[i]);
            if (!t->next[idx]) {
                t->next[idx] = new Trie();
            }
            t = t->next[idx];
        }
        t->wordEnd = true;
    }
    
    bool search(string word) {
        // Return true if the trie contains `word` and false otherwise
        Trie* t = this;
        for (int i = 0; i < word.size(); i++) {
            int idx = getIndex(word[i]);
            if (!t->next[idx]) {
                return false;
            }
            t = t->next[idx];
        }
        return t->wordEnd;
    }
    
    bool startsWith(string prefix) {
        // Return true if the trie contains a word that starts with `prefix`
        Trie* t = this;
        for (int i = 0; i < prefix.size(); i++) {
            int idx = getIndex(prefix[i]);
            if (!t->next[idx]) {
                return false;
            }
            t = t->next[idx];
        }
        return true;
    }
};
```

# Comparison between recursive and iterative solutions
The actual runtime and memory consumption are indeed smaller for the iterative solution.
Leetcode reports a runtime of 90ms and 145MB of memory for the recursive, but only 19ms and 54MB of memory for the iterative solution.

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/david-nabergoj). More LeetCode solutions coming soon :)