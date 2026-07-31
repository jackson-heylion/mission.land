# Mission 8 follow-up — negative-cycle block monoid search

- **Author:** `jackson-heylion`
- **Date:** 2026-07-31
- **Result:** no positive non-trivial cycle found

## Hypothesis

Known negative Collatz cycles provide exact parity words. Perhaps concatenating
those words and adding isolated even steps could move the multiplier from the
negative-cycle side `2^N < 3^r` to the positive side `2^N > 3^r`, while
preserving the divisibility needed for an integer fixed point.

Using the halved map

```text
phi(n) = n/2       if n is even
phi(n) = (3n+1)/2  if n is odd,
```

the search alphabet was:

```text
A = 1             parity block of the -1 cycle
B = 110           parity block of the -5 cycle
C = 11110111000   parity block of the -17 cycle
Z = 0             one additional even step
```

For any concatenated binary word with total length `N`, `r` ones, and affine
numerator `Bword`, the unique periodic candidate is

```text
x = Bword / (2^N - 3^r).
```

Block concatenation was evaluated exactly. If a current prefix has parameters
`(N, r, Bword)` and a block has `(Nb, rb, Cb)`, appending it gives

```text
Bword' = 3^rb * Bword + 2^N * Cb
N'     = N + Nb
r'     = r + rb.
```

Every leaf with `2^N > 3^r` was checked for exact divisibility. Any divisible
candidate was then replayed step by step against its expanded parity word and
rejected if the orbit contained 1.

## Exhaustive run

An optimized C++ implementation used exact unsigned 128-bit arithmetic. It
enumerated all ordered token strings of length 1 through 15 whose expanded
parity-word length remained below 127 bits.

```text
token length   leaves evaluated   positive-denominator leaves
1                         4                         1
2                        16                         7
3                        64                        34
4                       256                       147
5                     1,024                       536
6                     4,096                     2,116
7                    16,384                     8,674
8                    65,536                    35,747
9                   262,144                   148,597
10                1,048,576                   596,192
11                4,194,304                 2,390,279
12               16,777,215                 9,545,496
13               67,108,746                38,476,335
14              268,432,046               155,929,739
15            1,073,639,823               632,610,384
------------------------------------------------------
total         1,431,550,234               839,744,284
```

Result:

```text
no concatenation produced a positive integer cycle avoiding 1
```

The trivial positive cycle appears immediately as `AZ`, corresponding to the
word `10` and fixed point 1; it was explicitly excluded.

## Scope

This is exhaustive only for the stated four-block language and bounds. It does
not cover arbitrary parity words and does not improve published general lower
bounds. Its purpose was to test a concrete constructive hypothesis derived from
exact negative cycles rather than to repeat a generic starting-value search.
