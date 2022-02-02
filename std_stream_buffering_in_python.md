---
title: "print message not shown in nohup.out?"
date: 2021-03-16T22:54:16+08:00
draft: false
tags: [Linux]
categories: [Python]
---

When starting a long-running Python program,
I often run it under [nohup](https://en.wikipedia.org/wiki/Nohup) so I can redirect all the output to `nohup.out` for easier inspection.
I use `nohup python test.py&` to run the process in the background.
Then I can run `tail -f nohup.out` to monitor the output message from this program.

<!--more-->

One strange issue I met is that all messages from the [`print()`](https://docs.python.org/3/library/functions.html#print) function are missing.
To reproduce this issue, run the following `test.py` with command `nohup python test.py&`:

```python
import time

n = 20
for i in range(n):
    time.sleep(0.5)
    print(f"this is loop {i}")
```

Then use `tail -f nohup.out` to check the output. Notice that there is no output for some time.
Then, all of a sudden, all the output is printed when the program exits.

However, if we directly run this program (`python test.py`), to our expectations,
the messages are indeed shown one at a time.

# Different buffering behaviors of `sys.stdout` and `sys.stderr`

This has something to do with the stream buffering behavior of Python.
`print()` function in Python will print a message to the [sys.stdout](https://docs.python.org/3.7/library/sys.html#sys.stdout) stream.
When we print directly to the console, in this case, the stdout stream is used interactively,
it will be line-buffered, i.e., the message will be shown in the terminal once a newline is met.

If we use nohup, stdout is redirected to a file (`nohup.out`), it will be block-buffered in this case,
i.e., only when the size of the output reaches a certain limit, will they be put into the destination (`nohup.out` file).
So we can not see each print message in a timely manner.
When the program exits, all the output is flushed to `nohup.out`, that is when we see those messages.

This behavior of `sys.stdout` and `sys.stderr` is documented [here](https://docs.python.org/3.7/library/sys.html?highlight=sys%20stdout#sys.stdout):

> When interactive, stdout and stderr streams are line-buffered. Otherwise,
> they are block-buffered like regular text files.

This explains why the messages is not shown timely if we redirect stdout to `nohup.out`,
since stdout is block buffered in this case.
Block-buffered means that Python stores the output message in a buffer,
and only when the buffer reaches a certain size and can not accommodate the incoming message,
the message in this buffer will be flushed and reach its destination.

If we use sufficiently large message, it will also be shown immediately since the output buffer is full.
It seems that buffer size for print is 8192[^1]. To verify this, we modify `test.py` a little bit:

```python
import time

n = 20
size = 2048
sleep_time = 1

for i in range(n):
    time.sleep(sleep_time)
    print(f"{i}" + "a" * size)
```

If we run the above script with `nohup python test.py &` and then observe the output via `tail -f nohub.out`,
we will find that it will print 4 lines at a time, i.e., lines starting with `0`, `1`, `2`, and `3` will be printed out first,
then lines starting with `4`, `5`, `6`, and `7`, etc.

For stderr, users may want to see the error messages immediately when they are produced even if it is redirected.
In Python 3.9, [the behavior of stderr is changed](https://bugs.python.org/issue13601). The [new doc](https://docs.python.org/3/library/sys.html?highlight=sys%20stdout#sys.stderr) is:

> When interactive, the stdout stream is line-buffered. Otherwise, it is
> block-buffered like regular text files. The stderr stream is line-buffered in
> both cases.

Thus, after Python 3.9, stderr will always be line-buffered,
no matter how the error message is displayed, i.e., on the terminal or redirected to a file.

# Show the message without buffering

To show the printed message immediately, we may run Python in unbuffered mode with the `-u` flag:

> -u     : force the stdout and stderr streams to be unbuffered

```bash
nohup python -u test.py
```

Another way is to add `flush=True` to `print()` function so that output will be flushed to its destination forcibly.

```python
print(f"this is loop {i}", flush=True)
```

# References

+ What does the different buffering behavior mean?
    + https://www.oreilly.com/library/view/unix-filesystems-evolution/9780471456759/chap04-sec004.html
    + [What do fully buffered, line buffered and unbuffered mean in C?](https://stackoverflow.com/q/36573074/6064933)
+ [Python - nohup.out don't show print statement](https://stackoverflow.com/q/25674613/6064933)
+ [Nohup is not writing log to output file](https://stackoverflow.com/q/12919980/6064933)
+ [Python bugs mailing list: Improve documentation of stdout/stderr buffering in Python 3.x](https://bugs.python.org/issue13597#msg149419)
+ [How to flush output of print function?](https://stackoverflow.com/q/230751/6064933)
+ [Disable output buffering](https://stackoverflow.com/q/107705/6064933)

[^1]: Related to [`io.DEFAULT_BUFFER_SIZE`](https://stackoverflow.com/a/41259881/6064933).
