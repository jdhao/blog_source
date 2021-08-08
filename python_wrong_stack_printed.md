---
title: "Why is Wrong Stacktrace Printed for My Code?"
date: 2021-03-13T21:02:47+08:00
draft: false
tags: []
categories: [Python]
---

The other day, when I was updating the source code with the project running, I
noticed that the exception stack trace printed is not right, i.e., the printed
error line is actually not the line that is triggering the exception.

<!--more-->

Why did this happen? Is it related to the modification to the source file? At
first, I thought it absurd. Based on my knowledge, when Python runs the source
code, it will load it into memory. Since it is executing the code in the memory,
so it *should* print the offending line that is kept in the memory.

To figure it out, I write the following code (`test.py`):

```python
import time

time.sleep(10)
print(1/0)
```

I run it (`python test.py`), then add something below the time statement:

```python
import time

time.sleep(10)
# some comment
print(1/0)
```

Then program reaches the print statement and printed the following exception:

```txt
Traceback (most recent call last):
  File "test.py", line 4, in <module>
    # some comment
ZeroDivisionError: division by zero
```

Surprisingly, the stack trace printed is the 4th line in the modified `test.py`,
not the original 4th line (`print(1/0)`).

So my perception of how Python runs its code is not fully correct. I searched
on the internet and found some explanation about this, for example, discussion
[here](https://stackoverflow.com/a/43594452/6064933).

When Python run a source code, it will first compile the source code into the
so called [bytecode](https://docs.python.org/3/glossary.html#term-bytecode).
Then the bytecode is executed by the [Python virtual machine](https://docs.python.org/3/glossary.html#term-virtual-machine).

So what is really running is the bytecode, not the original source code. We can
use the `dis` package in standard library to inspect the bytecode produced by
Python.

```python
import dis

my_str = "import time\n\ntime.sleep(10)\nprint(1/0)"
print(dis.dis(my_str))
```

It will show the bytecode along with other infos for the source code:

```txt
│  1           0 LOAD_CONST               0 (0)
│              2 LOAD_CONST               1 (None)
│              4 IMPORT_NAME              0 (time)
│              6 STORE_NAME               0 (time)
│
│  3           8 LOAD_NAME                0 (time)
│             10 LOAD_METHOD              1 (sleep)
│             12 LOAD_CONST               2 (10)
│             14 CALL_METHOD              1
│             16 POP_TOP
│
│  4          18 LOAD_NAME                2 (print)
│             20 LOAD_CONST               3 (1)
│             22 LOAD_CONST               0 (0)
│             24 BINARY_TRUE_DIVIDE
│             26 CALL_FUNCTION            1
│             28 POP_TOP
│             30 LOAD_CONST               1 (None)
│             32 RETURN_VALUE
```

Number in the first column corresponds to the code line in the original script.
For example, the first line (`import time`) is turned into 4 bytecode
instructions.

As a result, the original source code is not kept by Python in the memory. When
an exception happens, python will try to find the corresponding source file and
print the exact line in this source file. It does not check whether the source
file has been changed since running. Hence, the mismatch between the printed
exception line and the actual exception line.

So this is just how Python works under the hood. There are [discussion](https://bugs.python.org/issue8087) on
changing this behaviour, but it seems that the Python core developers are not
going to change it any time soon.

# References

+ [Why does error traceback show edited script instead of what actually ran?](https://stackoverflow.com/q/55492150/6064933)
+ [What will happen if I modify a Python script while it's running?](https://stackoverflow.com/q/5296977/6064933)
+ See CPython run: Getting to know your Python interpreter: https://www.youtube.com/watch?v=tzYhv61piNY
+ Python virtual machine: https://www.ics.uci.edu/~brgallar/week9_3.html
+ python bytecode
    + https://www.youtube.com/watch?v=cSSpnq362Bk
    + https://nedbatchelder.com/blog/201803/is_python_interpreted_or_compiled_yes.html
