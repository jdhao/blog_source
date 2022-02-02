---
title: "Set the Number of Threads to Use in PyTorch"
date: 2020-07-06 23:15:25+0800
tags: [PyTorch, thread]
categories: [machine-learning]
---

In this post, I will share how PyTorch set the number of the threads to use for
its operations.

<!--more-->

`torch.set_num_threads()` is used to set the number of threads used for intra
operations on CPU.  According to discussions
[here](https://stackoverflow.com/q/41233635/6064933), intra operation roughly
means operations executed within an operation, for example, for matrix
multiplication.  By default, pytorch will use all the available cores on the
computer, to verify this, we can use `torch.get_num_threads()` get the default
threads number.

For operations supporting parallelism, increase the number of threads will
usually leads to faster execution on CPU. Apart from setting the number of
threads via `torch.set_num_threads`, we can also set the env variable
`OMP_NUM_THREADS` or `MKL_NUM_THREADS` to set the number of threads. Below is a
toy script to verify this (adapted from code
[here](https://github.com/pytorch/pytorch/issues/7087#issue-318787926)):

```python
import time

import numpy as np
import torch

INDEX = 10000
NELE = 1000
a = torch.rand(INDEX, NELE)
index = np.random.randint(INDEX-1, size=INDEX*8)
b = torch.from_numpy(index)

start = time.time()
for _ in range(10):
    res = a.index_select(0, b)
print("the number of cpu threads: {}, time: {}".format(torch.get_num_threads(), time.time()-start))
```

Setting `OM_NUM_THREADS` to 1, 2, 4, 8, the running time is:

```
the number of cpu threads: 1, time: 2.927994728088379
the number of cpu threads: 2, time: 1.6809608936309814
the number of cpu threads: 4, time: 1.092754602432251
the number of cpu threads: 8, time: 0.7028472423553467
```

# References

+ [Set env variable to control the num of threads pytorch uses may not always work](https://github.com/pytorch/pytorch/issues/16894).
+ https://github.com/pytorch/pytorch/issues/3146
+ https://github.com/pytorch/pytorch/issues/975
+ https://pytorch.org/docs/stable/notes/cpu_threading_torchscript_inference.html
+ [Meaning of inter_op_parallelism_threads and intra_op_parallelism_threads](https://stackoverflow.com/q/41233635/6064933)
+ https://github.com/pytorch/pytorch/issues/19002
+ https://github.com/pytorch/pytorch/issues/19001
