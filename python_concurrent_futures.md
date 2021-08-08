---
title: "Concurrent.futures in Python"
date: 2020-12-29T23:32:08+08:00
draft: false
tags: []
categories: [Python]
---

My notes on using `concurrent.futures` in Python.

# executor.map() VS executor.submit()

There are mainly two different ways to use executor for parallel processing,
the first is via [`executor.map()`](https://docs.python.org/3/library/concurrent.futures.html#concurrent.futures.Executor.map),
and the second way is via [`executor.submit()`](https://docs.python.org/3/library/concurrent.futures.html#concurrent.futures.Executor.submit)
combined with [`concurrent.futures.as_completed()`](https://docs.python.org/3/library/concurrent.futures.html#concurrent.futures.as_completed).

Here is a simple example to demonstrate this:

```python
import time
from contextlib import contextmanager
from concurrent.futures import ThreadPoolExecutor
import concurrent.futures


@contextmanager
def report_time(des):
    start = time.time()
    yield
    end = time.time()

    print(f"Time for {des}: {end-start}")

def square(x):
    time.sleep(0.5)
    return x*x


def main():
    num = 10

    with report_time("using executor.map"):
        # with ThreadPoolExecutor(max_workers=10) as executor:
        with ThreadPoolExecutor() as executor:
            res = executor.map(square, range(num))
        res = list(res)

    with report_time('using executor.submit'):
        with ThreadPoolExecutor() as executor:
            my_futures = [executor.submit(square, x) for x in range(num)]
            res = []
            for future in concurrent.futures.as_completed(my_futures):
                res.append(future.result())
            print(res)


if __name__ == "__main__":
    main()
```

Note that `executor.map()` will return an iterator instead of plain list, and
the order of results corresponds to the order that arguments are provided for
the function we want to run in parallel.

If we use `executor.submit()`, it will return a [future object](https://docs.python.org/3/library/concurrent.futures.html#future-objects).
We can later access the function return value via the future object. Unlike
`map()`, we then use `concurrent.futures.as_completed(my_futures)` to make sure
that the functions are actually executed and return the results. Any future
that gets finished first will be returned first. So <font color="red">there is
no guarantee of the result order anymore! </font> For example, we may get the
following result for `res`:

```
[4, 9, 1, 16, 25, 49, 36, 0, 64, 81]
```


# max_worker

For `ThreadPoolExecutor()`, there is parameter `max_worker` to
specify the max number of threads to use. According to the [official doc](https://docs.python.org/3/library/concurrent.futures.html#concurrent.futures.ThreadPoolExecutor),
it is set to `min(32,  os.cpu_count()  +  4)` for Python 3.8 and `os.cpu_count() * 5`
for Python version below 3.8 and above 3.5.

In some cases, the default `max_worker` may be too large to cause serious
issues. For example, when I use ThreadPoolExecutor() to request a web service
with default parameters, my code runs for a few requests, then it hangs
indefinitely without any progress. I have to reduce `max_worker` to about 50 to
run the code smoothly. So in our real projects, we should tweak the value of
`max_workers` to fit our needs.

# Ref

+ [How does ThreadPoolExecutor().map differ from ThreadPoolExecutor().submit?](https://stackoverflow.com/q/20838162/6064933)
+ [Number of max_workers when using ThreadPoolExecutor from concurrent.futures?](https://stackoverflow.com/q/47498288/6064933)
+ [How does concurrent.futures.as_completed work?](https://stackoverflow.com/q/51239251/6064933)
+ https://alexwlchan.net/2019/10/adventures-with-concurrent-futures/
