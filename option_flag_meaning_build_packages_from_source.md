---
title: Commonly-used Building Options Explained
date: 2018-11-07 21:38:00 +08:00
tags: [GCC, C, C++]
categories: [Linux]
---

On Linux system, if we do not have root priviledge, we can not use package
managers to install a package to its default location (usually under `/usr`).
Besides, the packages installed by the package managers are often too old to
have the latest features. Or, even if we have root priviledge, we do not want
to mess up with the system-wide packages and just want to experiment with a
package locally. On these ocassions, we may want to install the package to a
custom location.

<!--more-->

The standard procedure to build and install a package from source is something
like the following:

```bash
./configure
make && make install
```

By default, the package will be installed under `/usr` and you must have root
priviledge. If you are not familiar with these steps. Read
[here](https://robots.thoughtbot.com/the-magic-behind-configure-make-make-install)
and
[here](https://stackoverflow.com/questions/10961439/why-always-configure-make-make-install-as-3-separate-steps)
for more discussions on what each step does.

In order to install the package to a custom location and find dependency
packages which are not installed on standard locations, we need to set custom
options and variables for the `configure` script. In this post, I will explain
the meaning of `--prefix`, `CLFAGS`, `CXXFLAGS`, `CPPFLAGS` and `LDFLAGS`.

# The meaning of these options and variables

## `--prefix`

`--prefix` is used to tell `make` where to install the compiled packages.  For
example, if you use `--prefix=$HOME/local`, after building the package using
`make -j`, if you do a `make install`, the binary file will be put in
`$HOME/local/bin`, the library file will be put in `$HOME/local/lib` or
`$HOME/local/lib64`, the header file will be put in `$HOME/local/include` and
the help file and other resources may be put in `$HOME/local/share`.

## `CFLAGS` and `CXXFLAGS`

`CFLAGS` and `CXXFLAGS` are used to set up C and C++ compilers respectively. On
most Linux system, the C compiler is usually `gcc`, while the C++ compilers is
usually `g++`.

## `CPPFLAGS`

The `CPPFLAGS` is often mis-understood to set up C++ compiler options. In fact,
it is used by C/C++ preprocessors. It tells preprocessor where to find the
header files. If you need to set up C++ compiler, you should use `CXXFLAGS`
variable. A valid `CPPFLAGS` is something like
`CPPFLAGS="-I$HOME/local/include"`.

## `LDFLAGS`

`LDFLAGS` is used to set up the path of library files. For example,
`LDFLAGS="-L$HOME/local/lib"`.

# Conclusion

In this post, I introduced some of the most commonly-used options and flags for
the configure script when building packages from source. Understanding these
options and flags will help you succeed in building the packages.

# Reference

+ [`CPPFLAGS` vs `CXXLAGS`](https://stackoverflow.com/questions/495598/difference-between-cppflags-and-cxxflags-in-gnu-make).
+ [`CFLAGS` vs `CPPFLAGS`](https://stackoverflow.com/questions/2754966/cflags-vs-cppflags)
+ [Variable meanings from GNU offical doc](https://www.gnu.org/software/make/manual/html_node/Implicit-Variables.html)
+ [compiler flags on Unix system](https://github.com/molpopgen/devnotes/wiki/Compiler-flags-on-Unix-systems)


