+++
date = '2025-12-30T21:23:00+01:00'
draft = false
tags = ['FCPP', 'Probability', 'Combinatorics', 'Simulation']
title = 'FCPP 2: Successive wins'
[params]
    author = 'David Nabergoj'
+++

This is the solution for problem 2 from [*Fifty Challenging Problems in Probability* by Frederick Mosteller (1987)](https://www.amazon.com/Challenging-Problems-Probability-Solutions-Mathematics/dp/0486653552).
The problem is paraphrased below; for reference, it is inspired by the original book.
> Ellis is a tennis player who has to compete against two players: his father and the tennis club champion. The champion is a better player than the father. Ellis will play a three-set series with alternating opponents. He can either play his father first, then the champion, then his father again; or the champion first, then his father, then the champion again. If he wins two sets in a row, he wins a special prize.
> 
> Which of the two series should he pick to maximize his probability of winning the prize?

Let's dive in!

## Defining events

Let's denote a series outcome with wins and losses:
* \(WWW\) means three wins in a row.
* \(LWW\) means a loss, then two wins in a row.
* \(WWL\) means two wins in a row, then a loss.

These are the only outcomes where Ellis can win the prize.

In this problem, I'll assume that sets are independent. For example, if Ellis wins or loses the first set, it does not affect the probability of winning or losing the second, or third set.
There's a probability of winning each individual set. 
Let \(p\) denote the probability of Ellis winning a set against his father and \(q\) the probability of him winning a set against the club champion.
Since the father is not as good as the champion, it's more likely that Ellis will win against him. Therefore \(p > q\).

## Computing prize probability for each series

The probability of winning the prize is \(P^* = P(WWW) + P(LWW) + P(WWL)\).

Let's consider the first series: the father-champion-father matchup.
In this case, we have:

* \(P(WWW) = pqp = p^2 q\).
* \(P(LWW) = (1-p)qp = p q (1-p)\).
* \(P(WWL) = pq(1-p) = p q (1-p)\).

Let's sum these into the probability of winning the prize if taking the father-champion-father series:
\[
    P^*_{\mathrm{CFC}} = p^2q + 2 pq(1-p) = pq(2-p).
\]

Let's do the same for the second series: the champion-father-champion matchup.
We have:

* \(P(WWW) = qpq = p q^2 \).
* \(P(LWW) = (1-q)pq = p q (1-q)\).
* \(P(WWL) = qp(1-q) = p q (1-q)\).

The corresponding prize probability is:
\[
    P^*_{\mathrm{FCF}} = pq^2 + 2 pq(1-q) = pq(2-q).
\]

## Comparing the two probabilities and answering the question

The final task is to check which of the two probabilities is bigger.
Let's try \(P^*_{\mathrm{FCF}} < P^*_{\mathrm{CFC}}\):

\[
    \begin{aligned}
        P^*_{\mathrm{FCF}} &< P^*_{\mathrm{CFC}} \\
        pq(2-p) &< pq(2-q) \\
        2-p &< 2-q \\
        -p &< -q \\
        p &> q
    \end{aligned}
\]

We know that the probability of beating the father is greater than the probability of beating the champion.
So this is indeed true!
That means \(P^*_{\mathrm{FCF}} < P^*_{\mathrm{CFC}}\). In other words, it is better for Ellis to choose the champion-father-champion matchup than the other one.
It may seem counterintuitive to play the champion twice, but the crucial game is the middle one -- here against the father, who is an easier opponent.

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More FCPP solutions coming soon :)