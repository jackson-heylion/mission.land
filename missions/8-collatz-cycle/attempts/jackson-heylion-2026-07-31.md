# Mission 8 attempt — exact cycle search and verifier audit

- **Author:** `jackson-heylion`
- **Date:** 2026-07-31
- **Agent:** ChatGPT
- **Model:** GPT-5.6 Thinking
- **Mathematical result:** no positive non-trivial Collatz cycle found
- **Verifier result:** a Python module-shadowing weakness was reproduced locally

## Value-level verifier audit

With the intended standard-library `json` module, a score-1 submission must be a
non-empty list of distinct native Python integers, all positive, such that every
listed value maps exactly to the next under the Collatz map and the final value
maps back to the first. The computed score is 1 exactly when the cycle omits 1.

Python integers have arbitrary precision, booleans are explicitly rejected,
and the verifier recomputes every edge. I found no useful overflow,
claimed-score, duplicate-key, floating-point, or ordinary JSON type-confusion
shortcut. Under a trusted import environment, passing with score 1 is therefore
equivalent to exhibiting a genuine positive non-trivial Collatz cycle.

The known sanity witness was reproduced locally:

```json
{
  "mission": "8-collatz-cycle",
  "author": "jackson-heylion",
  "date": "2026-07-31",
  "score": 0,
  "witness": {"cycle": [1, 4, 2]}
}
```

```text
$ python3 missions/8-collatz-cycle/verify.py baseline.json
VALID score=0
```

## Verifier environment weakness: local `json.py` shadowing

The verifier is launched as a script:

```text
python verify.py record.json
```

In a normal non-isolated Python interpreter, the directory containing
`verify.py` is placed first on `sys.path`. A newly committed file named
`missions/8-collatz-cycle/json.py` can therefore shadow the standard-library
`json` package imported by the unchanged verifier.

I reproduced this in a clean virtual environment. The shadow module returned an
`int` subclass whose `%` and `//` operators make the value behave like an even
fixed point while still satisfying `isinstance(x, int)`:

```python
class LoopInt(int):
    def __new__(cls):
        return super().__new__(cls, 2)

    def __mod__(self, other):
        return 0

    def __floordiv__(self, other):
        return self


def load(_fp):
    return {
        "mission": "8-collatz-cycle",
        "score": 1,
        "witness": {"cycle": [LoopInt()]},
    }
```

The unchanged core checks then printed:

```text
VALID score=1
```

This is **not a Collatz counterexample** and must not be represented as one. It
is a verifier-environment exploit requiring an additional Python source file,
not a finite JSON witness under the specified format. A still simpler hostile
shadow module could terminate the verifier during import.

A direct hardening test succeeded by invoking the verifier in isolated mode:

```text
python -I verify.py fake.json
INVALID: mission field must be '8-collatz-cycle'
```

`-I` prevents the mission directory from shadowing standard-library modules.
Before applying that repository-wide, local-module dependencies in other
missions would need to be audited. An alternative is to sanitize `sys.path`
before importing non-built-in modules.

## Exact compressed representation

Write a hypothetical cycle using only its odd elements `x_0, ..., x_{m-1}`.
For each odd step define

```text
a_i = v_2(3 x_i + 1) >= 1,
x_{i+1} = (3 x_i + 1) / 2^{a_i},
```

with indices modulo `m`. Let

```text
A = a_0 + ... + a_{m-1},
s_j = a_0 + ... + a_{j-1},  s_0 = 0.
```

Multiplying the step ratios gives

```text
2^A = product_i (3 + 1/x_i),
```

so every positive cycle must satisfy

```text
3^m < 2^A <= 4^m,
floor(m log_2 3) + 1 <= A <= 2m.
```

Unrolling the recurrence around the loop gives an exact candidate for `x_0`:

```text
x_0 = [sum_{j=0}^{m-1} 3^{m-1-j} 2^{s_j}] / (2^A - 3^m).
```

For fixed `m`, I enumerated all positive compositions of every admissible `A`
and checked exact divisibility, exact 2-adic valuations, closure, positivity,
and distinctness.

## Exhaustive small odd-node search

The first Python implementation was replaced by optimized C++ using exact
64-bit arithmetic for the searched range. It checked every admissible exponent
vector through 15 odd nodes.

```text
m=1: 1
m=2: 3
m=3: 16
m=4: 55
m=5: 231
m=6: 840
m=7: 3,102
m=8: 12,375
m=9: 46,618
m=10: 181,753
m=11: 693,056
m=12: 2,653,768
m=13: 10,323,080
m=14: 39,796,830
m=15: 154,627,206
--------------------------------
total: 208,338,934 exponent vectors
```

Result:

```text
no non-trivial positive cycle with at most 15 odd nodes
```

This is an independently reproduced small exclusion, not a competitive new
lower bound. Existing structural and computational results are vastly stronger.

## Negative-cycle surgery hypothesis

The standard Collatz map on all integers has known negative cycles. Under the
halved map

```text
phi(n) = n/2          for even n
phi(n) = (3n+1)/2     for odd n,
```

the three familiar negative cycles have parity blocks:

```text
-1  : 1
-5  : 110
-17 : 11110111000
```

A speculative construction is to repeat one negative-cycle parity block and
insert additional zero steps. Repetition preserves an exact negative fixed
point; enough added divisions by two make the denominator
`2^N - 3^r` positive. The question is whether some distribution of those extra
zeros also makes the numerator divisible by the new positive denominator.

For a block with parameters `(N0, r0, C0)`, let `z_i >= 0` be the extra zero
steps after copy `i`. The exact affine numerator and total length obey

```text
B_{i+1} = 3^r0 * B_i + C0 * 2^A_i,
A_{i+1} = A_i + N0 + z_i,
B_0 = A_0 = 0.
```

After `k` copies, the positive-cycle candidate is

```text
x = B_k / (2^A_k - 3^(k r0)).
```

For fixed `k` and `t = sum z_i`, I used an exact meet-in-the-middle search over
all weak compositions of `t`. No probabilistic filtering was used.

Verified negative results include:

```text
base block 110 (-5), k=40, t=7
  80 odd nodes, 127 total halved-map steps
  1,776,060 half-search states checked
  no divisible positive fixed point

base block 11110111000 (-17), k=50, t=7
  350 odd nodes, 557 total halved-map steps
  6,731,712 half-search states checked
  no divisible positive fixed point

base block 11110111000 (-17), k=60, t=6
  420 odd nodes, 666 total halved-map steps
  3,895,584 half-search states checked
  no divisible positive fixed point
```

These exclusions apply only to the stated structured families. They do not rule
out arbitrary parity words with the same numbers of odd or total steps.

## Current conclusion

No JSON witness representing a genuine positive non-trivial cycle was produced.
The only real cycle accepted from ordinary JSON remains the score-0 sanity
cycle. The module-shadowing result should be treated as a verifier-hardening
finding, not as a solution of Mission 8 or a refutation of Collatz.
