---
title: "Static and Dynamic Linkage in C++"
date: 2021-07-04T01:02:33+08:00
draft: false
tags: [C++]
categories: [Programming]
---

In C++, we can build a library either statically or dynamically. In this post,
I want to summarize how to build a static and a shared/dynamic library[^1].

<!--more-->

# Build a static and dynamic library

Suppose we have the following source and header files.

`foo.h`:

```cpp
#ifndef _FOO_H
#define _FOO_H
void foo();

#endif
```

`foo.cpp`:

```cpp
#include "foo.h"
#include <iostream>

void foo(){
  std::cout << "This is foo" << std::endl;
}
```

`main.cpp`:

```cpp
 #include "foo.h"

 int main()
 {
   foo();
   return 0;
 }
```

Here is how to build a dynamic and static library:

```bash
g++ -fPIC -c foo.cpp

# build a shared library
g++ -shared -fPIC -o libfoo.so foo.o

# build a static library
ar rvs libfoo.a foo.o

# build main.o object file
g++ -c main.cpp
```

# Use a static and dynamic library

To link the static library and `main.o` to produce an executable, we use:

```bash
g++ main.o libfoo.a -o main
```

The order of object file and library file matters. The following won't work for
static library:

```bash
g++ libfoo.a main.o -o main
```

It will result in the following error:

> main.o: In function `main':
> main.cpp:(.text+0x5): undefined reference to `foo()'
> collect2: error: ld returned 1 exit status

It is because the linker process the files from left to right. If we put static
library before the object file, there is no unresolved symbols yet, all symbol
in the static lib will be dropped. When the object file is processed, the
symbol `foo()` can be resolved since it is the last object that is processed.

According to [here](https://stackoverflow.com/a/29457226/6064933), if you pass
`--start-group` to linker via `-Wl,--start-group`, the linker will search all
libraries for unresolved symbol. So the following works:

```bash
g++ -Wl,--start-group libfoo.a main.o -o main_static
```

If we link the dynamic library with `main.o`, things are different. The
following two commands both work:

```bash
g++ libfoo.so main.o -o main
g++ main.o libfoo.so -o main
```

In this case, the order of library and the object file does not matter.
According to [answer here](https://stackoverflow.com/a/409470/6064933), if we
pass `--as-needed` option to linker via `-Wl,--as-needed`, then the library
file must follow the object file that depend on it.

```bash
g++ -Wl,--as-needed libfoo.so main.o -o main_shared  # will fail with undefined reference error
```

# References

+ Undefined reference error when using static library
    + https://stackoverflow.com/a/23748443/6064933
    + https://stackoverflow.com/a/2487723/6064933
    + https://stackoverflow.com/q/1517138/6064933
    + https://stackoverflow.com/q/23748372/6064933
    + https://stackoverflow.com/a/4623410/6064933
+ link order: https://www.linuxtopia.org/online_books/an_introduction_to_gcc/gccintro_18.html
+ https://cgold.readthedocs.io/en/latest/tutorials/libraries/symbols/link-order.html#link-order
+ https://eli.thegreenplace.net/2013/07/09/library-order-in-static-linking

[^1]: For the pros and cons of using static VS dynamic library, see [this post](https://stackoverflow.com/q/1993390/6064933).
