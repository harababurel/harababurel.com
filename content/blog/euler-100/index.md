+++
title = "Project Euler #100: Arranged probability"
date = 2021-09-24

[extra]
katexjs = true
+++

> If a box contains twenty-one coloured discs, composed of fifteen blue discs and six red discs, and two discs were taken at random, it can be seen that the probability of taking two blue discs, $P(BB) = \frac {15} {21} \times \frac {14} {20} = \frac 1 2$.
>
> The next such arrangement, for which there is exactly 50% chance of taking two blue discs at random, is a box containing eighty-five blue discs and thirty-five red discs.
>
> By finding the first arrangement to contain over $10^{12} = 1,000,000,000,000$ discs in total, determine the number of blue discs that the box would contain.

This is a fun problem that seems out of reach initially but can be reduced to something more simple.

## Compute the probability

Assuming there are $b$ blue discs and $r$ red discs, the probability of choosing one blue disc at random is $P_1 = \frac {b} {b+r}$. The probability of choosing another blue disc from the remaining set is $P_2 = \frac {b-1} {b+r-1}$.

So the probability of choosing 2 blue discs is $P(BB) = P_1 \cdot P_2 = \frac {b} {b+r} \cdot \frac {b-1} {b+r-1}$

We need to find the smallest solution of:

$$
\begin{cases}
   \frac {b} {b+r} \cdot \frac {b-1} {b+r-1} = \frac 1 2 \newline
   b + r > 10^{12}
\end{cases}
$$

If we consider $b$ to be a constant, the first equation can be rewritten to express $r$ as a function of $b$. In short:

$$ r^2  + (2b-1) \cdot r + (b-b^2) = 0 $$

Which has at most two natural roots:

$$ r_{1,2} = \frac {1 - 2b \pm \sqrt {(2b-1)^2 - 4(b-b^2)} } 2 $$

We can now compute the corresponding $r$ for every possible $b$, if it exists. Assuming that $b$ and $r$ are on the same order of magnitude, this approach should find the answer after ~$10^{12}$ iterations.

## Finding patterns

The previous approach finds several solutions quickly:

|         $Total$ |        $Blue$ |         $Red$ |
|----------------:|--------------:|--------------:|
|            $21$ |          $15$ |           $6$ |
|           $120$ |          $85$ |          $35$ |
|           $697$ |         $493$ |         $204$ |
|          $4060$ |        $2871$ |        $1189$ |
|         $23661$ |       $16731$ |        $6930$ |
|        $137904$ |       $97513$ |       $40391$ |
|        $803761$ |      $568345$ |      $235416$ |
|       $4684660$ |     $3312555$ |     $1372105$ |

It turns out that $b$ follows a known sequence ([A011900](https://oeis.org/A011900)):

$ a(n) = 6\cdot a(n-1) - a(n-2) - 2 $, with $ a(0) = 1 $ and $ a(1) = 3 $

Similarly, $r$ is a triangular number ([A001109](https://oeis.org/A001109)):

$ a(n) = 6\cdot a(n-1) - a(n-2) $, with $ a(0) = 0 $ and $ a(1) = 1 $

It takes less than 20 iterations to find the first solution which exceeds $10^{12}$.

