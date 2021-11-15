---
title: "How to Debug Python Code in Terminal"
date: 2019-01-16 10:21:12 +0800
tags: [debug, terminal]
categories: [Python]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190116225245.jpg" width="600">
</p>

<!--more-->

When I use [Pycharm](https://www.jetbrains.com/pycharm/download/) a while ago,
I get used to debugging my code with its built-in GUI debugger, which works
really well. But when I write code on a server without X11 support, I am forced
to use [Neovim](https://neovim.io/) for Python development. My debugging is
reduced to a serize of `print` statement in the source code. It works well for
simple programs. Once my source code becomes long enough or complex enough, it
becomes inefficient to add `print` one by one to just play with an object.

Finally, I decided to give terminal debuggers a try. In this post, I introduce
three debuggers.

# Pdb #

Python ships with a native debugger called
[pdb](https://docs.python.org/3/library/pdb.html).

You can debug your code with intrusive way or non-introsive way. For the
intrusive way, you need to modify your source code:

```python
import pdb

# put the following statement where you want the code to stop and step to pdb
pdb.set_trace()
```

Personally, I prefer the non-intrusive way to invode pdb. You can run pdb as module:

```bash
python -m pdb your_script.py
```

It will start debugging your code from the begining of the code.

## Common commands

Pdb provides a few commands to help you debug your code on its interacitve
interface. Here, I list some of the commands and their meanings:

+ `n`: execute the next line
+ `p`: print the value of an object
+ `s`: step into a function
+ `r`: return from a function
+ `b [num]`: set a breakpoint at line `[NUM]`
+ `c`: continue to run the code until a break point is met
+ `unt [NUM]`: run the code until line `[NUM]`
+ `whatis`: print the type of object (similar to `p type(some_object)`)
+ `l`: list the context of current line (default 11 lines)
+ `h`: show help message
+ `q`: quit the debugger

## How to enable autocomplete ##

By default, pdb does not provide auto-completion for variable names and object
methods. To enable auto-completion, create a file `.pdbrc` under your **HOME**
directory with the following settings:

```
import rlcompleter
import pdb

pdb.Pdb.complete=rlcompleter.Completer(locals()).complete
```

# ipdb #

[Ipdb](https://github.com/gotcha/ipdb) is like pdb, but with built-in
autocompletion, syntax highlighting and more commands. You can install it with
pip:

```
pip install ipdb
```

Its usage is similar to pdb, so I will not waste time here. One thing worth
noting is that ipdb adds a `sticky` command:

```
sticky [start end]

Toggle sticky mode. When in sticky mode, it clear the screen
and longlist the current functions, making the source
appearing always in the same position. Useful to follow the
flow control of a function when doing step-by-step execution.

If ``start`` and ``end`` are given, sticky mode is enabled and
only lines within that range (extremes included) will be
displayed.
```

It means that in stick mode, the current function will be shown and current
line will be highlighted to make your follow the execution of program easier.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190116230104.png" width="600">
</p>

# pudb #

Another terminal debugger worth trying is
[pudb](https://documen.tician.de/pudb/misc.html). First, install it with pip:

```
pip install pudb
```

To invoke it, you can either use

```
python -m pudb my_script.py
```

or simply

```
pudb3 my_script.py
```

Pudb creates a GUI-like interface for debugging your code. It will show your
code, current variable, stack trace, break points and a terminal window in its
terminal interface.

![](https://blog-resource-1257868508.file.myqcloud.com/20190116230928.png)

You can use `k` and `j` to move the cursor line up and down and use `b` to set
a break point, which will be highlighted in red. To execute next line of code,
use `n`. To run the code until a break point, use `c`. For more usage, press
`h`.

Overall, it is quite powerful, considering that it is implemented in a
terminal.

# Conclusion

In this post, I introduced three Python debuggers --- pdb, ipdb and pudb. They
can both help us to debug the code. Personally, I think pudb is the most
*beautiful* one I want to use.

# References

+ [Enable auto-completion for pdb](https://stackoverflow.com/questions/15971201/autocomplete-and-tab-key-in-pdb).
+ [Python debugger discussions](https://stackoverflow.com/questions/4929251/can-you-step-through-python-code-to-help-debug-issues)
+ [A list of Python debugger](https://wiki.python.org/moin/PythonDebuggingTools).

---
Title image is taken from [here](https://opensourceprojects.org/python-debugging-using-pdb/)
