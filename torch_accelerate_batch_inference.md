---
title: "Accelerate Batched Image Inference in PyTorch"
date: 2022-03-18T22:59:33+08:00
draft: false
tags: [PyTorch]
categories: [machine-learning]
---

I have a web service where the images come in a batch so I have to do inference for several images in PIL format at a time.
Initially, I use a naive approach and just transform the images one by one,
then combine them to form a single tensor and do the inference.
The inference becomes really slow when I have a batch with more than 30 images.
The inference time is about 1-2 seconds per batch.

<!--more-->

# Should we set cudnn.benchmark to True?

Some blog posts have recommend an easy way to speed your inference: setting `torch.backends.cudnn.benchmark` to `True`.
By setting this option to `True`, cudnn will try to find the fastest convolution algorithm for your input shape.
However, this only works when the input shape to the model does not change.
If the input shape changes, the time cost will actually be worse[^1].

# Dataset and DataLoader for inference

After some debugging, I found that data transformation may be the bottleneck.
In the naive approach, the data processing for the images are done sequentially,
sometime like this:

```python
processed_imgs = [transform(im) for im in pil_imgs]
```

Actually we can use `DataLoader` from torch to accelerate the image processing speed.
We need to define a `Dataset` and `DataLoader` for the inference.

```python
class InferDataset(torch.utils.data.Dataset):
    def __init__(self, pil_imgs):
        super(InferDataset, self,).__init__()

        self.pil_imgs = pil_imgs
        self.transform = make_transform() # some infer transform

    def __len__(self):
        return len(self.pil_imgs)

    def __getitem__(self, idx):
        img = self.pil_imgs[idx]

        return self.transform(img)


infer_data = InferDataset(pil_imgs)
infer_loader = torch.utils.data.DataLoader(infer_data,
                                           batch_size=64,
                                           shuffle=False,
                                           num_workers=4,
                                           pin_memory=True)
with torch.no_grad():
    for data in infer_loader:
        data = data.cuda()
        output = model(data)
        # ... more processing
```

# Use torch.cuda.synchronize() for correct benchmarking

Note that the torch cuda operations are asynchronous, which will return without waiting to finish.
To time a cuda operation correctly, we need to use `torch.cuda.synchronize()` to wait for the operation to finish.
So the timing code should be like this:

```python
torch.cuda.synchronize()
start = time.time()
# your cuda operations go here, for example, out = mode(input)
torch.cuda.synchronize()

end = time.time()
print(f"elapse: {end-start}")
```

# Important parameters

The parameters that impact the speed most are `batch_size` and `num_workers`.

If GPU memory permits, using a large batch size will be faster since we have fewer iterations to run.
The exact value for batch size should be benchmarked on your system.

The parameter `num_worker` means the number of worker processes used for fetching data.
When it is 0, only the main processes will be used, which will be slow.
However, it does not mean more workers will definitely lead to faster processing speed.
We need to benchmark and choose a suitable value.
Generally, it should not exceed the number of CPU cores we have.
For example, I found that setting `num_workers` to 1 works the fastest for me.

The parameter `pin_memory=True` will reduce the time cost for transferring data from your CPU to GPU
(detail [here](https://developer.nvidia.com/blog/how-optimize-data-transfers-cuda-cc/)), thus accelerating data processing.
So in generally, it should be always used.

# Conclusion

With all these optimizations, I was able to reduce the batched image inference time from 2 seconds to about 100 ms.

# References

+ https://www.willprice.dev/2021/03/27/debugging-pytorch-performance-bottlenecks.html
+ https://leimao.github.io/blog/PyTorch-Benchmark/
+ https://auro-227.medium.com/timing-your-pytorch-code-fragments-e1a556e81f2

[^1]: See also discussion [here](https://discuss.pytorch.org/t/what-does-torch-backends-cudnn-benchmark-do/5936/31?u=jdhao).
