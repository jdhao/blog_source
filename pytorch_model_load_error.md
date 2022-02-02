---
title: "A Dig into PyTorch Model Loading"
date: 2022-01-28T23:17:45+08:00
draft: false
tags: [PyTorch]
categories: [machine-learning]
---

# Saving and loading PyTorch models

Models in PyTorch are a subclass of `torch.nn.Module`. To save the model parameters,
we use `model.state_dict()` to get all the model parameters:

<!--more-->

```python
state = model.state_dict()
```

Then save the model parameter using `torch.save()`:

```python
torch.save(state, 'my-model.pth')
```

# Loading error when using torch.load to load model trained on GPU

When we load a model trained on GPU in a machine with no GPU using `torch.load(model_path)`,
we often get the following error:

> RuntimeError: Attempting to deserialize object on a CUDA device but
> torch.cuda.is_available() is False. If you are running on a CPU-only machine,
> please use torch.load with map_location=torch.device('cpu') to map your
> storages to the CPU.

# The cause

This is because when we use [`torch.save()`](https://pytorch.org/docs/stable/generated/torch.save.html#torch-save) to save an object,
torch will also store the location of original data (called location tag, check [code here](https://github.com/pytorch/pytorch/blob/be2dc8f2940d3c95941516a811be8c504910d1ea/torch/serialization.py#L574)).
`torch.save()` also keeps the view relationship between tensors unchanged, see [here](https://pytorch.org/docs/stable/notes/serialization.html#saving-and-loading-tensors-preserves-views).

Based on [code here](https://github.com/pytorch/pytorch/blob/be2dc8f2940d3c95941516a811be8c504910d1ea/torch/serialization.py#L600-L601), it seems that PyTorch will save the GPU tensor as CPU.

When we use [`torch.load()`](https://pytorch.org/docs/stable/generated/torch.load.html#torch.load), since the tensor location has been recorded,
torch will load the tensor first to CPU, then moves it to the GPU indicated by the location tag.
If that GPU is missing or we are using a CPU machine, the above error will occur.

# Load the model correctly

A better way to load a model is to move it to CPU using the `map_location` parameter of `torch.load()`.
Load the model to CPU, then load the model parameter into the model, finally, move the model to GPU:

```python
# move the model parameter to cpu
state = torch.load('my-model.pth', map_location=torch.device('cpu'))

model.load_state_dict(state)

# now move the model parameter to a GPU device of your choice
model.to(torch.device('cuda:0'))
```

# ref

+ saving and loading models: https://pytorch.org/tutorials/recipes/recipes/save_load_across_devices.html#saving-and-loading-models-across-devices-in-pytorch
