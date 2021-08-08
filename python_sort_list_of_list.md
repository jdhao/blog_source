---
title: "How to sort a list of tuple or list in Python -- lambda or itemgetter?"
date: 2019-11-06 22:43:59+0800
tags: [sorting]
categories: [Python]
---

In Python, when we want to sort a list of tuples or lists, we may want to sort
it based on certain element in each sub-list, for example, sort the list based
on the first element in each sub-list.

<!--more-->

There are two different ways to achieve this, one is using `lambda` and the
other is using
[`itemgetter`](https://docs.python.org/3/library/operator.html#operator.itemgetter).

```
# using lambda
my_list.sort(key=lambda x:x[0])

# using itemgetter
from operator import itemgetter
my_list.sort(key=itemgetter(0))
```

According to my benchmark on a list of 1000 tuples, using `itemgetter` is
almost twice as quick as the plain `lambda` method. The following is my code:

```python
In [1]: a = list(range(1000))

In [2]: b = list(range(1000))

In [3]: import random

In [4]: random.shuffle(a)

In [5]: random.shuffle(b)

In [6]: c = list(zip(a, b))

In [7]: %timeit c.sort(key=lambda x: x[1])
81.4 µs ± 433 ns per loop (mean ± std. dev. of 7 runs, 10000 loops each)

In [8]: random.shuffle(c)

In [9]: from operator import itemgetter

In [10]: %timeit c.sort(key=itemgetter(1))
47 µs ± 202 ns per loop (mean ± std. dev. of 7 runs, 10000 loops each)
```

I have also tested the performance (run time in $µs$) of this two method for
various list size. See the table below for a comparison:

```
+-----------+--------+------------+
| List size | lambda | itemgetter |
+-----------+--------+------------+
| 100       | 8.19   | 5.09       |
+-----------+--------+------------+
| 1000      | 81.4   | 47         |
+-----------+--------+------------+
| 10000     | 855    | 498        |
+-----------+--------+------------+
| 100000    | 14600  | 10100      |
+-----------+--------+------------+
| 1000000   | 172000 | 131000     |
+-----------+--------+------------+
```

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/Xnip2019-11-06_23-56-44.jpg" width="600">
</p>

(The code producing the above image can be found [here](https://gist.github.com/jdhao/5569afa7efc13abf75a5baf18e7c29d6))

From the above image, we can see that `itemgetter` is consistently faster than
`lambda` regardless of the list size. Combined with its conciseness to select
multiple elements from a list, `itemgetter` is clearly the winner to use in
sort method.

Note: this post is based on [my answer on StackOverflow](https://stackoverflow.com/a/53828002/6064933).
