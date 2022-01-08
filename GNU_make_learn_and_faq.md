---
title: "GNU Make FAQ"
date: 2021-08-29T23:17:12+08:00
draft: false
tags: [Make]
categories: [tools]
---

[GNU Make](https://www.gnu.org/software/make/) is a [build](https://en.wikipedia.org/wiki/Build_automation) [automation](https://en.wikipedia.org/wiki/List_of_build_automation_software) tool typically used on Linux systems[^1].
Below I will summarize a list of FAQs, which explain how Make works and how to use Make.

<!--more-->

# General questions

## Why do we need Make?

Suppose that we want to build an executable or a library with multiple source and header files.
To build it, we often need to run quite a few commands.
During the development cycle, we need to repeatedly run these commands to build the project.
Of course we can do it manually, but it would be time-consuming and error-prone.

With Make, we can run repeating build tasks easily without having to type the build commands manually.

## How is Make different from CMake?

Make is build tool that uses Makefile to build a project.
CMake is a meta-build tools, in that it can produce a Makefile or other build files such as `build.ninja` for [Ninja](https://ninja-build.org/).

## What name should we use for Makefile?

Make usually looks for `makefile` or `Makefile` in the current directory.
The convention is to use `Makefile`. However, `makefile` also works fine.

## What is a target, recipe?

A rule is a bunch of commands we want to run together to build a target, e.g.,
building a certain object file. The format is like:

```
Target_name: dependency
# <--> indicates a TAB character.
<-->command1
<-->command2
...
```

These commands are called recipe for this target. Each command must start with a `TAB`,
though [it can be changed](https://stackoverflow.com/a/2131227/6064933) in newer versions of make.

A target is the result of executing the rule.

## How does make work generally?

Make is smart. It will not blindly build all the target each time you run it.
Since each target may have some dependencies, and each dependency in turn will have their own dependencies.
In the end, when we make a target, there is a dependency tree.
If you change a source file, make will only compile its parent target and other targets that depend on its parent target recursively.
Specifically, make will check the last-modify time of a target's dependency,
if the time stamp is newer than the target itself, it means that this target should be rebuilt to make it up-to-date.

In summary, make is smart enough to only build a target that needs to be rebuilt, reducing compilation time significantly.

Ref:

+ https://stackoverflow.com/q/22630756/6064933

## What is the default target for Make?

When we simply type `make`, it can build some target.
By default, if we do not specify the target to build, make will pick the first target that does not start with `.` in its name.
So we may use a `default` target (the name does not really matter) as the first target,
to do the default action if the user does not specify one.

Ref:

+ https://stackoverflow.com/q/2057689/6064933


# Usage question

## Difference between `@echo` and `echo`

By default, `make` will print a command before executing it.
Adding `@` before a command will suppress its printing before execution.

Ref:

+ https://www.gnu.org/software/make/manual/html_node/Echoing.html
+ https://stackoverflow.com/a/29576870/6064933


## Difference between `:=` and `=` for assignment?

Variables defined using `:=` get its value when it is defined.
Variables defined using `=` get its value when it is used.
There is a risk that the variable value may change if it depends on the value of other variables.

Ref:

+ https://www.gnu.org/software/make/manual/html_node/Flavors.html#Flavors
+ https://stackoverflow.com/q/4879592/6064933
+ https://stackoverflow.com/q/448910/6064933

## What is a `.PHONY` target and why is it needed?

PHONY target is a target that does not produce a file.
Typically, a target name is the corresponding file to generate.
However, for certain target, we only want to run some command and do not want to generate a target of the same name.

If you do not mark the target as phony, make will first check if there is file of the same name that has been updated.
If there is a file of the same name that has been updated, make will not build that target, which is not what we want.

For example, if you have the following Makefile:

```make
foo:
	@echo "hello, this is target foo"
```

If there is a file named `foo` in the directory, when you run `make foo`, you get the following message:

```
make: 'foo' is up to date
```

So we mark this target as phony (i.e., we do not want to generate file `foo`):

```makefile
.PHONY: target_name
```

By doing this, we make sure that this target will always be build,
ignoring a file with the same name.
Another example of phony target is the `clean` target,
where we want to remove the generated executable and object files:

```make
.PHONY: clean
clean:
	rm *.o main
```

Ref:

+ Purpose of `.PHONY` in Makefile: https://stackoverflow.com/q/2145590/6064933

## Special variables used in Makefile

We can use special variable to simplify the writing of Makefile:

+ `$@`: the target name
+ `$<`: the first dependency
+ `$^`: all the dependencies

```
main: foo.o bar.o
	g++ -o main foo.o bar.o
```

In the above example, `$@` would be `main`. `$<` would be `foo.o` and `$^` would be `foo.o bar.o`.

Ref:

+ https://www.gnu.org/software/make/manual/html_node/Automatic-Variables.html#Automatic-Variables

# Assign command output to a variable

Sometimes, we want to assign the output of some command to a variable.
We can use the `shell` builtin to get the shell command output:

```makefile
VIM_PATH := $(shell which vim)
```

Ref:

+ https://stackoverflow.com/q/2019989/6064933


# References

+ https://www.cs.colby.edu/maxwell/courses/tutorials/maketutor/ (finished)
+ https://web.mit.edu/gnu/doc/html/make_3.html
+ https://makefiletutorial.com/
+ https://www.cs.swarthmore.edu/~newhall/unixhelp/howto_makefiles.html
+ https://www.ruanyifeng.com/blog/2015/02/make.html
+ https://github.com/edhowland/Exploring_make_and_Makefiles
+ https://github.com/unix-tools/tutorial-makefiles
+ https://github.com/weetmuts/MakeTutorial
+ https://github.com/vampy/Makefile

[^1]: Technically, Make can also be used on Windows, see [here](http://gnuwin32.sourceforge.net/packages/make.htm).
