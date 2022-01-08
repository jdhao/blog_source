---
title: "Using STL Containers with pybind11"
date: 2021-12-23T23:21:38+08:00
draft: false
tags: [C++, Make, pybind11]
categories: [Python]
---

In my [old post](https://jdhao.github.io/2021/08/17/pybind11_first_impression/), I have shared how to use [pybind11](https://pybind11.readthedocs.io/) to accelerate execution of Python code.

<!--more-->

In this post, I will introduce how to use STL containers in exported functions.

If we want to use STL containers for pybind11-exported functions,
We need to include the pybind11 stl headers:

```cpp
#include "pybind11/stl.h"
```

Otherwise, we will see the following error messages:

> Did you forget to `#include <pybind11/stl.h>`? Or <pybind11/complex.h>,
> <pybind11/functional.h>, <pybind11/chrono.h>, etc. Some automatic
> conversions are optional and require extra headers to be included
> when compiling your pybind11 module.

Here is the content of a test cpp file `pybind_stl.cc`:

```cpp
#include <iostream>
#include <map>
#include <string>
#include <vector>

#include "pybind11/pybind11.h"
#include "pybind11/stl.h"

namespace py = pybind11;

using std::string;
using std::vector;
using std::map;

int lcs(string s1, string s2) {
  int N1 = s1.size();
  int N2 = s2.size();

  vector<vector<int>> dp(N1 + 1, vector<int>(N2 + 1, 0));

  for (int i = 1; i <= N1; i++) {
    for (int j = 1; j <= N2; j++) {
      if (s1[i - 1] == s2[j - 1]) {
        dp[i][j] = dp[i - 1][j - 1] + 1;
      } else {
        dp[i][j] = std::max(dp[i - 1][j], dp[i][j - 1]);
      }
    }
  }

  return dp[N1][N2];
}

int demo(map<int, int> freq){
  int max = -1;

  for (auto & item: freq){
    if (item.second > max){
      max = item.second;
    }
  }

  return max;
}

PYBIND11_MODULE(stl_demo, m) {
  m.doc() = "LCS calculation"; // optional module docstring

  m.def("lcs", &lcs, "lcs cal", py::arg("s1"), py::arg("s2"));
  m.def("demo", &demo, "demo", py::arg("freq"));
}
```

To convert the C++ source file to shared object that can be imported by Python,
I also created a Makefile to simplify code development.
The content of Makefile is:

```
.PHONY: test clean

CC := g++
FLAGS := -Wall -std=c++11 -shared -fPIC
INC := $(shell python3 -m pybind11 --include)
SUFFIX := $(shell python3-config --extension-suffix)

CC_FILE := pybind_stl.cc
OBJ := stl_demo$(SUFFIX)

$(OBJ): $(CC_FILE)
	$(CC) $(FLAGS) $(INC) $< -o $(OBJ)
test: $(OBJ)
	python test.py
clean:
	rm *.so
```

We use the following `test.py` to check if the cpp code works as expected:

```python
from stl_demo import lcs, demo


def main():
    s1 = "afb"
    s2 = "acfb"

    print(f"lcs len: {lcs(s1, s2)}")

    freq = {2: 3, 1: 4, 3: 5}

    max_cnt = demo(freq)
    print(f"max cnt: {max_cnt}")


if __name__ == "__main__":
    main()
```

To test the demo code, simply run:

```bash
make test
```

If everything works correctly, we will get the following result:

> lcs len: 3\
> max cnt: 5
