---
title: "Pretty Output Format in C++"
date: 2021-07-04T00:26:30+08:00
draft: false
tags: [C++]
categories: [Programming]
---

Although `std::cout` in C++ is powerful, it lacks the ease of use of [format
string](https://docs.python.org/3/tutorial/inputoutput.html#the-string-format-method) in Python. Fortunately, the 3rd party package [fmt](https://github.com/fmtlib/fmt)
provides a similar feature for C++.

<!--more-->

# Build fmt

To use the fmt package, we need to build it:

```bash
git clone --depth 1 https://github.com/fmtlib/fmt.git
cd fmt
mkdir build && cd build
cmake -G "Unix Makefiles" -DCMAKE_INSTALL_PREFIX=~/tools/fmt -DCMAKE_BUILD_TYPE=Release ..
make -j 8
make install
```

This will build the static version of fmt and install it under
`$HOME/tools/fmt`.

To build a dynamic library, use the following cmake command instead:

```bash
cmake -G "Unix Makefiles" -DCMAKE_INSTALL_PREFIX=~/tools/fmt -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=TRUE ..
```

# How to use

Here is a short snippet to test it:

```cpp
#include <iostream>
#include <vector>
#include <fmt/ranges.h>
#include <fmt/core.h>

using std::vector;

int main() {
  vector<int> arr = {1, 2, 3};

  fmt::print("arr is {}", arr);

  fmt::print("The price of {} is {} Yuan/kg", "apple", 4.2);
  return 0;
}
```

Use the following comamnd to build against the static version of fmt:

```bash
g++ -L$HOME/tools/fmt/lib64/ -I$HOME/tools/fmt/include/ test.cc -l:libfmt.a -o test_static
```

And the following command to build against the dynamic version of fmt:

```bash
g++ -L$HOME/tools/fmt/lib64/ -I$HOME/tools/fmt/include/ test.cc -lfmt -o test_shared
```

To run the dynamic executable, you need to set up the env variable
`LD_LIBRARY_PATH` properly:

```bash
LD_LIBRARY_PATH=$HOME/tools/fmt/lib64:$LD_LIBRARY_PATH ./test_shared
```

The size differs significantly between the static and dynamic executable files:

```
32K     test_shared
220K    test_static
```

# References

+ Building fmt: https://fmt.dev/latest/usage.html#building
+ Link library statically
    + https://stackoverflow.com/a/20728782/6064933
    + https://stackoverflow.com/a/3701129/6064933
    + https://stackoverflow.com/q/3698321/6064933
+ Use a cpp package:
    + https://stackoverflow.com/a/1656679/6064933
    + https://stackoverflow.com/a/46905510/6064933
    + https://stackoverflow.com/a/10358977/6064933
