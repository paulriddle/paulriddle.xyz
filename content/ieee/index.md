+++
title = "IEEE Floating-Point Representation"
template = "ieee.html"
date = 2020-09-15
updated = 2020-09-16
[extra]
tags = ["c", "x86-64", "javascript"]
+++

# IEEE Floating-Point Representation

First, see how floating-point numbers look like in C on a bit level.
```c
/* filename: main.c */
#include <stdio.h>

int main(void)
{
    double a = 5;
    double b = -5;
    double c = 5.523523523523;
    /* There is no formatter for printing doubles in binary form, so use lldb */
    printf("a = %g\n", a);
    printf("b = %g\n", b);
    printf("c = %g\n", c);
}
```
```fish
clang -g main.c
lldb a.out
```
```
breakpoint set -n main
run
n
n
n
frame variable -f b
(double) a = 0b0 10000000001 0100000000000000000000000000000000000000000000000000
(double) b = 0b1 10000000001 0100000000000000000000000000000000000000000000000000
(double) c = 0b0 10000000001 0110000110000001011010001100111100001101111100110100
               ^      ^                            ^
               |      |                            |
              sign   exp (k = 11 digits)       frac (n = 52 digits)
```
Now these are doubles and if we want to interpret the bits according to IEEE 754
we do as follows. The formula for the final value is this:
```
                                  (-1)^sign + M * 2^E
```
but first we must determine what kind of number it is. There are three cases:

1. **`Normalized`**. If `exp != 00000000000 && exp != 11111111111`. That is it is
   not all zeroes or ones. In this case the exponent `E` has *biased form* and
   significand `M` has *implied leading one*. Evaluated as:

        E = exp - (2^(k-1) - 1)
        M = 1 + frac

   where `exp` is interpreted as unsigned int, `frac` as unsigned binary
   fraction. These are most numbers.
2. **`Subnormal`**. If `exp = 00000000000`, all zeroes.

        E = 1 - (2^(k-1) - 1)
        M = frac

   These numbers are small.

3. **`Special`**. If `exp = 11111111111`, all ones. These numbers encode `+∞`,
   `-∞`, and `NaN`.

To be continued.

### References
---
0. [Computer Systems: A Programmer's Perspective, Chapter 2.4, Floating Point](http://csapp.cs.cmu.edu/3e)
1. https://www.youtube.com/watch?v=QghC6G8TyQ0
2. https://www.youtube.com/watch?v=T-dAJHRAym8
3. https://www.youtube.com/playlist?list=PLKK11Ligqithrgou1e6_kl9HJr1jI_LcT
4. [FLOATING POINT VISUALLY EXPLAINED](https://fabiensanglard.net/floating_point_visually_explained)
5. Relevant wiki pages:
    * [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754)
---
