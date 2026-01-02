+++
date = '2026-01-02T13:21:00+01:00'
draft = false
tags = ['FCPP', 'Probability', 'Geometry']
title = 'FCPP 6: Chuck-a-Luck'
[params]
    author = 'David Nabergoj'
+++

This is the solution to problem 6 from [*Fifty Challenging Problems in Probability* by Frederick Mosteller (1987)](https://www.amazon.com/Challenging-Problems-Probability-Solutions-Mathematics/dp/0486653552).
The problem is paraphrased below; for reference, it is inspired by the original book.
> We're playing a gambling game called Chuck-a-Luck at a carneval. We bet either 1, 2, 3, 4, 5, or 6 dollars. Three dice are rolled. If the bet appears on one, two, or three dice, we receive one, two, or three times our bet plus our money back. Otherwise, we lose our bet.
> 
> What is our expected loss per stake?

Let's dive in!

## Solution outline

I'm an optimist at heart so I'll reframe the question to finding the expected *reward* per stake :)
The possible events are that we roll 0, 1, 2, or 3 winning dice. 
We can describe them with a discrete random variable \(X \in \{0, 1, 2, 3\}\).
The corresponding rewards \(R_B\) are \(-B, B, 2B\), or \(3B\) dollars.
The expected reward is then simply
\[
    \mathbb{E}[R_B] = -BP(X=0)+BP(X=1)+2BP(X=2)+3BP(X=3).
\]

The expected reward relies on our bet \(B\). Let's assume we'll later pick the bet that maximizes this reward.

## The probability of rolling winning dice

We (reasonably) assume the three dice are rolled independently.
The probability of any individual die roll matching \(B\) is equal to \(1/6\) (we also reasonably assume the dice are fair). This holds regardless of the value of \(B\).

We can use a [Bernoulli](https://en.wikipedia.org/wiki/Bernoulli_distribution) random variable with parameter \(1/6\) to describe whether a die matches \(B\).
Since we're interested how many dice match \(B\), we effectively want to find the sum of three Bernoulli random variables.
This is the previously stated random variable \(X\), which has a [binomial](https://en.wikipedia.org/wiki/Binomial_distribution) distribution:
\[
    X \sim \mathrm{Binomial}(3, 1/6).   
\]

Now we can easily compute the probability terms in our expected reward:
* \(P(X = 0) = \binom{3}{0} (1/6)^0 (5/6)^3 = 125/216\).
* \(P(X = 1) = \binom{3}{1} (1/6)^1 (5/6)^2 = 75/216\).
* \(P(X = 2) = \binom{3}{2} (1/6)^2 (5/6)^1 = 15/216\).
* \(P(X = 3) = \binom{3}{3} (1/6)^3 (5/6)^0 = 1/216\).

## Final steps

All we have to do is plug in the computed probabilities into our expected reward formula:
\[
    \mathbb{E}[R_B] = -B(125/216)+B(75/216)+2B(15/216)+3B(1/216),
\]
which simplifies to \(\mathbb{E}[R_B] = -17B/216\).
Therefore, betting \(B\) dollars will lead us to lose \(17B/216\) dollars on average.
We'll lose the least if we only bet a single dollar.


Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More FCPP solutions coming soon :)