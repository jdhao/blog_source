---
title: "How to Pass List by Value as Parameter in Python"
date: 2022-07-27T01:35:30+08:00
markup: markdown
tags: []
categories: [Python]
---

If we pass list as parameter to a function and change the parameter, the original list is also changed.
This is because list is a mutable type, when we pass list to a function, we are passing the same list.

<!--more-->

```python
def my_fun(my_list):
    my_list.append(1)

    return my_list


x = [1, 2, 3]
y = my_fun(x)

print(f"x: {x}, y: {y}")
# x, y are both [1, 2, 3, 1]
```

How can we pass the "value" of this list instead of its "reference"?
We can use several ways:

+ slicing: `my_fun(x[:])`
+ list(): `my_fun(list(x))`
+ `list.copy()`: `my_fun(x.copy())`
+ `copy.copy()`: `my_fun(copy.copy(x))`
+ `copy.deepcopy()`: `my_fun(copy.deepcopy(x))`

The first four ways only create a shallow copy of the original list.
They only work for simple list consisting of immutable types, for example, a list of int.
If the list element is a mutable type themselves, they will not work.

Only the [`copy.deepcopy()`](https://docs.python.org/3/library/copy.html#copy.deepcopy) method can truly create a new list.

# references

+ https://docs.python.org/3/faq/programming.html#why-did-changing-list-y-also-change-list-x
+ clone a list so that it doesn't change after assignment: https://stackoverflow.com/q/2612802/6064933
+ pass list as argument: https://stackoverflow.com/q/2322068/6064933
+ pass list to function by value: https://stackoverflow.com/q/15377050/6064933
