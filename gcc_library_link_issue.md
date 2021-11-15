---
title: "Why Are Some Libraries Linked by Default in GCC?"
date: 2020-12-10T23:22:57+08:00
tags: [GCC, Clang]
categories: [Programming]
---

Different behavior of linkage between Clang and GCC.

<!--more-->

# Different behavior of linkage for C and C++

For the following program:
```c
#include <math.h>
#include <stdio.h>

int main() {

    printf("e^{1} = %f\n", exp(1));
    return 0;
}
```

If we compile the program with `clang test.c -o test`, compilation failed with
the following error:

```
/tmp/test-32c2ea.o: In function `main':
test.c:(.text+0x18): undefined reference to `exp'
clang-11: error: linker command failed with exit code 1 (use -v to see invocation)
```

This is totally expected since we use `exp()` function here, which is
implemented in the shared object `libm.so`. For `clang`, the linker will not
link against `libm.so` by default. In order to compile the program, we need to
explicitly link `libm.so`:

```bash
clang test.c -lm -o test
```

One explanation for the separation of libm.so and libc.so is that in the early
days of computing, floating point calculation is expensive. People use various
workarounds for floating point calculations. It makes no sense to integrate
math functions into libc.so. It makes libc.so larger with little benefit. As
computer hardware upgrades with time, the separation of libm and libc does not
make much sense any more.

However, if we *translate* the above program to C++:

```cpp
#include <iostream>
#include <cmath>

int main() {

    std::cout << "e^{1} = " << std:: exp(1) << std::endl;
    return 0;
}
```

we can actually compile the program **without** linking against `libm`:

```bash
clang++ test.cc -o test
```

The output of command `ldd test` is:

```
> ldd test
        linux-vdso.so.1 =>  (0x00007ffd009a4000)
        libstdc++.so.6 => /usr/lib/x86_64-linux-gnu/libstdc++.so.6 (0x00002b4d4d13c000)
        libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00002b4d4d51f000)
        libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00002b4d4d828000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00002b4d4da40000)
        /lib64/ld-linux-x86-64.so.2 (0x00002b4d4cf14000)
```

`libm.so` is actually implicitly linked. According to post [here](https://stackoverflow.com/a/1033940/6064933),
for C++ programs, since `libstdc++.so` requires `libm.so`, so `libm.so` will be
implicitly linked even if you haven't instructed its linkage[^2].

# Some libraries are linked by default.

It is interesting that shared library like `libc.so`[^1] is linked even if we
haven't specified that. This behaviour can be disabled by providing the
`-nostdlib` option:

```bash
clang -nostdlib test.c -o test
```

You will now see an error message about undefined `printf()`:

> test.c:(.text+0x29): undefined reference to `printf'

Ref:

+ [Why does gcc/clang know to link to libc by default?](https://stackoverflow.com/q/54395692/6064933)
+ [Libraries that are linked by default](https://stackoverflow.com/q/6969225/6064933)

# Different between GCC and Clang on linkage

You might think that GCC will also fail if we compile the `test.c` without
linking `libm`, but it is [not true](https://stackoverflow.com/q/38604278/6064933). The following command runs without
error for gcc 4.8.0 and 7.5.0 on my servers:

```bash
gcc test.c -o test
```

This is because GCC uses its built-in implementation of some standard C library
functions to produce faster and smaller executables. According to [post here](https://stackoverflow.com/a/57062793/6064933),
we can use `-fno-builtin` to disable this behavior. We can also use
`-fno-builtin-somefunc` to disable a single function named `somefunc`. Take the
above `test.c` for an example, the following command will result in compilation
error:

```bash
gcc -fno-builtin-sqrt test.c -o test
# adding -lm will fix the compilation errors.
# gcc -fno-builtin-sqrt test.c -lm -o test
```

Ref:

+ [Other Built-in Functions Provided by GCC](https://gcc.gnu.org/onlinedocs/gcc/Other-Builtins.html)
+ https://gcc.gnu.org/onlinedocs/gcc/C-Dialect-Options.html#index-fno-builtin

[^1]: `libc` provides the implementation for functions like `printf()` and `fopen()`.
[^2]: The original post is about GCC, I guess the same is true for clang++?
