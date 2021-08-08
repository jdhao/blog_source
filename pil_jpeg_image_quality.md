---
title: "JPEG Image Quality in PIL"
date: 2019-07-20 21:19:39+0800
tags: [PIL]
categories: [Python]
---

# Introduction #

The other day, I was haunted by a bug and found that it was an issue with the
image quality saved by [Pillow](https://pillow.readthedocs.io/en/stable/) after
spending a few hours debugging the code.

<!--more-->

Initially, the workflow of my code was like loading the JPEG image with PIL
and after some operation, saving the image again. But I didn't give any other
parameters except the file name when using
[`Image.save()`](https://pillow.readthedocs.io/en/stable/reference/Image.html#PIL.Image.Image.save)
method.

# The quality parameter #

After looking up [the documentation](https://pillow.readthedocs.io/en/stable/handbook/image-file-formats.html?#jpeg),
I find that PIL will save the image with quality factor 75 by default so that
the image quality is decreased. The documentation for `quality` says:

> The image quality, on a scale from 1 (worst) to 95 (best). The default is 75.
> Values above 95 should be avoided; 100 disables portions of the JPEG
> compression algorithm, and results in large files with hardly any gain in
> image quality.

Using a quality factor of 95 should be enough to preserve the image quality:

```python
img.save('test.jpg', quality=95)
```

## Subsampling ##

According to [this post](https://stackoverflow.com/questions/15481062/pil-jpeg-how-to-preserve-the-pixel-color),
even if you use `quality=100`, the saved image will still be slightly different
from the original image. This is because PIL will also use
[subsampling](https://en.wikipedia.org/wiki/JPEG#Downsampling) technique to
reduce the image size. You can use `subsampling=0` to turn off this feature.
Overall, to retain the content of original image, you may use:

```python
img.save('test.jpg', quality=100, subsampling=0)
```

# Other options for quality #

For JPEG files, there are also a list of preset options you can use for the
`quality` parameter, such `web_high`, `high`. The complete list of options can
be found [here](https://github.com/python-pillow/Pillow/blob/master/src/PIL/JpegPresets.py#L71).
These preset options will set the `subsampling` factor and quantization table
which is used in image quantization process.

# References

+ [Why is the quality of JPEG images produced by PIL so poor?](https://stackoverflow.com/q/19303621/6064933)
+ [PIL jpeg, how to preserve the pixel color](https://stackoverflow.com/q/15481062/6064933)
