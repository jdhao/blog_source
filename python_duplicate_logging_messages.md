---
title: "Duplicate Logging Messages in Python"
date: 2020-06-20 00:45:12+0800
tags: [logging]
categories: [Python]
---

When I am working on a project, I find that the same message is printed twice.
The following minimal code can reproduce this issue:

<!--more-->

Source code for `logger.py`:

```python
import sys
import logging


class MyLogger:
    def __init__(self, name):
        self.logger = logging.getLogger(name)
        self.logger.setLevel(logging.DEBUG)

        stream_handler = logging.StreamHandler(sys.stderr)
        formatter = logging.Formatter(
            "[%(asctime)s] [%(name)s] [%(levelname)s] %(message)s", "%Y-%m-%d %H:%M:%S"
        )
        stream_handler.setFormatter(formatter)

        stream_handler.setLevel(logging.DEBUG)
        self.logger.addHandler(stream_handler)
        # self.logger.propagate = False

    def info(self, message):
        self.logger.info("{}".format(message))

my_logger = MyLogger("Logging debug")
```

Source code for `main.py`:

```python
import logging

from logger import my_logger

root_logger = logging.getLogger()
stream_handler = logging.StreamHandler()
root_logger.addHandler(stream_handler)

my_logger.info("demo")
my_logger.info("another test")
```

When we run `main.py`, we can see that each log message is printed twice on the
console:

```
[2020-06-19 21:56:53] [Logging debug] [INFO] demo
demo
[2020-06-19 21:56:53] [Logging debug] [INFO] another test
another test
```

But why? The reason is that, when we use `logging.getLogger()` without any
argument, we will get [the root logger](https://docs.python.org/3/library/logging.html#logging.getLogger).
`my_logger` in `logger.py` is thus a child logger of this root logger. By
default, child logger messages will be propagated to the logger in the upper
hierarchy.

We have two options: (1) Disable propagation from child logger (2) Do not use
root logger.

Option 1: In the `logger.py`, uncomment the line `self.logger.propagate =
False` to make sure that child logger does not propagate its message to the
root logger.

Option 2: Simply do not use root logger in `main.py`. For example, we can get
another logger by using `another_logger = logging.getLogger('main')`. In this
way, logging message from `my_logger` have no relationship with
`another_logger`. So the logging messages are printed only once.

# References

+ [Duplicate log output when using Python logging module](https://stackoverflow.com/q/7173033/6064933).
+ [Advanced logging tutorial](https://docs.python.org/3/howto/logging.html#advanced-logging-tutorial).
