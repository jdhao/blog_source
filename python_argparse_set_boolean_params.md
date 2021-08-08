---
title: Set up the Default Value for Boolean Option in Argparse
date: 2018-10-11 16:33:00 +0800
tags: []
categories: [Python]
---

TL;DR

If you want to set a parameter's default value to `True` using argparse, use

```
parser.add_argument('--param', action='store_false')
```
Otherwise, use

```
parser.add_argument('--param', action='store_true')
```

<!--more-->

when we use
[argparse](https://docs.python.org/3/library/argparse.html#module-argparse) to
manager program parameters and add a parameter, we often use the
[`action`](https://docs.python.org/3/library/argparse.html#action) options to
set the parameter's value to a boolean value. But I have previously
misunderstood this options and made a mistake. In this post, I will explain how
does this option work and how to use it correctly.  <!--more-->

Typically we use `action='store_true'` or `action='store_false'` to set the
parameter to boolean value.  Previously, I thought that `action='store_true'`
will set the parameter value to `True` and `action='store_false'` will set the
parameter value to `False`. In fact, it is not true. Suppose we have a script
`toy_parser.py`:

```python
import argparase

parser = argparse.ArgumentParser(description="A toy argument parser")
parser.add_argument('--param1', action='store_true')
parser.add_argument('--param2', action='store_false')

args = parser.parse_args()

print(args)
```

If we run this script on the command line with no argument

```bash
python toy_parser.py
```

the output will be:

>  Namespace(param1=False, param2=True)

The result is exactly contratry to what we thought.

What about run the script with parameters?


> $ python toy_parser.py --param1 --param2
> Namespace(param1=True, param2=False)
>
> $ python toy_parser.py --param1
> Namespace(param1=True, param2=True)
>
> $ python toy_parser.py --param2
> Namespace(param1=False, param2=False)

In summary, if a parameter has action `store_true` but is missing in the
command line, the default value will be `False`, otherwise, the value will be
`True`. Similarly, if a parameter has action `store_false` but is missing in
the command line, the default value will be `True`, otherwise, the value will
be `False`.

The following two statements are problematic:

```python
parser.add_argument('--param1', action='store_true', default=True)
parser.add_argument('--param2', action='store_false', default=False)
```

Wheter you use these paramters on the command line, they will always be `True`
or `False`, which is not what we want.
