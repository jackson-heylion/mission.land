# Mission 8 continuation — Christoffel extremality and the scale barrier

- **Author:** `jackson-heylion`
- **Date:** 2026-08-03
- **Agent:** ChatGPT
- **Model:** GPT-5.6 Thinking
- **Result:** no score-1 witness found

## New literature cross-check

A preprint posted on 2026-07-24 by Carlos Fernández and Santiago Ibáñez,
*Christoffel words as extremal structures in Collatz dynamics*
(arXiv:2607.24844v1), proves that for fixed halved-map period `N` and number
`r` of odd iterates, the Christoffel word uniquely maximizes (up to rotation)
the minimum numerator over a rotation class.

The paper derives the universal bound

```text
x_min <= 1 / (2^(N/r) - 3)
```

for the minimum element of a periodic orbit, where necessarily
`N/r > log_2(3)`. This rigorously supports the balanced-word direction explored
in the preceding attempt note. It is currently a version-1 preprint, so I treat
it as a promising primary source rather than as a settled peer-reviewed result.

## Exact consequence when combined with the 2^68 floor

The mission statement cites computational verification through approximately
`2^68`. Therefore a hypothetical non-trivial positive cycle must have

```text
x_min > 2^68.
```

Combining this with the Christoffel extremal bound gives

```text
2^68 < 1 / (2^(N/r) - 3),
```

hence

```text
log_2(3) < N/r < log_2(3 + 2^-68).
```

The width of this interval is

```text
log_2(3 + 2^-68) - log_2(3)
= 1.6293469766311226837... * 10^-21.
```

An exact continued-fraction / intermediate-convergent search for rationals
strictly above `log_2(3)` shows that the first admissible rational in this
interval is

```text
N/r = 114208327604 / 72057431991,
```

with excess

```text
N/r - log_2(3)
= 1.1033606933208349302... * 10^-22.
```

Consequently, under the cited `2^68` verification floor and the preprint's
extremal theorem, any non-trivial positive cycle must have at least

```text
r >= 72,057,431,991 odd iterates,
N >= 114,208,327,604 halved-map steps.
```

This corrects an earlier informal description that mentioned a roughly
45.7-billion scale. The checked denominator is about 72.06 billion.

## Why this does not solve the mission

The result is a lower-bound synthesis, not a cycle construction and not a proof
that cycles cannot exist. It confirms that direct exponent-word enumeration is
hopeless at the true candidate scale. The remaining plausible routes are:

1. strengthen the Christoffel extremal inequality enough to force `x_min < 1`
   for every admissible rational slope;
2. prove an arithmetic obstruction for all near-Christoffel words, not only the
   single extremal word;
3. combine rotation-invariant gcd obstructions with a finite-width defect
   classification around Christoffel words.

No genuine positive non-trivial cycle and no valid score-1 JSON witness were
produced.
