+++
date = '2026-01-01T21:07:00+01:00'
draft = false
tags = ['FCPP', 'Probability', 'Geometry']
title = 'FCPP 5: Coin in Square'
[params]
    author = 'David Nabergoj'
+++

This is the solution to problem 5 from [*Fifty Challenging Problems in Probability* by Frederick Mosteller (1987)](https://www.amazon.com/Challenging-Problems-Probability-Solutions-Mathematics/dp/0486653552).
The problem is paraphrased below; for reference, it is inspired by the original book.
> We're playing a game at a carneval. There is table with horizontal and vertical lines that create a grid of squares. The side of each square is one inch long.
> We throw a penny onto the table. If the penny doesn't cross any line, we win a prize!
> The diameter of a penny is \(3/4\) inches.
> 
> Assuming our penny lands on the table, what's the probability of winning a prize?

Let's dive in!

## The geometric solution

First, we'll assume that throwing the penny onto the table is uniformly random. We don't hit any area with greater or smaller probability than any other area.
We'll also assume the lines are infinitesimally thin (though the solution is conceptually quite similar if they have some width).

When I read the question I thought of sketching some possible outcomes.
Let's see a picture.
![Two possible outcomes (no prize, prize)](/fcpp/fcpp_5/prize-no-prize.png#center)

Thinking about it, we can place a bounding box around our penny. The bounding box will intersect or touch the big square exactly when the penny intersects or touches it.
![Two possible outcomes with bounding box (no prize, prize)](/fcpp/fcpp_5/prize-no-prize-bbox.png#center)

We can move the bounding box around the big square.
To win the prize, there are only certain locations within the big square that the center of the box can occupy.
If the penny radius is \(3/4\) inches, then so is the side of our box.
Therefore, the box center will be offset by \(3/8\) inches from its side.
If we wish to avoid touching or intersecting the big square, then the box center must be \(3/8\) inches away from either side of the big square.
Let's see a picture of this valid prize area.
![Prize area](/fcpp/fcpp_5/prize-area.png#center)

What's left is to compute the prize area surface relative to the big square surface.
The big square surface is just one square inch.
The prize area starts at \(3/8\) inches and ends at \(6/8\) inches for both the \(x\) and \(y\) axes.
The prize area side length is thus \(2/8\) inches.
The prize area surface is \(2/8 \cdot 2/8 = 4/ 64 = 1/16\).
Dividing this by the big square surface simply gives \(1/16\) again.

Since we assumed to be throwing the penny uniformly onto the table, the probability of landing its center in the prize area and winning the prize is \(1/16\).

Thanks for reading! If you liked this post, you can support me on [Ko-fi ☕](https://ko-fi.com/davidnabergoj). More FCPP solutions coming soon :)