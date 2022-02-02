---
title: "Difference between view, reshape, transpose and permute in PyTorch"
date: 2019-07-10 23:21:48+0800
tags: [PyTorch]
categories: [machine-learning]
---

PyTorch provides a lot of methods for the Tensor type. Some of these methods
may be confusing for new users. Here, I would like to talk about
[`view()`](https://pytorch.org/docs/stable/tensors.html#torch.Tensor.view)  vs
[`reshape()`](https://pytorch.org/docs/stable/torch.html#torch.reshape),
[`transpose()`](https://pytorch.org/docs/stable/torch.html#torch.transpose) vs
[`permute()`](https://pytorch.org/docs/stable/tensors.html#torch.Tensor.permute).

<!--more-->

# view() vs reshape() and transpose()

## view() vs transpose()

Both `view()` and `reshape()` can be used to change the size or shape of
tensors. But they are slightly different.

The `view()` has existed for a long time. It will return a tensor with the new
shape. The returned tensor shares the underling data with the original tensor.
If you change the tensor value in the returned tensor, the corresponding value
in the viewed tensor also changes.

On the other hand, it seems that `reshape()` [has been introduced in version
0.4](https://github.com/pytorch/pytorch/pull/5575). According to the
[document](http://pytorch.org/docs/master/torch.html#torch.reshape), this
method will

>Returns a tensor with the same data and number of elements as input, but with the specified shape. When possible, the returned tensor will be a view of input. Otherwise, it will be a copy. Contiguous inputs and inputs with compatible strides can be reshaped without copying, but you should not depend on the copying vs. viewing behavior.

It means that `torch.reshape` may return a copy or a view of the original
tensor. You can not count on that to return a view or a copy. According to the
developer:

> if you need a copy use clone() if you need the same storage use view(). The semantics of reshape() are that it may or may not share the storage and you don't know beforehand.
>

As a side note, I found that torch version 0.4.1 and 1.0.1 behaves differently
when you print the `id` of original tensor and viewing tensor:

```
In [1]: import torch

In [2]: a = torch.rand(3, 4)

In [3]: id(a), id(a.storage())
Out[3]: (2236511690472, 2236511611848)

In [4]: b = a.view(2, 6)

In [5]: id(b), id(b.storage())
Out[5]: (2236523527984, 2236470501128)
```

You see that `id` of `a.storage()` and `b.storage()` is not the same. Isn't
that their underlying data the same? Why this difference?

I filed [an issue](https://github.com/pytorch/pytorch/issues/22614) in the
PyTorch repo and got answers from the developer. It turns out that to find the
data pointer, we have to use the `data_ptr()` method. You will find that their
data pointers are the same.

## view() vs transpose()

`transpose()`, like `view()` can also be used to change the shape of a tensor
and it also returns a new tensor sharing the data with the original tensor:

> Returns a tensor that is a transposed version of input. The given dimensions dim0 and dim1 are swapped.
>
> The resulting out tensor shares it’s underlying storage with the input tensor, so changing the content of one would change the content of the other.
>

One difference is that `view()` can only operate on contiguous tensor and the
returned tensor is still contiguous. `transpose()` can operate both on
contiguous and non-contiguous tensor. Unlike `view()`, the returned tensor may
be not contiguous any more.

### But what does contiguous mean?

There is [a good answer](https://stackoverflow.com/a/26999092/6064933) on SO
which discusses the meaning of `contiguous` in Numpy. It also applies to
PyTorch.

As I understand, `contiguous` in PyTorch means if the neighboring elements in
the tensor are actually next to each other in memory. Let's take a simple
example:

```
x = torch.tensor([[1, 2, 3], [4, 5, 6]]) # x is contiguous
y = torch.transpose(0, 1) # y is non-contiguous
```

Tensor `x` and `y` in the above example share the same memory space[^1].

```
print(x.data_ptr()) # 94018404758288
print(y.data_ptr()) # 94018404758288
```

If you check their contiguity with
[`is_contiguous()`](https://pytorch.org/docs/stable/tensors.html#torch.Tensor.is_contiguous),
you will find that `x` is contiguous but `y` is not.

```
print(x.is_contiguous()) # True
print(y.is_contiguous()) # False
```

Since x is contiguous, x[0][0] and x[0][1] are next to each other in memory.
But y[0][0] and y[0][1] is not.

A lot of tensor operations requires that the tensor should be contiguous,
otherwise, an error will be thrown. To make a non-contiguous tensor become
contiguous, use call the
[`contiguous()`](https://pytorch.org/docs/stable/tensors.html#torch.Tensor.contiguous),
which will return a new contiguous tensor. In plain words, it will create a new
memory space for the new tensor and copy the value from the non-contiguous
tensor to the new tensor.

# transpose() and permute()

`permute()` and `tranpose()` are similar. `transpose()` can only swap two
dimension. But `permute()` can swap all the dimensions. For example:

```
x = torch.rand(16, 32, 3)
y = x.tranpose(0, 2)

z = x.permute(2, 1, 0)
```

Note that, in `permute()`, you must provide the new order of all the
dimensions. In `transpose()`, you can only provide two dimensions. `tranpose()`
can be thought as a special case of `permute()` method in for 2D tensors.

# References

+ https://discuss.pytorch.org/t/in-pytorch-0-4-is-it-recommended-to-use-reshape-than-view-when-it-is-possible/17034
+ [tensor data pointers](https://github.com/pytorch/pytorch/issues/1649).
+ [view after transpose raises non-contiguous error](https://github.com/pytorch/pytorch/issues/764).
+ [When to use which, permute, view, transpose](https://discuss.pytorch.org/t/different-between-permute-transpose-view-which-should-i-use/32916).
+ [Difference between reshape() and view()](https://stackoverflow.com/a/49644300/6064933).

[^1]: To show a tensor's memory address, use `tensor.data_ptr()`.
