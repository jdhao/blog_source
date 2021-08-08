---
title: "Profile Python Code with line_profiler"
date: 2020-11-06T01:03:18+08:00
draft: false
tags: []
categories: [Python]
---

To profile Python code line by line. We can use [line_profiler](https://github.com/pyutils/line_profiler)[^1].

<!--more-->

# Install #

## Linux:

Use pip to install this package:

```bash
pip install line_profiler
```

## Windows

It is easier to install using conda, since it does not require building the
package from source:

```
# if you use pip on Windows, you must have Visual Studio to build the package.
conda install line_profiler
```

# How to profile?

Annotate the function we want to profile with `@profile` annotation. On the
command line, run:

```bash
kernprof -v -l test_script.py
```

The command line options to `test_script.py` can be followed after it. Just
like you call `python test_script.py`.

# Ref

+ https://jakevdp.github.io/PythonDataScienceHandbook/01.07-timing-and-profiling.html
+ [How can I profile Python code line-by-line?](https://stackoverflow.com/q/3927628/6064933)

[^1]: This is a fork of original [line_profiler repo](https://github.com/rkern/line_profiler). The original repo is not maintained.
