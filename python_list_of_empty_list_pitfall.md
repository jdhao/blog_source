---
title: "Creating List of Empty List in Python"
date: 2020-11-22T14:57:00+08:00
draft: false
tags: []
categories: [Python]
---

I wrote some code for my project and found that the result isn't correct. So I
spent quite some time debugging the whole working process this program and
found that it is because I have initialized a list wrongly.

<!--more-->

When we do the following in Python:

```python
x = [[]] * 3
x[0].append(1.0)
```

I expect x now becomes `[[1.0], [], []]`. Instead, it becomes `[[1.0], [1.0], [1.0]]`.
This is because when we use multiplication to create `x`, we actually
created 3 references to the empty list. List is a mutable object in Python.
When we append values to a list, we haven't changed its identity. As a result,
changing either one of the 3 sub-list will change the others since they refer
to the same list.

It is different when we create a list of same immutable objects. For example,
if we create a list of same int, and then change one of the int, other elements
will not change. It is because we can not change the value of immutable types,
assigning a new int to a list element will make it point to another address.

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
independent of each other.

# Ref

+ [List of lists changes reflected across sublists unexpectedly](https://stackoverflow.com/q/240178/6064933)
+ https://bugs.python.org/issue27135
+ https://docs.python.org/3/faq/programming.html#how-do-i-create-a-multidimensional-list
