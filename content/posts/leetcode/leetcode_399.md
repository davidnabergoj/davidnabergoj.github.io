+++
date = '2025-12-25'
draft = false
tags = ['BFS', 'LeetCode', 'C++', 'Graph']
title = 'LeetCode 399: Evaluate Division'
[params]
    author = 'David Nabergoj'
+++

Hi, everyone! Today, we'll be looking at [LeetCode problem 399](https://leetcode.com/problems/evaluate-division/?envType=study-plan-v2&envId=leetcode-75).
In this problem, we are given a bunch of reference equations of the form \(a_i / b_i = c_i\) for \(i = 1 , \dots, n\).
The symbols \(a_i, b_i\) are given as strings, while \(c_i\) are given as floating point numbers.
We're then asked to compute the value of a *query* equation \(q_1 / q_2\).

If \(q_1 / q_2\) is one of the reference equations, we can return that value.
Otherwise, we can follow a kind of *chain-rule* strategy.
Suppose we are given `"a" / "b"` with value `2.0` and `"b" / "c"` with value `3.0`, then we can compute `"a" / "c"` by the product:
`"a" / "c" = "a" / "b" * "b" / "c"`, which is equal to `2.0 * 3.0 = 6.0`.
If there's no way to find a solution, we return `-1`.

We'll approach this as a graph problem and use breadth-first search (BFS). Let's dive in!

## Constructing a graph of symbols
We approach this problem by first constructing a graph.
Each node represents a symbol as given in the reference equations.
If there is an edge going from node `"a"` to node `"b"`, its value is the reference value `"a" / "b"`.

Let's consider an example where the reference equations are `[["a", "b"], ["b", "c"]]` and the reference values are `[2.0, 3.0]`.

![Symbol graph example](/leetcode_399/example_graph.png#center)

To find the value of `"a" / "c"`, we start in node `"a"`, then traverse past node `"b"` and arrive at node `"c"`.
We multiply the values of all edges along the way and obtain `6.0`.

It's possible that an edge might exist from one variable to the other, but we need to traverse the path in the opposite direction.
To handle this, we can add another edge whose value is the reciprocal of the reference equation value.
For example, if the given equation is `"a" / "b" = 2.0`, we add edges `"a" -> "b"` with value `2.0` and `"b" -> "a"` with value `1 / 2.0`.

Each symbol can be represented as a struct with incoming and outgoing connections.
Each connection is a pair containing the other symbol and the edge value.
```cpp
struct Symbol {
    vector<pair<Symbol*, double>> incoming;
    vector<pair<Symbol*, double>> outgoing;
};
```

Let's look at how to construct the graph.
We store symbol objects in an unordered map, accessed by string keys.
As we traverse the equations, we add new symbols to the map if they don't yet exist.
At every step, we also update the incoming and outgoing edges of each symbol with the appropriate equation value.
```cpp
vector<double> calcEquation(vector<vector<string>>& equations, vector<double>& values, vector<vector<string>>& queries) {
    unordered_map<string, Symbol*> symbols;
    for (size_t i = 0; i < equations.size(); ++i) {
        vector<string> eq = equations[i];
        double value = values[i];

        string s1 = eq[0];
        string s2 = eq[1];

        if (!symbols.contains(s1)) {
            symbols[s1] = new Symbol;
        }
        if (!symbols.contains(s2)) {
            symbols[s2] = new Symbol;
        }

        symbols[s1]->outgoing.push_back({symbols[s2], value});
        symbols[s2]->incoming.push_back({symbols[s1], 1 / value});
    }

    ...  // we'll implement the rest later
}
```

## Evaluating an equation with BFS

We'll evaluate the equation by traversing the graph.
However, cycles in the graph might cause us to loop indefinitely.
We can avoid this by keeping track of already visited states in a set.
We can avoid going to deep into the graph by using BFS instead of depth-first search (DFS), but a DFS solution is possible as well.

When given a query \(q_1 / q_2\), we will construct a queue that contains with \(q_1\) and the corresponding initial path product `1.0`.
While the queue is not empty, we will pop its front element and add all its connected nodes to the back of the queue.
If the popped element corresponds to \(q_2\), we will return the corresponding path product.

Let's look at the code.
We first initialize the set of visited states and a queue containing the symbols and equation values.
We then execute the queue loop as outlined above.

```cpp
double evaluate(Symbol *src, Symbol *dst) {
    set<Symbol*> visited;
    queue<pair<Symbol*, double>> q;
    q.push({src, 1.0});

    while (!q.empty()) {
        pair<Symbol*, double> p = q.front();
        q.pop();

        // If we have not yet visited this symbol
        if (visited.find(p.first) == visited.end()) {
            // Have we arrived at the destination?
            if (p.first == dst) {
                return p.second;
            }

            // Add connecting symbols into ther queue
            for (auto pNext: p.first->outgoing) {
                q.push({pNext.first, pNext.second * p.second});
            }
            for (auto pNext: p.first->incoming) {
                q.push({pNext.first, pNext.second * p.second});
            }

            // Mark this symbol as visited
            visited.insert(p.first);
        }
    }
    
    // If no solution was found in the loop, return -1
    return -1;
}
```

## Full solution and complexity
Below is the full solution code.
The only practical addition is constructing an output vector and processing each query equation one-by-one.

```cpp
class Solution {
public:
    struct Symbol {
        vector<pair<Symbol*, double>> incoming;
        vector<pair<Symbol*, double>> outgoing;
    };

    double evaluate(Symbol *src, Symbol *dst) {
        set<Symbol*> visited;
        queue<pair<Symbol*, double>> q;
        q.push({src, 1.0});

        while (!q.empty()) {
            pair<Symbol*, double> p = q.front();
            q.pop();
            if (visited.find(p.first) == visited.end()) {
                if (p.first == dst) {
                    return p.second;
                }
                for (auto pNext: p.first->outgoing) {
                    q.push({pNext.first, pNext.second * p.second});
                }
                for (auto pNext: p.first->incoming) {
                    q.push({pNext.first, pNext.second * p.second});
                }
                visited.insert(p.first);
            }
        }

        return -1;
    }

    vector<double> calcEquation(vector<vector<string>>& equations, vector<double>& values, vector<vector<string>>& queries) {
        unordered_map<string, Symbol*> symbols;
        for (size_t i = 0; i < equations.size(); ++i) {
            vector<string> eq = equations[i];
            double value = values[i];

            string s1 = eq[0];
            string s2 = eq[1];

            if (!symbols.contains(s1)) {
                symbols[s1] = new Symbol;
            }
            if (!symbols.contains(s2)) {
                symbols[s2] = new Symbol;
            }

            symbols[s1]->outgoing.push_back({symbols[s2], value});
            symbols[s2]->incoming.push_back({symbols[s1], 1 / value});
        }

        vector<double> out;
        for (size_t i = 0; i < queries.size(); ++i) {
            vector<string> eq = queries[i];
            string s1 = eq[0];
            string s2 = eq[1];

            if (!symbols.contains(s1) || !symbols.contains(s2)) {
                out.push_back(-1.0);
            } else {
                out.push_back(evaluate(symbols[s1], symbols[s2]));
            }
        }
        return out;
    }
};
```

Suppose there are \(m\) symbols in the \(n\) referenced equations.
To evaluate a new query with BFS, we have to traverse at most \(m\) symbols.
We can check whether a symbol is in symbol set or not in \(O(1)\) time, as the unordered set is implemented as a hash table with constant-time lookup.
Given \(k\) queries, the worst-case time complexity is thus \(O(km)\).

We also need to store \(m\) symbols, at most \(n\) outgoing edges, and at most \(n\) incoming edges.
Within each evaluation call, the constructed queue contains a variable amount of elements, potentially more than \(m\) depending on the input graph. However, never more than \(m^2\).
The practical space complexity is \(O(m+n)\) to hold the graph and \(O(m)\) for the queue, although it can be dominated by \(O(m^2)\) for complicated input graphs.
Let's say \(O(n+m)\) for practical cases.

LeetCode reports that this algorithm runs in 0ms, beating 100% of solution in runtime.
It takes 11.75 MB memory, beating 80.54% in space utilization.
That's quite good!

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More LeetCode solutions coming soon :)