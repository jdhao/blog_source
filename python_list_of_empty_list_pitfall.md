---
title: "The Correct Way to Create List of Empty List in Python"
date: 2020-11-22T14:57:00+08:00
draft: false
tags: []
categories: [Python]
---

TL;DR: do not use list multiplication to initialize an empty list of list, or
you will end up wasting hours debugging your program.

<!--more-->

I wrote some Python code for my project and found that the result isn't
correct. So I spent quite some time debugging the whole working process of this
program, and found sadly that the culprit is a list of empty list, which is
wrongly initialized.

What is the resulting list, if we do the following in Python?

```python
x = [[]] * 3
x[0].append(1.0)
```

I expect x now becomes `[[1.0], [], []]`. Instead, it becomes `[[1.0], [1.0],
[1.0]]`. This is because when we use multiplication to create list `x`, we
actually created 3 references to an empty list. List is a mutable object in
Python. When we append values to a list, we haven't changed its identity. As a
result, changing either one of the 3 sub-list will change the others, since
they all refer to the same list.

Note that things are different when we create a list of same immutable objects
using multiplication. For example, if we create a list of same int, and then
change one of the them, the other elements will not change, because we can not
change the value of immutable types, assigning a new int to a list element will
make it point to another address.

```python
In [18]: a = [1] * 2

In [19]: a
Out[19]: [1, 1]

In [20]: print(id(a[0]), id(a[1]))
4438635584 4438635584

In [21]: a[0] = 2

In [22]: print(id(a[0]), id(a[1]))
4438635616 4438635584
```

To create a list 3 empty lists, we may use list comprehension instead:

```python
x = [[] for _ in range(3)]
```

In each iteration, a new empty list is created. So the 3 sub-list are
independent of each other. Changing one won't affect the others.

# References

+ [List of lists changes reflected across sublists unexpectedly](https://stackoverflow.com/q/240178/6064933)
+ https://bugs.python.org/issue27135
+ https://docs.python.org/3/faq/programming.html#how-do-i-create-a-multidimensional-list
