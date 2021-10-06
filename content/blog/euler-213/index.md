+++
title = "Project Euler #213: Flea Circus"
date = 2021-10-06

[extra]
katexjs = true
+++

> A $30 \times 30$ grid of squares contains 900 fleas, initially one flea per square.
> When a bell is rung, each flea jumps to an adjacent square at random (usually 4 possibilities, except for fleas on the edge of the grid or at the corners).
>
> What is the expected number of unoccupied squares after 50 rings of the bell? Give your answer rounded to six decimal places.

The problem statement looks deceivingly simple but also doesn't give away any approach towards finding a solution. The entire process can be simulated using the [Monte Carlo method](https://en.wikipedia.org/wiki/Monte_Carlo_method):

- place one flea in each cell of an $ n \times n $ grid ($ n = 30 $)
- simulate $ k = 50 $ bell rings
  - during each bell ring, move all $ 900 $ fleas to a randomly selected neighboring cell
- count the number of empty cells at the end
- repeat the whole process as many times as possible and average the results

This can give a reasonable approximation of the final result but is in no way feasible considering the required precision. A straight-forward implementation can simulate one experiment in  $ O(n^2 \cdot k) $, which is already a non-trivial complexity. My C++ implementation needs almost a minute to simulate the experiment $ 10^5 $ times. The precision is not great: at most one significant figure, and only some of the time. Increasing the iteration count to $ 10^6 $ does not necessarily perform better. There is still a high variance between runs.

But at least we have a better idea of what the answer looks like.

## Expected value

The expected number of unoccupied cells is, simply put, the average that would be obtained by repeating the simulation infinitely many times. Mathematically, it is a weighted arithmetic mean of the possible outcomes (i.e. number of unoccupied cells) and the probabilities of obtaining them.

If we denote the discrete random variable $ X $ to represent the number of unoccupied cells after 50 bell rings, then:

$$ E(X) = \sum x_i p_i $$

where the values $ x_i $ are the possible outcomes $ X $.

In practice, we know what these outcomes are. The two extremes are $ x_\{min} = 1 $ (all fleas end up in exactly the same cell) and $ x_\{max} = 900 $ (all fleas end up in distinct cells, exactly like the beginning). Every value in between is also plausible.

## Probabilities

Now we need a way to compute any given probability $ p_i $ of ending up with exactly $ i $ unoccupied cells. We can draw some intuitive conclusions right away: it's highly likely that $ p $ follows a bell curve. Moreover, it might be more likely for border and corner cells to be empty. They have fewer neighbors where fleas can enter from, and border fleas can only stay for so long before the randomness pulls them closer to the center. I have no proof for either of these claims but it might be fun to plot some graphs and verify them.

In any case, the probability is tricky to compute. Since this looks a lot like a counting problem, maybe a dynamic programming approach can work. I tried to come up with a useful definition, going through several iterations depending on the information that was needed:

* $ dp(i,j,k) = $ the probability of having cell $(i, j)$ occupied after $k$ bells.
* Initial state: $ dp(i,j,0) = 1 $. Every cell is occupied in the beginning.
* Recurrence: hard to tell. Since all fleas which are in cell $ (i, j) $ after $ k-1 $ bells will jump away, none of them will be there anymore after $ k $ bells. Instead, all fleas from the (up to) 4 neighboring cells have a chance of entering cell $ (i,j) $.
* Problems: this $ dp $ table doesn't tell us anything about the number of fleas. A neighbor cell with 20 fleas has more chances of having some of them jump to the current cell than a neighbor with only 3 fleas. We don't have enough information to compute an exact probability.

Maybe introduce another dimension:

* $ dp(i,j,x,k) = $ the probability of having exactly $ x $ fleas in cell $(i, j)$ after $k$ bells.
* Initial state: $ dp(i, j, 1, 0) = 1 $. Every cell has exactly one flea in the beginning.
* Recurrence: calculable but computationally expensive.

Assuming we want to compute the current state $ dp(i, j, x, k) $, what previous states could we have come from?

We know the fleas came from (up to) 4 neighbors. Those neighbors could have had any number of fleas themselves after $ k-1 $ bells, ranging from $0$ all the way to $900$. So the possible previous states are:

$$ dp(i_t, j_t, x_t, k-1), 1 \le t \le 4; 0 \le x_t \le 900 $$


Moreover, the $x$ fleas we have obtained in cell $(i,j)$ could have come in any combination from these neighbors. Essentially, we must try all configurations of $ x = \sum x_t'$, where $ 0 \le x_t' \le x_t $.

With a bit more gymnastics we can compute the probability of having exactly $x_t'$ out of $x_t$ fleas jump in exactly the direction they need to in order to reach the current cell. The whole recurrence starts to take form but ends up looking extremely unwieldy. I will not attempt to write it here in full (or at all really).

## Perspective shift

This unlocked the problem for me. What if, instead of tracking cells and the fleas contained within, we track the journey of individual fleas? This proves to be useful, despite adding a new dimension to the $dp$ table:

* $ dp(i, j, x, y, k) = $ the probability of the flea who started in cell $(i, j)$ to reach cell $(x,y)$ after exactly $k$ bells.
* Initial state: $dp(i,j,i,j,0)=1$. Every flea has reached its own cell in the beginning.
* Recurrence: the flea must have come from one of the (up to) 4 neighboring cells. Each neighbor $(x_t, y_t)$ has a probability of containing this particular flea after the previous bell (basically $ dp(i, j, x_t, y_t, k-1) $). In turn, the neighbor can have $ b_t $ neighbors of its own ($ 2 \le b_t \le 4 $). All of them are equally likely to be chosen as the jump destination but only one of them is our current cell. So we have a clear relation:

$$ dp(i, j, x, y, k) = \sum_{t=1}^4 \frac 1 b_t \cdot dp(i, j, x_t, y_t, k-1) $$

Of course, only valid neighbors are considered.

## Putting it all together

Now we know for every flea how likely it is to end up in any cell of the grid after 50 bell rings. How is this useful?

We can treat cells individually. A cell $(x, y)$ will be unoccupied at the end only if _all_ fleas land somewhere else. If we denote this probability as $p(x, y)$, then:

$$ p(x,y) = \prod \(1-dp(i, j, x, y, k)\) $$

As it turns out, this probability shows exactly by how much cell $(x, y)$ contributes to the expected number ofunoccupied cells. The final answer is:

$$ E(X) = \sum p(x, y) $$

Complexity: $O(n^4 \cdot k)$. Execution time less than 2 seconds.
