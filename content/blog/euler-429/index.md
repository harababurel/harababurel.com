+++
title = "Project Euler #429: Sum of squares of unitary divisors"
date = 2021-09-23

[extra]
katexjs = true
+++

> A unitary divisor $d$ of a number $n$ is a divisor of $n$ that has the property $gcd(d, \frac n d) = 1$.
>
> The unitary divisors of $4! = 24$ are $1, 3, 8$ and $24$.
>
> The sum of their squares is $12 + 32 + 82 + 242 = 650$.
>
> Let $S(n)$ represent the sum of the squares of the unitary divisors of $n$. Thus $S(4!)=650$.
>
> Find $S(100 000 000!) \mod 1 000 000 009$.

For clarity, let's denote $n=10^8$ and $N=n!=10^8!$

### Observation #1

 $N$ is huge. It has ~$750$ million digits. Computing it is out of the question.

### Observation #2

We are looking for coprime divisors of some integer $N$. This means they are not allowed to have any prime factors in common. Moreover, their product is $N$ so together they must share exactly all factors of $N$.

If $d$ and $\frac N d$ are two such divisors, and $N$ can be factorized as $\prod p_i^{a_i}$, then every $p_i^{a_i}$ must belong to either $d$ or $\frac N d$. It can not be subdivided as that would imply $gcd(d, \frac N d)$ is at least $p_i$.

$N$ contains every prime factor in the interval $[2, n]$. There are $\pi(n) = 5761455$ such primes ([A000720](https://oeis.org/A000720)). Each of them can belong to either $d$ or $\frac n d$ so we can generate $2^{5761455}$ such pairs of divisors. Again, not feasible.

### Observation #3

The sum of $k$-th powers of [unitary divisors](https://en.wikipedia.org/wiki/Unitary_divisor) of $n$ is denoted by $\sigma^*_k(n)$. Wikipedia doesn't help much but [MathWorld](https://web.archive.org/web/20210717050404/http://mathworld.wolfram.com/UnitaryDivisorFunction.html) has a useful formula for generating this function:


<p style="text-align: center;">
$\sigma^*_k(\prod p_i^{a_i}) = \prod (1+p_i^ {k \cdot a_i}) $
</p>

Using this notation, the answer is $\sigma^*_2(N)$, which can be computed in $O(\pi(n))$ -- completely reasonable.

So we need a few things:

- All primes up to $n$.
- The factorization of $N$ using said primes. Since $N=n!$, we can compute the factorization of every integer up to $n$ and merge the results.
- The computation of $\sigma^*_2(N)$ using said factorization.

A final implementation detail: the intermediate results can become significantly large. It is important to compute them modulo $ 10^9+9 $. Since the exponents $a_i$ can get fairly large too (on the same order of magnitude as $n$), it doesn't hurt to use [fast exponentiation](https://en.wikipedia.org/wiki/Exponentiation_by_squaring) too.

This solution runs in ~1.5min on my machine: 14s generating primes, 70s performing the factorization and <1s computing $\sigma^*_2(N)$.
