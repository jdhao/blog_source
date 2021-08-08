---
title: "Using Multiprocessing in Python"
date: 2020-07-27 23:58:56+0800
tags: []
categories: [Python]
---

One way to achieve parallelism is to use multi-processing, where we can
execute tasks in different cores of the CPU to reduce the total processing
time. Python provides the [multiprocessing](https://docs.python.org/3/library/multiprocessing.html) package to facilitate this.

This post summarizes some of the questions I have when I learn to use
multiprocessing in Python.

<!--more-->

# How many processes should we use?

For CPU-bound tasks, set the process number to the number of CPU cores is
perhaps appropriate. To get the number of CPUs in your system:

```python
import multiprocessing as mp

print(mp.cpu_count())
```

However, the time to complete our task will stop decreasing when the number of
processes reach a certain number due to other factors. So it is a good idea to
do some benchmark to find the optimal number of processes to use.

Ref:

+ [How many processes should I run in parallel?](https://stackoverflow.com/q/23816546/6064933)
+ [Python multiprocessing's Pool process limit](https://stackoverflow.com/q/20039659/6064933)

# Do I need to use pool.close() and pool.join() after finishing my tasks?

`pool.close()` makes sure that process pool does not accept new processes, and
`pool.join()` waits for the processes to properly finish their work and return.
So it is a good idea to use `pool.close()` and `pool.join()` explicitly.

Otherwise, the processes may not be released properly. In our application, we
have seen error related to memory usage:

> OSError: [Errno 12] Cannot allocate memory

Ref:

+ https://stackoverflow.com/q/35708371/6064933
+ Python cannot allocate memory using multiprocessing.pool: https://stackoverflow.com/q/26717120/6064933

# Difference between `pool.map()` and `pool.map_aysnc()`?

`pool.map` is blocking until it gets the actual results, that is, it will block
the execution of code until it gets all the result from the processes.
`pool.map_async`, on the other hand, is non-blocking and will return
immediately and all we get is an [`AsyncResult`](https://docs.python.org/3.6/library/multiprocessing.html#multiprocessing.pool.AsyncResult)
object. To get the actual result, we use `result.get()` to retrieve them. To
illustrate their differences:

```python
import multiprocessing as mp

def toy(x):
    print("function gets executed.")
    return x*x

def main():
    p = mp.Pool()

    result = p.map(toy, [1])
    print("after p.map")
    print(result)

    res = p.map_async(toy, [1])
    print("after p.map_async()")
    print(res.get())


if __name__ == "__main__":
    main()
```

If you use `p.map()`, you will see the following result:

```
function gets executed.
after p.map()
[1]
```

This is because `p.map()` is blocking. So the statement following it won't be
executed if `p.map()` hasn't got the results back.

Unlike `p.map`, `p.map_async` is non-blocking. The output for second part of
the code is:

```
after p.map_async()
function gets executed.
[1]
```

That is because `p.map_async` will not wait for the function to be executed and
returned. So you see the output `after p.map_async()` first. Then you see
`function gets executed.`.

+ [multiprocessing.Pool: When to use apply, apply_async or map?](https://stackoverflow.com/q/8533318/6064933)

# `pool.map()` without argument?

The function `pool.map()` is used to feed the element of an iterable to a
function one by one. We can not use it to run functions without
argument. However, we may change the function to accept an argument and ignore
that argument. Or we can write a wrap function to accept argument and
invoke the original function in the wrap function.

```python
import multiprocessing as mp

def f():
    print("function without argument")

def wrap_func(x):
    f()

p = mp.Pool()
p.map(wrap_func, range(10))
```

Ref:

+ https://stackoverflow.com/q/27689834/6064933

# functions with multiple arguments?

`pool.map()` can only execute functions that accept one argument, to run a function
that accepts multiple arguments, we can use [`pool.starmap()`](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.pool.Pool.starmap):


```python
import multiprocessing as mp


def func(x, y):
    return x * y

p = mp.Pool()

l1 = range(1, 10)
l2 = range(10, 19)

res = p.starmap(func, zip(l1, l2))
print(res)
```

If the other parameters of the function are constants, it may be convenient
to use [partial](https://docs.python.org/3.6/library/functools.html#functools.partial) functions instead:

```python
from functools import partial

l1 = range(1, 10)
# If one of the parameters is constant.
partial_func = partial(func, y=2)
res = p.map(partial_func, l1)
print(res)
```
