---
title: "Notes on Using Python Regex Package"
date: 2020-11-09 22:16:22+0800
tags: [Regex]
categories: [Python]
---

Some notes on using regular expressions in Python.

<!--more-->

# Escape special characters

Some ASCII characters have special meaning in regex. If you want to match them
literally in your pattern, you need to escape them. For example, if we want to
match `(abc)` **literally**, we need to write it as `\(abc\)`.  Doing this
manually is tedious and error-prone.

A better way is using `re.escape()` instead of doing it manually.

Ref

+ [Match literal parentheses in regex](https://stackoverflow.com/q/23805835/6064933).

# The meaning of re.ASCII

`re.ASCII` only affects what characters are in a character class. It doesn't
restrict the searched strings in any way.

Ref:

+ [Python regex: pattern with re.ASCII can still match unicode characters?](https://stackoverflow.com/q/61200505/6064933)

# Regex search is slow?

If we have a lot of regex patterns, it is better to compile them using
`re.compile()`. It will boost performance significantly.

Ref:

+ [Is it worth using Python's re.compile?](https://stackoverflow.com/q/452104/6064933)

# Using compiled regex in `re.search()` is slow.

I accidentally use compiled regex pattern in `re.search()` and find that it is
slower than normal string patterns. To verify, see the below code:

```python
In [1]: test = 'sdf dfads dfads fsdfdas jkjlajdfsa adf'

In [2]: p = 'sdf'

In [3]: import re

In [4]: %timeit re.search(p, test)
1.17 µs ± 2.49 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)

In [5]: cp = re.compile(p)

In [6]: %timeit cp.search(test)
381 ns ± 0.315 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)

In [7]: %timeit re.search(cp, test)
1.75 µs ± 7.24 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)
```

In the above code, in terms of execution speed, `cp.search(test)` (using
compiled regex) is the fastest, `re.search(p, test)` (using the normal string)
is the second, and `re.search(cp, test)` (using the compiled regex pattern in
`re.search`) turns out to be the slowest.

In fact, when the pattern `p` is more complex, the time gap between
`re.search(p, test)` and `re.search(cp, test)` is even larger.

This has something to do with how regex search is implemented in Python re
package. If you use `re.search(p, some_str)`, re package will try to compile
the `p` string under the hood and cache it in an internal dict using its hash
values if this pattern hasn't been stored yet. If the pattern has been stored
in the cache, re will use the compiled version.

The slowness for compiled pattern using `re.search()` is mainly caused by the
calculating the hash key. Every time you call the re package with this compiled
pattern, it will calculate the hash key for this pattern, consuming a lot of
time. There is a more detailed discussion [here](https://stackoverflow.com/q/47477347/6064933).

# References

+ Show regex match visually: https://regex101.com/
+ [Is it worth using Python's re.compile?](https://stackoverflow.com/q/452104/6064933)
