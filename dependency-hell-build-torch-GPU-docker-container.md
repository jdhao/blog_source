---
title: "Dependency Hell When Building A PyTorch GPU Docker Image"
date: 2022-02-09T21:30:00+08:00
draft: false
tags: [PyTorch, Docker]
categories: [machine-learning]
---

In order to for PyTorch to use host GPU inside a Docker container, their versions must match.

<!--more-->

# Use correct nvidia-cuda docker as base image

First, in order to use GPU, we can not just use a regular docker image as the base image,
we need to use images provided by [`nvidia/cuda`](https://hub.docker.com/r/nvidia/cuda). For example, to use CUDA 10.1 as the base image:

```docker
FROM nvidia/cuda:10.1-cudnn7-devel-ubuntu18.04
#... other build steps follows
```

Otherwise, even if you have installed PyTorch inside the container, `torch.cuda.is_available()` is still `False`.

# PyTorch and torchvision versions

Another point to remember is to install the right version of PyTorch that supports your CUDA versions.
If you install PyTorch without specifying the version, the latest one is installed.
It will have higher requirement for CUDA version. If you try to use CUDA, then you see error like this:

<style type="text/css">
@import url('//maxcdn.bootstrapcdn.com/font-awesome/4.2.0/css/font-awesome.min.css');

.error-msg {
    color: #D8000C;
    background-color: #FFBABA;
    margin: 5px 0;
    margin-bottom: 20px;
    padding: 10px;
    border-radius: 5px 5px 5px 5px;
    border: 2px solid transparent;
    border-color: transparent;
}
</style>

<div class="error-msg">
    <i class="fa fa-times-circle"></i>
    pytorch the nvidia driver on your system is too old (found version 10010)
</div>

To check which version of CUDA torch is built with, use `torch.version.cuda` (source [here](https://stackoverflow.com/a/64145989/6064933)).

## Find suitable PyTorch version

To find the PyTorch version built with CUDA 10.1, in the [PyTorch stable release page](https://download.pytorch.org/whl/torch_stable.html), search `cu101`.
We know that there is v1.6.0 built with CUDA 10.1, then we can run the following command to install v1.6.0:

```
pip install torch==1.6.0+cu101 -f https://download.pytorch.org/whl/torch_stable.html
```

## Match torchvision with torch version

The torchvision package version should also match version of torch.
In [this page](https://github.com/pytorch/vision#installation), we can see a table of version correspondence.
For example, for torch 1.6.0, torchvision 0.7.0 is fine. So our final install command:

```
pip install torch==1.6.0+cu101 torchvision==0.7.0 -f https://download.pytorch.org/whl/torch_stable.html
```

# Ref

https://pytorch.org/get-started/previous-versions/
