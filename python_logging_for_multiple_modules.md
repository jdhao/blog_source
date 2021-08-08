---
title: "How to Use Unified Logging Config Across the Project"
date: 2020-04-24 01:12:43+0800
tags: [logging]
categories: [Python]
---

When we are working on a project, we often need to log some message for easier
debugging. How do we configure logging once and use it across the project?

<!--more-->

# Set up logging for the project

Suppose we have the following file in our project:

```
main.py
module1.py
module2.py
```

`main.py` is the entry point of our project and use functions or classes from
`module1` and `module2.`

The best practice is to set up logging in `main.py` like this:

```python
import module1
import module2
import logging
# other imports

logging_level = logging.DEBUG
main_logger = logging.getLogger()
main_logger.setLevel(logging_level)

# Set up a stream handler to log to the console
stream_handler = logging.StreamHandler()
stream_handler.setLevel(logging_level)
formatter = logging.Formatter("%(name)s - %(levelname)s - %(message)s")
stream_handler.setFormatter(formatter)

# Add handler to logger
main_logger.addHandler(stream_handler)

# other codes
```

In the above code, we define a root logger `main_logger` via
`logging.getLogger()` (notice that no name is provided to this method), and set
up the handlers and logging level properly.

In `module1` and `module2`, we only need to define a logger like this:

```python
import logging

logger = logging.getLogger(__name__)

# then use logger.info() or logger.debug() in your code.
```

In other modules, if we define the logger using `logging.getLogger(__name__)`,
it will automatically use the settings from the root logger. This ensures that
we only need to configure logging once in our project.

## Changing logging level to disable some logging message ##

There is also another benefit in using a unified config: we can easily control
the logging message verbosity by changing the logging level. For example, in
the above example, we set logging level in `main.py` to `logging.DEBUG`, so all
`logger.debug()` and `logger.info()` message will be printed to the console. If
we do not want to see `logger.debug("xxx")` messages anymore, we can set
logging level to `logging.INFO`, which will disable any debug message across
the whole project.

# Disable logging message from other packages

When we import 3rd party packages in our own project and set proper logging
level for the project, if a logging message in 3rd party packages is above that
level, the message will also be printed. Since we usually only care about
logging messages in our project, we can disable logging message in 3rd party
packages by setting their logger level higher or disable logging entirely for
3rd party packages. Here is the code:

```python
import logging

# suppose that the logger level of our module is logging.DEBUG, we set
# logger level of other module higher than logging.DEBUG
for m in ("urllib3", "foo", "bar"):
    logging.getLogger(m).setLevel(logging.CRITICAL)
    # To disable it entirely, use the following.
    # logging.getLogger(m).disabled = True
```

# References

+ https://stackoverflow.com/a/15735146/6064933
+ https://stackoverflow.com/q/47608069/6064933
+ [Disable logger message from other packages](https://stackoverflow.com/q/27538879/6064933).
