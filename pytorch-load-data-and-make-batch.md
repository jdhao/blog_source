---
title: "Writing Your Own Custom Dataset for Classification in PyTorch"
date: 2017-10-23 17:14:26 +0800
tags: [PyTorch]
categories: [Deep-Learning]
---

In this post, I'd like to talk about how to create your own dataset, process it
and make data batches ready to be fed into your neural networks, with the help
of PyTorch.

<!--more-->

In PyTorch, in order to feed your own training data into the network, you will
mainly deal with two classes: the
[Dataset](http://pytorch.org/docs/master/data.html#torch.utils.data.Dataset)
class and the
[Dataloader](http://pytorch.org/docs/master/data.html#torch.utils.data.DataLoader)
class. Now I will explain in more detail what they do.

# Create your `Dataset` class

## Overview

`Dataset` class is used to provide an interface for accessing all the training
or testing samples in your dataset. In order to achieve this, you have to
implement at least two methods, `__getitem__` and `__len__` so that each
training sample (in image classification, a sample means an image plus its
class label) can be accessed by its index.

In the initialization part of the class, you should collect a list of all the
images and its labels in the dataset. When we want to get a particular sample,
we then read the image, transform it and return the transformed image and the
corresponding label.

A good example is `ImageFolder` class provided by `torchvision` package, you
can check its source code
[here](https://github.com/pytorch/vision/blob/master/torchvision/datasets/folder.py)
to get a sense of how it actually works.

## Data augmentation and preprocessing

Data augmentation and preprocessing is an important part of the whole
work-flow. In PyTorch, we do it by providing a `transform` parameter to the
`Dataset` class. `Transform` are class object which are called to process the
given input. You can cascade a series of transforms by providing a list of
transforms to
[`torchvision.transforms.Compose`](http://pytorch.org/docs/master/torchvision/transforms.html#torchvision.transforms.Compose)
method. Then the given transforms will be performed on the input in the order
they appear.

It should be noted that some of the transforms are for `PIL` image object, such
as
[RandomCrop()](http://pytorch.org/docs/master/torchvision/transforms.html#torchvision.transforms.RandomCrop)
and
[Resize()](http://pytorch.org/docs/master/torchvision/transforms.html#torchvision.transforms.Resize).
Other transforms are for torch `Tensor`, such as
[Normalize](http://pytorch.org/docs/master/torchvision/transforms.html#torchvision.transforms.Normalize).
If your dataset contains images, you should first perform all transforms
expecting `PIL` image object, then convert `PIL` image to `Tensor` using
[ToTensor()](http://pytorch.org/docs/master/torchvision/transforms.html#torchvision.transforms.ToTensor)
method. The `ToTensor` transform will convert `PIL` image to torch `Tensor` of
shape $H\times W\times C$, with its values in the range [0.0, 1.0].

The `Normalize` transform expects torch tensors. Its parameters are the means
and standard deviations of RGB channels of all the training images. For
ImageNet, the devs have already done that for us, the normalize transform
should be

```python
normalize = transforms.Normalize(mean=[0.485, 0.456, 0.406],
                                 std=[0.229, 0.224, 0.225])
```

For your own dataset, you have to calculate the statistics yourself.

# Create data batch using `Dataloader`

Although we can access all the training data using the `Dataset` class, but
that is not enough. For deep learning, we need the functionality such as
batching, shuffling, multiprocess data loading, etc. This is what the
`Dataloader` class do.

The `Dataloader` class accept a dataset and other parameters such as
`batch_size`, `batch_sampler` and number of `workers` to load the data and so
on... Then we can iterate over the `Dataloader` to get batches of training data
and train our models.

## Loading variable size input images

By default, `Dataloader` use
[`collate_fn`](https://github.com/pytorch/pytorch/blob/master/torch/utils/data/dataloader.py#L83)
method to pack a series of images and target as tensors (first dimension of
tensor is batch size). The default `collate_fn` expects all the images in a
batch to have the same size because it uses `torch.stack()` to pack the images.
If the images provided by `Dataset` have variable size, you have to provide
your custom `collate_fn`. A simple example is shown below:

```python
# a simple custom collate function, just to show the idea

# `batch` is a list of tuple where first element is image tensor and

# second element is corresponding label

def my_collate(batch):
    data = [item[0] for item in batch]  # just form a list of tensor

    target = [item[1] for item in batch]
    target = torch.LongTensor(target)
    return [data, target]
```

# Conclusion

In this post, I give an introduction to the use of `Dataset` and `Dataloader`
in PyTorch. `Dataset` is used to access single sample from your dataset and
transform it, while `Dataloader` is used to load a batch of samples for
training or testing your models. If your training images have variable size,
you may also have to use your own custom `collate_fn`.

# References

+ [Normalization for input images](https://discuss.pytorch.org/t/how-to-preprocess-input-for-pre-trained-networks/683/3).
+ [Another post about image normalization](https://discuss.pytorch.org/t/whats-the-range-of-the-input-value-desired-to-use-pretrained-resnet152-and-vgg19/1683).
+ [Dataloader with variable size images](https://discuss.pytorch.org/t/how-to-create-a-dataloader-with-variable-size-input/8278/3).
+ [ConvNet for variable size input image](https://discuss.pytorch.org/t/how-to-create-convnet-for-variable-size-input-dimension-images/1906).
