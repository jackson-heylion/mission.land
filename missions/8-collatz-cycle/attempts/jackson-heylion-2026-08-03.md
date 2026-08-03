# Mission 8 continuation — rotation invariance and balanced exponent words

- **Author:** `jackson-heylion`
- **Date:** 2026-08-03
- **Agent:** ChatGPT
- **Model:** GPT-5.6 Thinking
- **Result:** no score-1 witness found

## Setup

For an accelerated odd Collatz cycle, write

```text
x_{i+1} = (3 x_i + 1) / 2^{a_i},  a_i >= 1,
```

with an exponent word `a = (a_0, ..., a_{m-1})`, total

```text
A = sum_i a_i,
s_j = a_0 + ... + a_{j-1},  s_0 = 0.
```

The unique rational fixed-point candidate for that cyclic word is

```text
x_0 = N(a) / D,
D = 2^A - 3^m,
N(a) = sum_{j=0}^{m-1} 3^{m-1-j} 2^{s_j}.
```

A positive integer cycle requires `D > 0` and `D | N(a)`.

## Exact lemma: gcd obstruction is invariant under cyclic rotation

Let `rho(a)` be the left rotation

```text
rho(a) = (a_1, ..., a_{m-1}, a_0),
```

and write `N' = N(rho(a))`. The corresponding candidate is the next odd
node in the same formal orbit, so

```text
N' / D = (3 N / D + 1) / 2^{a_0}.
```

Therefore

```text
N' = (3N + D) / 2^{a_0}.
```

The denominator `D = 2^A - 3^m` is odd and coprime to 3. Dividing by a power
of two does not alter a gcd with `D`, hence

```text
gcd(N', D)
  = gcd(3N + D, D)
  = gcd(3N, D)
  = gcd(N, D).
```

Thus:

> For a fixed cyclic exponent word, `gcd(N(a), 2^A-3^m)` is identical for
> every cyclic rotation.

Consequences:

1. If one rotation has `gcd(N,D)=1` and `D>1`, no rotation can yield an integer
   cycle candidate.
2. A search never needs to test all starting positions of the same necklace.
3. Canonical-necklace enumeration is mathematically exact, not merely a
   symmetry heuristic.

## Balanced-word experiment

A hypothetical cycle with large minimum element must have

```text
A/m = log_2(3) + a very small positive error.
```

This motivates testing maximally balanced words whose entries are 1 and 2.
For each upper continued-fraction convergent `A/m` below, I formed the lower
mechanical word

```text
a_i = floor((i+1) A/m) - floor(i A/m).
```

The exact numerator and denominator were computed with arbitrary-precision
integers. Results:

```text
A/m              m       A/m - log_2(3)          gcd(N,D)
8/5               5       1.5037499279e-2          1
65/41             41      4.0335293738e-4          1
485/306           306     4.8195402817e-6          1
24727/15601       15601   1.6825358896e-9          1
125743/79335      79335   6.6642394206e-11         1
301994/190537     190537  4.8843350294e-13         1
```

By the rotation lemma, every cyclic rotation of each tested mechanical word is
also excluded.

This computation is exact, but it is only evidence about one distinguished
balanced necklace for each `(A,m)`. It does **not** exclude the exponentially
many other exponent words with the same totals.

## Next structural target

The promising question is no longer whether these particular words work, but
whether every valid exponent word sufficiently close to slope `log_2(3)` must
be balanced enough to inherit a modular obstruction.

A possible proof route is:

1. choose the least odd node of a hypothetical cycle;
2. translate least-node inequalities into bounds on every prefix discrepancy
   `s_j - j log_2(3)`;
3. show the resulting word lies in a narrow family of balanced necklaces;
4. exclude that family using modular arithmetic or a finite automaton.

No such general implication has been proved here yet.

## Current status

No genuine positive non-trivial cycle and no valid score-1 JSON witness were
produced. The new contribution is the exact rotation-gcd lemma plus reproducible
arbitrary-precision exclusions for six canonical balanced exponent words.
