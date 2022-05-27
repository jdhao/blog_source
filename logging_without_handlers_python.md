---
title: "What Happened When Logging without Handlers in Python"
date: 2022-05-27T23:27:12+08:00
markup: markdown
tags: [logging]
categories: [Python]
---

I saw a post from stackoverflow asking why his logger does not work as expect?
The code is like:

<!--more-->

```python
import logging


logger = logging.getLogger()

logger.info("some message")
```

There is no message coming out. Okay, let's check the level of this logger:

```python
print(logger.level)

# output is 30 (logging.WARNING)
```

Then we can just change its level to `DEBUG`, it will certain work, right?

```python
logger.setLevel(logging.DEBUG)
```

However, still no output! W*F, what happened?

According to [official doc](https://docs.python.org/3/howto/logging.html#what-happens-if-no-configuration-is-provided),
if you do not set an explicit handler for the logger, a special handler called `lastResort` will be used.
See the [code here](https://github.com/python/cpython/blob/5e34b494a08015e9b5a3deade23943bdba284a93/Lib/logging/__init__.py#L1727-L1733). By default the logging level of `lastResort` (it is stream handler) is 30.
We can change its level to output info message.

```python
# setting both the logger and handler's level will work as expected.
logger.setLevel(logging.DEBUG)
logging.lastResort.setLevel(logging.DEBUG)
```

However, this is like a hack and never an encouraged solution.

# Using logging.basicConfig()

If we want to do logging real quick, we can use the method [`logging.basicConfig`](https://docs.python.org/3/library/logging.html#logging.basicConfig).


```python
logging.basicConfig(level=logging.DEBUG)
```

This will create the logger. The `logger.level` will be the level we set here.
Without a level param, the default level for root logger is `WARNING`.

A stream handler will be also created, with level `NOTSET`.
This can be verified by printing the logger's handlers:

```python
print(logger.handlers)
# you will see: [<StreamHandler <stderr> (NOTSET)>]
```

# reference

+ logger logging without handler: https://stackoverflow.com/q/64570918/6064933
