---
title: "Set Default GPU in PyTorch"
date: 2018-04-02 11:13:22 +0800
tags: [PyTorch]
categories: [machine-learning]
---

You can use two ways to set the GPU you want to use by default.

<!--more-->

# Set up the device which PyTorch can see

The first way is to restrict the GPU device that PyTorch can see. For example,
if you have four GPUs on your system[^1] and you want to GPU 2. We can use the
environment variable `CUDA_VISIBLE_DEVICES` to control which GPU PyTorch can
see. The following code should do the job:

```bash
CUDA_VISIBLE_DEVICES=2 python test.py
```

The above code ensures that the GPU 2 is used as the default GPU. You do not
have to change anything in your source file `test.py`

If you want to set the environment in your script. Then you can use
[os.environ](https://stackoverflow.com/questions/5971312/how-to-set-environment-variables-in-python)
to set the environment variables. In order to use GPU 2, you can use the
following code

```python
import os
os.environ['CUDA_VISIBLE_DEVICES']='2'
```

There is something that we should notice. If you have set up
`CUDA_VISIBLE_DEVICES`. The actuall device will be numbered from zero. For
example, if you use

```python
os.environ['CUDA_VISIBLE_DEVICES']='2,3'
```

Then GPU 2 on your system now has ID 0 and GPU 3 has ID 1. In other words, in
PyTorch, device#0 corresponds to your GPU 2 and device#1 corresponds to GPU 3.

# Directly set up which GPU to use

You can also directly set up which GPU to use with PyTorch. The method is
[torch.cuda.set_device](http://pytorch.org/docs/0.3.1/cuda.html#torch.cuda.set_device).
For example, to use GPU 1, use the following code before any GPU-related code:

```python
import torch as th

th.cuda.set_device(1)
```

---

# References

+ <https://github.com/pytorch/pytorch/issues/260>   
+ <https://discuss.pytorch.org/t/how-to-change-the-default-device-of-gpu-device-ids-0/1041/13>  

[^1]: Their ids are 0, 1, 2, 3
