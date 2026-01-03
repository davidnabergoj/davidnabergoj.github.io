+++
date = '2026-01-03T13:20:00+01:00'
draft = false
tags = ['FCPP', 'Probability']
title = 'FCPP 9: Craps'
[params]
    author = 'David Nabergoj'
+++

This is the solution to problem 9 from [*Fifty Challenging Problems in Probability* by Frederick Mosteller (1987)](https://www.amazon.com/Challenging-Problems-Probability-Solutions-Mathematics/dp/0486653552).
The problem is paraphrased below; for reference, it is inspired by the original book.
> In the gambling game of *craps*, we roll two fair six-sided dice and observe the total.
> If we roll a 7 or an 11, we win! We can also win in a more elaborate way:
> 1. Throw anything except 2, 3, or 12. Our throw is called a *point*.
> 2. Keep throwing. If we throw the point, we win! If we throw a 7, we lose. Otherwise, we just keep throwing.
> 
> What is the probability of winning a game of craps?

Let's dive in!

## Looking at some winning paths

When solving this, I first wrote down some winning sequences of totals:
* 7;
* 11;
* 4, 4;
* 5, 2, 6, 2, 8, 12, 11, 5;
* and so on.

Assuming we win, we can either do it on the first throw or with a point.

## Probability of winning on the first throw

The probability of winning on the first throw is the same as the probability of rolling a 7 or an 11 immediately.
How do we find those?
Let's draw a 6 by 6 table where each row represents the outcome of die D1, each column represents the outcome of die D2, and each cell represents their total.

| D1/D2 | **1** | **2** | **3** | **4** | **5** | **6** |
|-------|-------|-------|-------|-------|-------|-------|
| **1** | 2     | 3     | 4     | 5     | 6     | 7     |
| **2** | 3     | 4     | 5     | 6     | 7     | 8     |
| **3** | 4     | 5     | 6     | 7     | 8     | 9     |
| **4** | 5     | 6     | 7     | 8     | 9     | 10    |
| **5** | 6     | 7     | 8     | 9     | 10    | 11    |
| **6** | 7     | 8     | 9     | 10    | 11    | 12    |

Since there are 36 cells, the probability of rolling a total of \(x\) is equal to the count of \(x\) in the table, divided by 36.
In more standard notation:

\[
    X \sim 
    \begin{pmatrix}
    2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 \\
    \frac{1}{36} & \frac{2}{36} & \frac{3}{36} & \frac{4}{36} & \frac{5}{36} & \frac{6}{36} &
    \frac{5}{36} & \frac{4}{36} & \frac{3}{36} & \frac{2}{36} & \frac{1}{36}
    \end{pmatrix}.
\]

The probability of winning in the first throw is thus \(P(X = 7) + P(X = 11) = \frac{8}{36}.\)

## Probability of winning with a point
To win this way, we first roll to get a point.
Then we roll until we get the point again -- or 7.
A valid sequence can be described as \(n, m_1, m_2, \dots, m_{k}, n\), 
where \(m_i \notin \{7, n\}\) for all \(i = 1, \dots, k\).

What is the probability of such a sequence?
Obviously, it depends on \(n\).
We note that \(n\) must be part of a valid set of totals \(V = \{4,5, 6,8,9, 10\}\).
The probability of winning with a point is the sum of probabilities of winning with each \(v \in V\).
We can summarize this as follows:
\[
    P_\mathrm{point-win} = \sum_{v \in V} P(v) P(\textrm{roll many times without seeing 7, nor } v) P(v).
\]
The two \(P(v)\) terms describe the probability of rolling \(v\) initially and as the final roll of the sequence.

How do we compute the middle term? Let's call it \(P_S(v)\).
We have to roll zero or more totals, none of which are in \(\{7, v\}\).
We can write:
\[
    P_S(v) = \sum_{i=0}^\infty P(\textrm{roll neither 7, nor } v)^i.
\]
Each term in the sum is the probability of a roll sequence of length \(i\). The sum checks all sequence lengths \(i\).
We notice that \(P(\textrm{roll neither 7, nor } v)\) is always the same for a given \(v\) regardless of \(i\).
Since probabilities are between 0 and 1, the entire sum is a geometric series of the form:
\[
    \sum_{i=0}^\infty p^i = \frac{1}{1 - p}.
\]

Let's compute this individually for all values \(v\in V\).
I'll use #\(v\) to denote the number of times \(v\) appears in the totals table.
Let \(q(v) = P(\textrm{roll neither 7, nor } v) = \frac{36 - 6 - \#v}{36}\).

| \(v\) | #\(v\) | \(q(v)\)             | \(P_S(v) = \sum_{i=0}^\infty q(v)^i\) |
|---|----|---------------|--------|
| 4 | 3  | \(\frac{3}{4}\) | \(4\)      |
| 5  | 4   | \(\frac{13}{18}\) | \(\frac{18}{5}\)      |
| 6  |  5  | \(\frac{25}{36}\) | \(\frac{36}{11}\)      |
| 8  |  5  | \(\frac{25}{36}\) | \(\frac{36}{11}\)      |
| 9  |  4  | \(\frac{13}{18}\) | \(\frac{18}{5}\)      |
| 10  |  3  | \(\frac{3}{4}\) | \(4\)      |

The probabilities are the same for \(4, 10\); \(5, 9\); and \(6, 8\) due to symmetry in the totals table.
What's left is to compute \(P_\mathrm{point-win}\):

\[
    P_\mathrm{point-win} = 2 \cdot \left( \left(\frac{3}{36}\right)^2 \cdot 4 + \left(\frac{4}{36}\right)^2 \cdot \frac{18}{5} + \left(\frac{5}{36}\right)^2 \cdot \frac{36}{11}\right) = \frac{134}{495}.
\]

Add the two together and we get our final win probability:

\[
    P(\mathrm{Win}) = P(X = 7) + P(X = 11) + P_\mathrm{point-win} = \frac{8}{36} + \frac{134}{495} \approx 0.49293.
\]

We're at a slight disadvantage, but it's almost even odds!

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More FCPP solutions coming soon :)