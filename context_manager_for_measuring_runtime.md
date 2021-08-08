---
title: "Measuring Code Running Time with contextmanager"
date: 2020-08-18 22:15:17+0800
tags: []
categories: [Python]
---

In Python, context manager is a great way to handle the closing of resources. It
makes sure that resources are correctly closed after their use.

<!--more-->

Basically, a context manager consists of two parts: enter and exit. You can
create a class to function as a context manager, but it is kind of verbose (see
[here](https://jeffknupp.com/blog/2016/03/07/python-with-context-managers/)).
In Python3, we can use the `contextlib` to create custom context managers
easily using the decorator. A good example is to measure the running time of a
code block. Here is the code:

```python
from contextlib import contextmanager
import time

@contextmanager
def report_time(name):
    start = time.time()
    yield
    end = time.time()
    print(f"Time needed for {name}: {end-start}")
```

In the above code, we define a context manager `report_time`, when the block it
manages starts, it will set the start time in `start`. Then it yields, which
means that the block it manages starts running. When the block finishes,
`report_time` resumes running and prints the running time of the measured code
block.

Here is an example to use this code:

```python
import requests

with report_time("httpbin request"):
    r = requests.get("https://httpbin.org/get")
```

Ref:

+ https://jeffknupp.com/blog/2016/03/07/python-with-context-managers/
+ https://www.youtube.com/watch?v=U2t2t_cpvoc
