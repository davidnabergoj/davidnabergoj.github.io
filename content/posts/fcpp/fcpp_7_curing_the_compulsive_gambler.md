+++
date = '2026-01-03T12:20:00+01:00'
draft = false
tags = ['FCPP', 'Probability']
title = 'FCPP 7: Curing the Compulsive Gambler'
[params]
    author = 'David Nabergoj'
+++

This is the solution to problem 7 from [*Fifty Challenging Problems in Probability* by Frederick Mosteller (1987)](https://www.amazon.com/Challenging-Problems-Probability-Solutions-Mathematics/dp/0486653552).
The problem is paraphrased below; for reference, it is inspired by the original book.
> Mr. Brown is playing roulette. He always bets one dollar on 13. If the 38-number wheel lands on it, then he wins $35 and the stake back. His friend bets him $20 that he will be behind after 36 plays.
> 
> What is Mr. Brown's expected net change after 36 plays?

Let's dive in!

## Expected net change without the friend's bet

Let \(\delta\) be the net change after one play.
The probability of winning $35 is \(1/38\), while the probability of losing $1 is \(37/38\).
Therefore, \(P(\delta = -1) = 37/38\) and \(P(\delta = 35) = 1/38\).
The expected net change after one play is thus \(\mathbb{E}[\delta] = -1 \cdot 37/38 + 35 \cdot 1/38 = -2/38\).
After 36 plays, this totals \(\mathbb{E}[\Delta] = 36 \cdot \mathbb{E}[\delta] = -2/38 \cdot 36 \approx -1.895\).

## Expected net change including the friend's bet

What's the probability of being net-negative after 36 plays, ignoring his friend's bet?
If Mr. Brown loses every time, he stands at -36 dollars. If he wins once, he's at \(-35 + 35 = 0\). If he wins twice, he's at \(-34 + 2\cdot 35 =56\). If he wins more often, he's always net-positive. So the probability of being net-negative is the same as the probability of losing every time: \(P_{\mathrm{net-neg}} = P(\delta = -1)^{36} \approx 0.383\).
This is the same as the probability of losing $20 in the bet.

The expected reward from this bet \(B\) is thus \(\mathbb{E}[B] \approx -20 \cdot 0.383 + 20 \cdot 0.617 = 4.68\).
If we add this to the expected net change from the base roullete game, we get the total expected change: \(\mathbb{E}[\Delta] + \mathbb{E}[B] \approx -1.895 + 4.68 = 2.785\).

Without the friend's bet, Mr. Brown would be losing money. Now, he's gaining money!

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More FCPP solutions coming soon :)