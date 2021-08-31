---
title: "Pybind11 hands on"
date: 2021-08-17T00:24:37+08:00
draft: false
tags: [pybind11,C++,Make]
categories: [Python]
---

To accelerate the execution of some performance critical code, we can write the
code in C++ with the help of [pybind11](https://github.com/pybind/pybind11) and
export the C++ code as shared library[^1]. Then we can import the shared
library as a module and enjoy speed boost.

<!--more-->

Install pybind11:

```bash
python3 -m pip install pybind11
```

# A simple code using pybind11

A simple example using pybind11 is shown below:

```cpp
// include pybind11 header files so that we can use PYBIND11_MODULE macro
#include <pybind11/pybind11.h>

namespace py = pybind11;

int sum(int start, int end){
    // calculate sum from i to j
  if (start > end) return 0;

  int sum = 0;
  for (int i = start; i <= end; i++){
    sum += i;
  }

  return sum;
}

PYBIND11_MODULE(demo, m) {
    m.doc() = "pybind11 demo plugin"; // optional module docstring

    m.def("sum", &sum, "calculate sum from start to end",
        py::arg("start") = 1, py::arg("end") = 1000);
}
```

In the above code, we use macro `PYBIND11_MODULE` to define a module, whose
name is `demo` and represented as `m`. `m.def()` is used to register function
to this module:

+ The first parameter is the function name you want to use. It does not need to
be the same with the C++ function name.
+ Second parameter is the function address (`&sum`).
+ Third parameter is the function documentation.
+ `py:arg()` is used to add keyword argument and its default values to the function.


# Compile the C++ code

## On the command line

We can directory compile the code on the command line:

```bash
c++ -O3 -Wall -shared -std=c++11 -fPIC -I$(python3 -m pybind11 --includes) demo.cc -o demo$(pyton3-config --extension-suffix)
```

In the above command, `python3 -m pybind11 --includes` is used to get the
relevant include path for pybind11. On my system, the output is like the following:

> -I/Users/jdhao/tools/miniconda3/include/python3.9 -I/Users/jdhao/tools/miniconda3/lib/python3.9/site-packages/pybind11/include

The command `python3-config --extension-suffix` is used to get the proper
suffix for shared library based on current system.

## Compile using Makefile

It would be tedious to type the above command each time to compile the code. We
can write a simple Makefile to simplify the work:

```make
INCLUDE := $(shell python3 -m pybind11 --includes)
FLAG := -O3 -Wall -shared -std=c++11 -fPIC
SUFFIX := $(shell python3-config --extension-suffix)
demo:
    c++ $(FLAG) $(INCLUDE) demo.cc -o demo$(SUFFIX)
```

Then use `make demo` to compile.

# Import and run the code

Open a Python interpreter and we can use the module just like other Python modules:

```python
import demo

print(demo.sum())  # use the default parameter value

print(demo.sum(start=1, end=100))
```


# References

+ Official doc: https://pybind11.readthedocs.io/en/latest/basics.html

[^1]:  Of course, there are [other ways](https://realpython.com/python-bindings-overview/) to run C++ code in Python.
