+++
date = '2025-12-30'
draft = false
math = true
tags = ['FCPP', 'Probability', 'Combinatorics', 'Simulation']
title = 'FCPP 1: The Sock Drawer'
[params]
    author = 'David Nabergoj'
+++

This is the first post in a series solving problems from [*Fifty Challenging Problems in Probability* by Frederick Mosteller (1987)](https://www.amazon.com/Challenging-Problems-Probability-Solutions-Mathematics/dp/0486653552).
The problem is paraphrased below; for reference, it is inspired by the original book.
> A drawer contains some red and black socks. Two socks are drawn at random, and the probability that both are red is  \(1/2\).
> 1. What is the minimum total number of socks?
> 2. What is the minimum if the number of black socks is even?

Let's dive in!

## Setting up the basic equation

We want to express the probability of drawing two red socks.
Let \(r\) denote the number of red socks and let \(b\) denote the number of black socks.
We consider two events:
* \(R_1\), the event that the first sock we draw is red.
* \(R_2\), the event that the second sock we draw is red.

The probability that they are both red can be expressed as \(p = P(R_1) P(R_2 | R_1)\).
In other words, the probability that we draw red first multiplied by the probability that we draw red second *under the condition* that we already drew red first.

We can compute the first as \(P(R_1) = r / (r + b)\), since we wish to draw one of \(r\) red socks out of a drawer containing \(r + b\) total socks.
We can compute the second as \(P(R_2 | R_1) = (r - 1) / (r + b - 1)\). That's because we are picking between \(r-1\) remaining red socks (remember: we already drew one) among \(r + b - 1\) remaining total socks.

Let's summarize the findings:
\[
    \frac{r}{r+b} \cdot \frac{r-1}{r+b-1} = \frac{1}{2}.
\]

## The key inequality

We can observe the following:
\[
    \frac{r}{r + b} > \frac{r - 1}{r + b - 1}.
\]

This is the most crucial observation to solving our problem.
Let's verify it:

$$
\begin{aligned}
\frac{r}{r+b} &> \frac{r-1}{r+b-1} \\
r(r+b-1) &> (r+b)(r-1) \\
r^2 + rb - r &> r^2 - r + rb - b \\
0 &> -b \\
b &> 0.
\end{aligned}
$$

The final statement is true, because we're solving a problem with a positive number of black (and red) socks.

## Implications of the inequality

Several things naturally follow from our established inequality.
First, we can say:
\[
    \left(\frac{r}{r+b}\right)^2 > \frac{1}{2} \quad \textrm{and also} \quad \left(\frac{r-1}{r+b-1}\right)^2 < \frac{1}{2}.
\]
Let's put the two statements together:
\[
    \left(\frac{r}{r+b}\right)^2 > \frac{1}{2} > \left(\frac{r-1}{r+b-1}\right)^2.
\]

Solving this is cumbersome due to the squares. Let's take the square root instead (since all terms are positive):
\[
    \frac{r}{r+b} > \frac{1}{\sqrt{2}} > \frac{r-1}{r+b-1}.
\]

## Bounding \(r\) with \(b\)
We now have two inequalities, which we can potentially rearrange to bound the number of one type of socks with the other.
Let's try to bound \(r\) with \(b\).

We can rearrange the first equality for the first bound:
\[
    \begin{aligned}
        \frac{r}{r+b} &> \frac{1}{\sqrt{2}} \\
        r &> \frac{r + b}{\sqrt{2}} \\
        r &> \frac{r}{\sqrt{2}} + \frac{b}{\sqrt{2}} \\
        r - \frac{r}{\sqrt{2}} &> \frac{b}{\sqrt{2}} \\
        \frac{r\sqrt{2} - r}{\sqrt{2}} &> \frac{b}{\sqrt{2}} \\
        r\sqrt{2} - r &> b \\
        r(\sqrt{2} - 1) &> b \\
        r &> \frac{b}{\sqrt{2} - 1}. \\
    \end{aligned}
\]

We can also rearrange the second inqeuality for the second bound:
\[
    \begin{aligned}
        \frac{1}{\sqrt{2}} &> \frac{r-1}{r+b-1} \\
        \frac{r-1}{r+b-1} &< \frac{1}{\sqrt{2}} \\
        r-1 &< \frac{r+b-1}{\sqrt{2}} \\
        r-1 &< \frac{r}{\sqrt{2}} + \frac{b-1}{\sqrt{2}} \\
        r-\frac{r}{\sqrt{2}} &< 1 + \frac{b-1}{\sqrt{2}} \\
        r-\frac{r}{\sqrt{2}} &< \frac{b + \sqrt{2} - 1}{\sqrt{2}} \\
        \frac{r(\sqrt{2} - 1)}{\sqrt{2}} &< \frac{b + \sqrt{2} - 1}{\sqrt{2}} \\
        r(\sqrt{2} - 1) &< b + \sqrt{2} - 1 \\
        r &< \frac{b + \sqrt{2} - 1}{\sqrt{2} - 1} \\
        r &< \frac{(b + (\sqrt{2} -1))(\sqrt{2} + 1)}{(\sqrt{2} - 1)(\sqrt{2} + 1)} \\
        r &< \frac{b (\sqrt{2} + 1) + 1}{1} \\
        r - 1 &< b (\sqrt{2} + 1). \\
        r &< b (\sqrt{2} + 1) + 1. \\
    \end{aligned}
\]

Now we can finally express full the bound as:

\[
    b (\sqrt{2} + 1) < r < b (\sqrt{2} + 1) + 1.
\]

## Finding suitable values of \(r\) and \(b\)

Let's try a few values of \(b\) and keep in mind that \(\sqrt{2} + 1 \approx 2.41\).

If \(b\) = 1, then \(r \in [2.41, 3.41]\). The only possible value is \(r = 3\).
Let's plug both values into the probability equation:

\[
    \frac{r}{r+b} \frac{r-1}{r+b-1} = \frac{3}{4} \cdot \frac{2}{3} = \frac{1}{2}.
\]

So \(r = 3, b = 1\) is a solution! In fact, \(b\) can't possibly be smaller, so this solution answers question (a): the minimum number of socks is \( 3 + 1 = 4\).

Let's try \(b = 2\). 
Now \(r \in [4.83, 5.83]\), so \(r = 5\).
If we plug them into the probability formula, we get:
\[
    \frac{r}{r+b} \frac{r-1}{r+b-1} = \frac{5}{7} \cdot \frac{4}{6} = \frac{10}{21}.
\]

While there is a real-valued solution for \(r\) in that interval, there is no integer-valued solution.
Only some values of \(b\) give an integer-valued \(r\).
Let's check them programatically to speed up our search.
I've written a small Python script to do so:

```python
import random
import math

c = math.sqrt(2) + 1
for b in range(1, 10000):
    min_r = int(b * c)
    max_r = int(math.ceil(b * c + 1))
    for r in range(min_r, max_r):
        prob = r / (r + b) * (r - 1) / (r + b - 1)
        eps = 1e-8
        if 0.5 - eps < prob < 0.5 + eps:
            print(f'r: {r}, b: {b} -> {prob}')
```

This script prints values of \(r\) and \(b\) when the probability is equal to 0.5 (allowing for some numerical imprecision).
This will give us suitable candidates that we can check by hand.
Running the script gives the following output:

```
r: 3, b: 1 -> 0.5
r: 15, b: 6 -> 0.5
r: 85, b: 35 -> 0.5
r: 493, b: 204 -> 0.5
r: 2871, b: 1189 -> 0.5000000000000001
r: 16731, b: 6930 -> 0.5
```

Our first solution shows up. The second solution is \(r = 15, b = 6\), which is the first with an even number of black socks. We can easily verify it by hand.
This answers question (b): the minimum number of socks with an even number of black socks is \(15 + 6 = 21\).
The four remaining solutions are also valid and there are more possibilities if we check greater values of \(b\).

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More FCPP solutions coming soon :)