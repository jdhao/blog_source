---
title: "Tqdm Issues and Tips"
date: 2020-09-05T00:23:28+08:00
draft: false
tags: []
categories: [Python]
---

[Tqdm](https://github.com/tqdm/tqdm) is a popular package to show a progress
bar. Here are a few tips when using tqdm.

<!--more-->

# Windows display issue

On Windows, it seems that tqdm will print a new line when showing the progress,
which is annoying.

According to issue [here](https://github.com/tqdm/tqdm/issues/454), this is due
to buggy Unicode handling in Windows terminal. We can add `ascii=True` in
`tqdm()` to prevent this behavior. Works for me on Windows for both Cmder and
plain cmd.

# Iterate over each line in a file and show progress?

When we are processing files with large number of lines, we may want to see the
progress. To see the progress, we need to know the number of lines in this
file. Here is a sample code to show the progress bar using tqdm:

```python
from tqdm import tqdm

file_path = "test.txt"
num_lines = sum(1 for line in open(file_path, 'r'))

with open(file_path, 'r') as f:
    for line in tqdm(f, total=num_lines):
        # deal with this line...
        pass
```

Ref

+ [python enumerate tqdm bar when reading a file?](https://stackoverflow.com/q/48437189/6064933)

# Show progress for concurrent.futures.ThreadPoolExecutor

Here is an example of using tqdm together with concurrent.futures:

```python
from concurrent.futures import ThreadPoolExecutor
import time

from tqdm import tqdm


def square(x):
    time.sleep(0.01)
    return x*x


def main():
    nums = list(range(10000))
    with ThreadPoolExecutor() as executor:
        results = list(tqdm(executor.map(square, nums), total=len(nums)))

if __name__ == "__main__":
    main()
```

The output of `executor.map(square, nums)` is a generator containing all the
returned results from the function `square()`. So we need to supply the total
number in the `total` argument.

In the latest version of tqdm, we can also simplify the above code by using
`tqdm.contrib.concurrent.thread_map()` method, which is essentially a wrapper
around above code:

```python
from concurrent.futures import ThreadPoolExecutor
import time

from tqdm.contrib.concurrent import thread_map


def square(x):
    time.sleep(0.01)
    return x*x


def main():
    nums = list(range(10000))
    with ThreadPoolExecutor() as executor:
        results = thread_map(square, nums)

if __name__ == "__main__":
    main()
```

The above code is tested on tqdm version 4.48.2.

Ref:

+ [Use tqdm with concurrent.futures?](https://stackoverflow.com/a/52242947/6064933)
+ Another way: https://gist.github.com/alexeygrigorev/79c97c1e9dd854562df9bbeea76fc5de
+ [doc on tqdm.contrib.concurrent](https://tqdm.github.io/docs/contrib.concurrent/#tqdmcontribconcurrent).
