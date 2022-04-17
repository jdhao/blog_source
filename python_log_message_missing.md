---
title: "Why Are Some Logging Messages Missing in Python?"
date: 2021-04-01T23:10:56+08:00
draft: false
tags: [logging]
categories: [Python]
---

In my project, I use the [Python logging package](https://docs.python.org/3/library/logging.html) for logging service.

<!--more-->

My setup looks like this:

```python
import logging

logger = logging.getLogger('demo project')
formatter = logging.Formatter(
    "[%(asctime)s][%(name)s][%(levelname)s] %(message)s", "%Y-%m-%d %H:%M:%S"
)

stream_handler = logging.StreamHandler()
stream_handler.setFormatter(formatter)
stream_handler.setLevel(logging.INFO)
# stream_handler.setLevel(logging.DEBUG)
logger.addHandler(stream_handler)

logger.setLevel(logging.INFO)

logger.info("info message")
```

# Missing log message

This works well when I show info messages. Later, I want to show some debug messages via my logger.
So I set the logger's logging level to `DEBUG`, and use `logger.debug()` to print the debug message:

```python
logger.setLevel(logging.DEBUG)

logger.debug("some debug info")
```

Unfortunately, this does not work as intended. When I run my code, no debugging message is shown. Why?

# Separate logging levels: logger vs handler

It has something to do how the logging messages are actually logged.
Remember that in our first code snippet, we set the logging level both for the logger and the handler (in this case, a stream handler).

The levels we use for logger and the handler together decide which message can be logged and which should be rejected.
The logger's level is like the first gate/filter, and the handler's level is like the second filter.

When a logging message comes, it first passes the logger, and then the handler.
If the logging message's level is no less than the logger's level, it then goes to the handler.
If its level is also no less than handler's level, the message gets logged.

Take the snippet in the second code snippet as an example,
the logger's level is `DEBUG`, and the handler's level is `INFO`.
When we want to log some debug messages, it has the same level as logger's level.
However, its level is below the handler's level (DEBUG vs INFO), so it gets rejected by the handler
(i.e., the handler will not log it, we see nothing on the terminal output).
On the other hand, if we use a high level for logger, for example `ERROR`,
and use a low level for handler, e.g., `DEBUG`, only error loggings will be logged,
since all other logging messages will be rejected by the logger.

The Python doc also has a [nice diagram](https://docs.python.org/3/howto/logging.html#logging-flow) showing the workflow of logging processing,
which is much more detailed.

# The usefulness of separate logging levels

What is the benefit of separate logging level of logger and handler?
With this, we can achieve flexible logging plans.
For example, we may want to log all messages on the stream handler,
but only want to log error messages on the file handler to save storage space.

```python
stream_handler = logging.StreamHandler()
stream_handler.setFormatter(formatter)
stream_handler.setLevel(logging.DEBUG)
logger.addHandler(stream_handler)

file_handler = logging.FileHandler('demo-project.log')
file_handler.setFormatter(formatter)
file_handler.setLevel(logging.ERROR)
logger.addHandler(file_handler)

logger.setLevel(logging.DEBUG)

logger.debug("debug message")
logger.info("info message")
logger.warning("warning message")
logger.error("error message")
```

If we run the above code, we can see that all logging messages are shown on the terminal,
but only the error message is logged in `demo-project.log`.

# References

+ [What is the point of setLevel in a python logging handler?](https://stackoverflow.com/q/17668633/6064933)
+ [Log level of file handler vs. that of logger](https://stackoverflow.com/q/57813067/6064933)
