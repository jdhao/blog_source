---
title: "A Simple Guide on How to Use the Python Logging Module"
date: 2018-10-12 22:13:00 +0800
tags: [logging]
categories: [Python]
---

In the past, I use plain `print()` to display some statistics during training
process of my models. However, it is not convenient to save the statistics in a
file for later inspection. Once the program is finished, we do not have an
overview of the training process. I am aware that there are packages such as
[tensorboardX](https://github.com/lanpa/tensorboardX) and [visdom](https://github.com/facebookresearch/visdom), which are specifically designed for
inspecting the various statistics during training. Right now, I would rather
keep the program plain simple and usable. So I decided to give [Python logging
pacakge](https://docs.python.org/3/library/logging.html) a try.

<!--more-->

If we want to display messages on the terminal and save to a file, we can use
the following setting:

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    datefmt="%Y-%m-%d %H:%M:%S",
    format="%(asctime)s %(message)s",
    handlers=[
        logging.FileHandler("{}/{}".format(log_path, log_name)),
        logging.StreamHandler()
    ]
)
```

The `datefmt` parameter is used to format the time string in the logging
message. With the above format, the time is shown as something like the
following:

> 2018-10-15 09:44:49 (...other message...)

The format string uses the standard Python time format. You can find the
full list of format directives [here](https://docs.python.org/3/library/time.html#time.strftime).

The `format` paramter is used to set the format of the output message, i.e.,
what to show in the logging message. We can show time, current file name,
process name etc. A list of predefined attributes are (excerpted from the
logging documentation):

<details>
<summary><font color="red">Click to see the doc.</font></summary>

```
%(name)s            Name of the logger (logging channel)
%(levelno)s         Numeric logging level for the message (DEBUG, INFO,
                    WARNING, ERROR, CRITICAL)
%(levelname)s       Text logging level for the message ("DEBUG", "INFO",
                    "WARNING", "ERROR", "CRITICAL")
%(pathname)s        Full pathname of the source file where the logging
                    call was issued (if available)
%(filename)s        Filename portion of pathname
%(module)s          Module (name portion of filename)
%(lineno)d          Source line number where the logging call was issued
                    (if available)
%(funcName)s        Function name
%(created)f         Time when the LogRecord was created (time.time()
                    return value)
%(asctime)s         Textual time when the LogRecord was created
%(msecs)d           Millisecond portion of the creation time
%(relativeCreated)d Time in milliseconds when the LogRecord was created,
                    relative to the time the logging module was loaded
                    (typically at application startup time)
%(thread)d          Thread ID (if available)
%(threadName)s      Thread name (if available)
%(process)d         Process ID (if available)
%(message)s         The result of record.getMessage(), computed just as
                    the record is emitted
```
</details>

The `handler` parameter is used to set up where logging messages should go. In
the above example, we use a [`FileHandler`](https://docs.python.org/3/library/logging.handlers.html#filehandler)
to save the logging message to a disk file. We use [`StreamHandler`](https://docs.python.org/3/library/logging.handlers.html#streamhandler)
to display messages in the terminal.

After configuring logging, we need a logger:

```python
logger = logging.getLogger(__name__)
```

In our program, if we need to log something, use [`logger.info()`](https://docs.python.org/3/library/logging.html#logging.info).
An example usage is shown below:

```python
logger.info("Epoch %d, Accuracy: %f, Loss: %f", epoch, accuracy, loss)
```

The logging module relies on the old Python string formatting style. If you use
the new [`.format()`](https://pyformat.info/) style string formatting. You will
see a pylint warning complaining:

> pylint: logging-not-lazy / Specify string format arguments as logging function parameters

The above formatting is also slightly different from the old Python formatting
style. We do not need to write `%` between strings and the values. We can just
append a list of needed values as the argument for the `info` method.

# How to log exceptions

After catching an exception, we may want to log the exception message using our
logger. Initially, I have the following code:

```python
try:
    # my code
except SomeError as e:
    logger.debug("Exception: %s", e)
```

Unfortunately, this will only print an incomplete excpetion message and does
not help to debug the error.

Using `str(e)` or `repr(e)` to represent the exception, you also won't get the
actual stack trace, so it is not helpful to find where the exception is.

After reading other answers and the logging package doc, the following two ways
work great to print the actual stack trace for later debugging:

1. use `logger.debug()` with parameter `exc_info`:

    ```python
    try:
        # my code
    except SomeError as e:
        logger.debug(e, exc_info=True)
    ```

2. use `logger.exception()` to print the exception directly:

    ```python
    try:
        # my code
    except SomeError as e:
        logger.exception(e)
    ```

# References

+ [Log to a file and stdio at the same line](https://stackoverflow.com/a/46098711/6064933).
+ [Fix pylint logging-not-lazy warning](https://stackoverflow.com/q/29147442/6064933).
+ [Show exception message using logging package](https://stackoverflow.com/q/4690600/6064933).
