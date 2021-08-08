---
title: Remainder Operator(%) in C++ 
date: 2017-10-07 21:11:12 +0800
tags: [C++]
categories: [Programming]
---

What is value of `a` and `b` in the following statement according to the rules of C++ 11?

```cpp
int a = -5 % 2;
int b = 5 % -2;
```

If you can not clearly tell the result, then you should update your knowledge
about % operator in C++.  <!--more-->

# The rules for remainder operator

Some people call `%` *modulo operator*, but its real name is *remainder
operator*. Rules about % operator differs between C++ 11 and pre-C++ 11 era. In
pre-C++ 11 era, the result of `a%b` when `a` or `b` is negative is undefined.
But in C++ 11, the result is well-defined, the standard says (emphasis added by
me):

> The binary / operator yields the quotient, and the binary % operator yields
> the remainder from the division of the first expression by the second. If the
> second operand of / or % is zero the behavior is undefined.
> **For integral operands the / operator yields the algebraic quotient with any
> fractional part discarded; if the quotient a/b is representable in the type
> of the result, (a/b)\*b + a%b is equal to a**.

It basically says that `a == (a/b)*b + (a%b)` and the result of `a/b` is
truncated toward zero. With these two rules, the result of `a%b` can be
calculated without any ambiguity:

```cpp
a%b = a - (a/b)*b
```

# A real example

Take the calculation of `a` and `b` in the first paragraph as an example,

```
-5/2 ==> -2
-5%2 = -5 - (-2*2) = -1

5/-2 = -2
5%-2 = 5 - (-2*-2) = 1
```

So, in C++ 11, the value for `a` and `b` is -1 and 1 respectively.

# References

+ [Remainder operator in C++, post 1](https://stackoverflow.com/questions/13100711/operator-modulo-change-in-c-11).
+ [Remainder operator in C++, post 2](https://stackoverflow.com/questions/11720656/modulo-operation-with-negative-numbers).
+ [Remainder operator in C++, post 3](https://stackoverflow.com/questions/7594508/modulo-operator-with-negative-values).
