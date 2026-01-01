+++
date = '2026-01-01T20:23:00+01:00'
draft = false
tags = ['FCPP', 'Probability']
title = 'FCPP 3: The Flippant Juror'
[params]
    author = 'David Nabergoj'
+++

This is the solution to problem 3 from [*Fifty Challenging Problems in Probability* by Frederick Mosteller (1987)](https://www.amazon.com/Challenging-Problems-Probability-Solutions-Mathematics/dp/0486653552).
The problem is paraphrased below; for reference, it is inspired by the original book.
> Jury A consists of three jurors. The first two have a probability \(p\) of being correct. The third one chooses based on a fair coin toss. All three jurors vote independently.
> Jury B consists of a single juror that is correct with probability \(p\). 
> 
> Which jury is correct more often?

Let's dive in!

## Defining events and the question

Let's denote a voting outcome for jury A with correct and wrong votes:
* \(CCC\) means all jurors pick correctly.
* \(CCW\) means the first two jurors vote correctly and the flippant one votes incorrectly.
* \(CWC\) means the first and the flippant juror vote correctly, while the second juror is incorrect.
* \(WCC\) means the second and the flippant juror vote correctly, while the first juror is incorrect.

It does not matter which of the two non-flippant jurors is correct if one is correct and the other is wrong, so \(P(CWC) = P(WCC)\).

The probability of jury B being correct is trivially \(P_B = p\).
The probability of jury A being correct is \(P_A = P(CCC) + P(CCW) + 2P(CWC)\).
Let \(q\) denote the probability that the flippant juror is correct.
Since all three are independent, we have \(P_A = p^2q + p^2(1-q) + 2p(1-p)q\).

We want to find out if either \(P_A > P_B, P_A = P_B\), or \(P_A < P_B\).

## The probability that the flippant juror is correct

Upon reading this, I immediately thought of guilty and non-guilty verdicts.
For instance, perhaps the flippant juror is correct more often if a person is innocent than the other two.
However, this is irrelevant. Let's see why.

Let \(G\) and \(I\) denote the events that a person on trial is guilty and innocent, respectively.
Let \(V_G\) and \(V_I\) denote the events that the flippant juror votes guilty and innocent, respectively.
We can then say \(q = P(V_G|G)P(G) + P(V_I|I)P(I)\).

We know that \(P(G) = 1 - P(I)\) as these are the only two possible events.
We also know that \(P(V_G|G) = P(V_I|I) = 1/2\), as the flippant juror throws a coin independent of the truth.
Therefore \(q = (1/2 \cdot (1 - P(I)) + 1/2\cdot P(I)) = 1/2\).

We can now plug this into our formula for \(P_A\).

## Final steps

We plug \(q = 1/2\) into \(P_A\) to get:
\[  
    \begin{aligned}
        P_A &= p^2/2 + p^2/2+p(1-p), \\
        P_A &= p^2 + p - p^2, \\
        P_A &= p.
    \end{aligned}
\]

It turns out that \(P_A = P_B\), so both juries are correct in the same proportion of cases.

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More FCPP solutions coming soon :)