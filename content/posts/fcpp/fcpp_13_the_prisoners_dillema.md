+++
date = '2026-01-04T19:02:00+01:00'
draft = false
tags = ['FCPP', 'Probability']
title = "FCPP 13: The Prisoner's Dillema"
[params]
    author = 'David Nabergoj'
+++

This is the solution to problem 13 from [*Fifty Challenging Problems in Probability* by Frederick Mosteller (1987)](https://www.amazon.com/Challenging-Problems-Probability-Solutions-Mathematics/dp/0486653552).
The problem is paraphrased below; for reference, it is inspired by the original book.
> Prisoners \(A\), \(B\), and \(C\) with equally good records have applied for parole. Only two prisoners will be granted parole, but the prisoners themselves don't know who.
> Prisoner \(A\) has a warder friend, who can answer this question.
> Instead of being direct, prisoner \(A\) contemplates asking: "Name one prisoner except me who will be granted parole."
> He thinks that his chances of release before asking the question are \(\frac{2}{3}\).
> He incorrectly deduces that after asking, his chances go down to \(\frac{1}{2}\).
> 
> How is he incorrect and what is the correct probability of releasing \(A\) after asking the warder?

Prisoner \(A\) thinks that asking will reduce his chances. Let's see what happens if he does ask.
The challenge in this problem is setting up the sample space correctly.
Let's dive in!

## Describing the board's decision
Exactly two prisoners will be granted parole by the parole board. 
We denote the events that "\(A\) and \(B\) are granted parole", "\(A\) and \(C\) are granted parole", and "\(B\) and \(C\) are granted parole" as \(AB, AC\), and \(BC\) to grant parole to \(AB, AC\), and \(BC\), respectively.
Since all records are equally good, each pair is equally likely:
\[
    P(AB) = P(BC) = P(AC) = \frac{1}{3}.
\]

Before asking the warder, the probability of \(A\) being granted parole is \(P(A) = P(AB) + P(AC) = \frac{2}{3}.\)

## Describing the warder's behavior

Prisoner \(A\) says: "Name one prisoner except me who will be granted parole."
The warder's response must be consistent with the board's decision.
The warder will respond with either \(B\) or \(C\). Let's name these events \(W_B\) and \(W_C\).

What's the probability of the warder giving each answer?
* If the board decides on granting parole to \(AB\), he will say \(W_B\) with probability \(1\).
* If the board decides on granting parole to \(AC\), he will say \(W_C\) with probability \(1\).
* If the board decides on granting parole to \(BC\), he picks either \(W_B\) or \(W_C\) with equal probability.

What are the corresponding unconditional probabilities?
* \(P(W_B) = P(AB) + \frac{1}{2}P(BC) = \frac{1}{3} + \frac{1}{6} = \frac{1}{2}.\)
* \(P(W_C) = P(AC) + \frac{1}{2}P(BC) = \frac{1}{3} + \frac{1}{6} = \frac{1}{2}.\)

## Computing the conditional probabilities

What is the probability that \(A\) is granted parole given the warder's answer?
Suppose he answers \(W_B\). Then:
* \(AB\) must have occurred, or
* \(BC\) must have occurred and the warder chose \(B\).

Since the warder always says \(B\) when \(AB\) occurs we have \(P(AB, W_B) = P(AB) = \frac{1}{3}\) and:
\[
    P(A | W_B) = P(AB | W_B) = \frac{P(AB, W_B)}{P(W_B)} = \frac{\frac{1}{3}}{\frac{1}{2}} = \frac{2}{3}.
\]

By symmetry, we also have \(P(A | W_C) = \frac{2}{3}.\)

## Answering the question

No matter what the warder says, the probability of granting parole to \(A\) remains \(\frac{2}{3}.\)
Asking the warder this indirect question provides no new information about prisoner \(A\)'s fate.
The warder's answer always names someone other than \(A\), and thus never rules out a scenario in which \(A\) is released.
Like the [Monty Hall](https://en.wikipedia.org/wiki/Monty_Hall_problem) host, the warder only reveals information he is forced to reveal, and such constrained information does not reduce \(A\)'s probability of success.

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More FCPP solutions coming soon :)