---
title: Notes on PyTorch Tensor Data Types
date: 2017-11-15 21:22:45 +0800
tags: [PyTorch]
category: [Deep-Learning]
---

In PyTorch,
[`Tensor`](http://pytorch.org/docs/master/tensors.html#torch-tensor) is the
primary object that we deal with (`Variable` is just a thin wrapper class for
`Tensor`). In this post, I will give a summary of pitfalls that we should avoid
when using Tensors. Since `FloatTensor` and `LongTensor` are the most popular
`Tensor` types in PyTorch, I will focus on these two data types.<!--more-->

# Tensor operations

## Tensor and Tensor operation

For operations between Tensors, the rule is strict. Both Tensors of the
operation must have the same data type, or you will see error messages like

```
TypeError: sub received an invalid combination of arguments - got (float), but expected one of:
 * (int value)
      didn't match because some of the arguments have invalid types: (!float!)
 * (torch.LongTensor other)
      didn't match because some of the arguments have invalid types: (!float!)
 * (int value, torch.LongTensor other)
```

As another example, several loss functions like
[`CrossEntropyLoss`](http://pytorch.org/docs/master/nn.html#torch.nn.CrossEntropyLoss)
require that the target should be torch `LongTensor`. So before doing
operations, make sure that your input `Tensor` types match the function
definitions.

It is easy to convert the type of one `Tensor` to another `Tensor`. Suppose `x`
and `y` are `Tensor` of different types. You can use `x.type(y.type())` or
`x.type_as(y)` to convert `x` to the type of `y`.

## Tensor and scalar operation

For `FloatTensor`, you can do math operations (multiplication, addition,
division etc.) with a scalar of type `int` or `float`. But for `LongTensor`,
you can only do math operation with `int` scalar **but not `float`**.

# Why do some losses require target to be LongTensor?

[According to PyTorch
developers](https://discuss.pytorch.org/t/problems-with-target-arrays-of-int-int32-types-in-loss-functions/140/3?u=jdhao),
some use cases requires that the target be `LongTensor` type and int just can
not hold the target value.

# FloatTensor or DoubleTensor

For deep learning, precision is not a very important issue. Plus, GPU can not
process double precision very well. So `FloatTensor` is enough, which is also
the default type for model parameters.

# NumPy array and torch Tensor

## Shared memory or not?

You can use `torch.from_numpy()` method to convert a NumPy array to
corresponding torch `Tensor`, which will share underlying memory with NumPy
array. To convert `Tensor` `x` to NumPy array, use `x.numpy()` to convert it to
a NumPy array, which also shares the memory with original `Tensor`.

Does torch `Tensor` and Numpy array always share the underlying memory? The
short answer is no. If their underlying data type is not compatible, a copy of
original data will be made. For example, if you try to save torch `FloatTensor`
as numpy array of type `np.float64`, it will trigger a deep copy.

## Correpsondece between NumPy and torch data type

It should be noted that not all NumPy arrays can be converted to torch
`Tensor`. Below is a table showing NumPy data types which is convertable to
torch `Tensor` type.

NumPy data type | Tensor data type
--- | ---
`numpy.uint8` |`torch.ByteTensor`
`numpy.int16` |`torch.ShortTensor`
`numpy.int32` |`torch.IntTensor`
`numpy.int` |`torch.LongTensor`
`numpy.int64` |`torch.LongTensor`
`numpy.float32` |`torch.FloatTensor`
`numpy.float` |`torch.DoubleTensor`
`numpy.float64` |`torch.DoubleTensor`

## Speed comparison between NumPy and torch operations

I am curious to know the speed difference between torch Tensor operation and
equivalent NumPy ndarray operations. I do it in Jupyter-console using the
built-in magic `%timeit`.

```python
import torch
import numpy as np

# torch Tensor on CPU

x = torch.rand(1, 64)
y = torch.rand(5000, 64)
%timeit z=(x*y).sum(dim=1)

# torch Tensor on GPU

x, y = x.cuda(), y.cuda()
%timeit z = (x*y).sum(dim=1)

# numpy ndarray on CPU

x = np.random.random((1, 64))
y = np.random.random((5000, 64))
%timeit z = (x*y).sum(axis=1)
```

The result is listed on the following table:

Data type and device | Average operation time
--- | ---
Tensor on CPU | 938 $\mu s$
Tensor on GPU | 38.9 $\mu s$
NumPy ndarray (on CPU)| 623 $\mu s$

It is pretty clear that Tensor operations on GPU runs orders of magnitute
faster than operations on CPU. NumPy, due to its excellent implementation of
its core in C, runs a little bit faster than Tensor on CPU.

# Convert scalar to torch Tensor

You can convert a scalar to `Tensor` by providing the scalr to the `Tensor`
constructor, which will not achieve what you want. For
example,`torch.Tensor(1)` will not give you a `Tensor` which contains float 1.
Instead, the produced `Tensor` is something like

>1.00000e-20 *
  5.4514
>[torch.FloatTensor of size 1]

To achieve what you want, you have to provide a list with single element 1 to
the `Tensor` constructor.

# References

+ [Integer type Tensor only works with integer, but float type Tensor works both with integer and float](https://github.com/pytorch/pytorch/issues/845).
+ [Tensor types must match](https://discuss.pytorch.org/t/problems-with-weight-array-of-floattensor-type-in-loss-function/381).
+ [Some loss functions require target to be LongTensor](https://discuss.pytorch.org/t/problems-with-target-arrays-of-int-int32-types-in-loss-functions/140/3).
+ [NumPy and torch Tensor conversion, shared memory or not?](https://github.com/pytorch/pytorch/issues/2246)
