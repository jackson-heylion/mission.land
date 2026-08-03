# Mission 8 continuation — an exact quantitative Christoffel defect

- **Author:** `jackson-heylion`
- **Date:** 2026-08-03
- **Agent:** ChatGPT
- **Model:** GPT-5.6 Thinking
- **Result:** no score-1 witness found

## Binary parity functional

For the half-accelerated Collatz map

```text
phi(n) = n/2          if n is even,
phi(n) = (3n+1)/2     if n is odd,
```

let `d` be a binary word of length `N` with `r` ones, and define

```text
C(d) = sum_{i=1}^N 2^(i-1) 3^(number of ones strictly right of i) d_i.
```

A periodic point with parity word `d` would satisfy

```text
x = C(d) / (2^N - 3^r).
```

The July 2026 preprint *Christoffel words as extremal structures in Collatz
dynamics* proves that, up to rotation, the Christoffel word uniquely maximizes
`C_min`, the minimum of `C` over cyclic rotations.

## Exact local-swap identity

Write two words differing by one adjacent transposition as

```text
d  = [u, 1, 0, v],
d' = [u, 0, 1, v].
```

Let

```text
p = length(u),
q = number of ones in v.
```

Using the concatenation identity

```text
C([a,b]) = 3^(ones(b)) C(a) + 2^(length(a)) C(b),
```

and `C(10)=1`, `C(01)=2`, we obtain the exact difference

```text
C(d') - C(d) = 2^p 3^q.
```

Thus every `10 -> 01` move strictly increases `C` by a completely explicit
integer.

## Uniform lower bound at fixed weight

The prefix `u` contains exactly `r-q-1` of the total `r` ones. Therefore

```text
p >= r-q-1.
```

Consequently

```text
C(d') - C(d)
  = 2^p 3^q
  >= 2^(r-q-1) 3^q
  = 2^(r-1) (3/2)^q
  >= 2^(r-1).
```

Hence:

> Every single adjacent `10 -> 01` transposition in a binary word with `r`
> ones increases `C` by at least `2^(r-1)`.

Combining this with the transposition path to the Christoffel word gives the
quantitative defect statement:

> If the canonical rotation of a word is not the Christoffel word, then its
> numerator lies at least `2^(r-1)` below the Christoffel numerator along the
> chosen monotone transposition chain.

For a path containing `s` swaps, the exact total defect is the sum of the
corresponding terms `2^p 3^q`, and is at least

```text
s * 2^(r-1).
```

This is stronger than uniqueness of the maximizer because it supplies an
explicit integral gap.

## Why this still does not force a hypothetical cycle to be Christoffel

After division by the cycle denominator, one swap changes the candidate cycle
minimum by at least

```text
2^(r-1) / (2^N - 3^r).
```

Near the critical slope `N/r = log_2(3) + epsilon`, the denominator is roughly

```text
2^N * (1 - 2^(-r epsilon)).
```

For candidate cycle sizes forced by the `2^68` computational lower bound,
`N-r` is on the order of tens of billions. The quotient above is therefore
astronomically small. The integral numerator gap is mathematically clean but
not large enough, by itself, to show that a cycle with minimum above `2^68`
must use the exact Christoffel word.

This falsifies the optimistic version of the rigidity hypothesis:

```text
large minimum => exact Christoffel parity word
```

It does not follow from the first discrete gap alone.

## More promising arithmetic direction

A 2026 peer-reviewed paper by Kevin Knight proves that positive integer
"high cycles" do not exist. Those high cycles are the extremal rational cycles
associated with fixed length and odd-count parameters, closely related to the
Christoffel extremizers. This means the exact extremal family is already known
to be arithmetically impossible.

The remaining gap is not the extremizer itself, but the enormous set of words
that are close to extremal. A useful next target is therefore a *stability plus
arithmetic* theorem:

1. classify words whose Christoffel defect is below a threshold;
2. express them as a bounded number of local defects or central-word surgeries;
3. use differences between selected rotations to force the odd denominator
   `2^N-3^r` to divide a pure `2`-power or `3`-power;
4. conclude impossibility because the denominator is coprime to both 2 and 3.

The key local identity suggests looking for two rotations that differ by a
single central swap. Their numerator difference can collapse to a pure power of
2 or 3, which would be incompatible with simultaneous divisibility by
`2^N-3^r` unless that denominator equals 1.

## Current status

No genuine positive non-trivial cycle and no valid score-1 JSON witness were
produced. The new result is the exact local defect formula and its uniform
`2^(r-1)` lower bound, together with the conclusion that numerical extremal
stability alone is too weak at the required cycle scale.
