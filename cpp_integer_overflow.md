---
title: "Integer Literal Type and Overflow In C++"
date: 2021-09-03T23:18:35+08:00
draft: false
tags: [C++]
categories: [Note]
---

I want to print the largest number that `unsigned int` can represent, which is
`2^32 - 1`. I use the following code:

<!--more-->

```cpp
unsigned int n = 2147483647 + 1 + 2147483647;

std::cout << n << std::endl;
```

However, I get an overflow warning from `clangd`:

> overflow in expression; result is -2147483648 with type 'int'

According to [post here](https://stackoverflow.com/a/61624886/6064933), the
expression `2147483647 + 1` overflows because both number are treated as `int`
type. The compiler will choose from `int`, `long`, `long long` in that order to
find if a type can fit the number. The maximum positive integer an `int` type
can represent is `2147483647`. So the type chosen for `2147483647` is `int`,
not `long` or `long long`. Thus we get the overflow warning when we add 1 to
it. If you use `2147483648` directly, the compiler will choose `long` type for
this number, which will also not overflow.

The variable type on the left side of the assignment does not impact the choose
integer type for the variable on the right side. It only decides whether type
conversion will happen when assigning the result to this variable. In this
case, although we want to the assign the final result to an `unsigned int` type,
`2147483647` is not converted to `unsigned int` automatically.

To fix issue, we can explicitly tell the compiler that we want an `unsigned
int` instead of `int`:

```cpp
unsigned int n = (unsigned int)2147483647 + 1 + 2147483647;
// or use the following
unsigned int n = 2147483647u + 1 + 2147483647;
```

By converting one number to unsigned int, the other number will also be
elevated to unsigned int and no overflow occurs in this case.


# References

+ C++ literal integer type: https://stackoverflow.com/q/42892227/6064933
+ Integer literal: https://en.cppreference.com/w/cpp/language/integer_literal
+ http://www.c-faq.com/expr/intoverflow1.html
