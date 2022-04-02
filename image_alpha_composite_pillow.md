---
title: "How to Do Image Alpha-compositing in Pillow"
date: 2022-04-01T23:40:04+08:00
markup: pandoc
tags: [PIL]
categories: [Python]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202204020034075.jpg" width="800">
</p>

# What is alpha-compositing

If you have two images A and B, you [alpha-composite](https://en.wikipedia.org/wiki/Alpha_compositing) A over B,
it will create an effect that you can see both image A and B in the resulting image,
as if A has transparency and you can *see* through A and also see B.

<!--more-->

# How does alpha-compositing work?

Suppose a is the src image, b is the destination image, we composite a over b.
The equation for alpha value and pixel value of the generated image are:

$$\alpha = \alpha_a + \alpha_b (1-\alpha_a)$$

$$\begin{aligned} v &= \frac{ \alpha_a v_a + \alpha_b (1-\alpha_a) v_b}{\alpha_a + \alpha_b (1-\alpha_a)}\\
                    &= \frac{\alpha_a}{\alpha_a + \alpha_b (1-\alpha_a)} v_a + \frac{\alpha_b (1-\alpha_a)}{\alpha_a + \alpha_b (1-\alpha_a)} v_b
\end{aligned}$$

So it is easy to see that the resulting pixel value is a linear combination of pixel value from a and b.

For RGB images, this is done for per-channel per-pixel.

Note that in Pillow, the alpha value is rescaled from $[0, 1.0]$ to $[0, 255]$.
So in the following text, I will use the range $[0, 255]$ and it should not cause any confusion.

```python
import numpy as np
from PIL import Image


def main():
    # alpha for im1 is 0.5 (rescaled in [0, 1.0] range)
    im1 = Image.new('RGBA', (100, 100), (200, 0, 0, 128))
    # alpha for im2 is also 0.5
    im2 = Image.new('RGBA', (100, 100), (0, 100, 0, 128))

    # im1.show()
    # im2.show()

    im1.alpha_composite(im2)
    im1.show()

    r, g, b, a = im1.split()
    print(np.array(g))


if __name__ == "__main__":
    main()
```

To test the correctness of the above equations, use the above script.
After alpha compositing, the value for channel R and G of `im1` should be all 67,
for channel `B` should be all 0, and for alpha channel should be all 192 (without rescale, it should be 0.75).

# Composite two JPEG images

So if we have a large image A and we want to overlay a small image B onto it.
How do we do it correctly? Suppose A and B are just JPEG images with no alpha channels.

We should first add an alpha channel with value 255 for image A.
This is the crucial step for correct alpha-compositing.
This will not affect the region in A that do not have overlap with image B.
Otherwise, you will get a opaque look for those regions.

Then we should set proper alpha for image B, depending on how you want to composite.
In this case, the pixel value for the resulting image becomes simply:

$$v = \alpha v_a + (1-\alpha) v_b$$

You can change $\alpha$ to control the contribution of A and B to the result image.
For example, if you want B to completely dominate the overlapping region, you can set alpha to 255.
If you want to show partial A and B, you can set alpha to a value between 0 and 255.
The larger alpha gets, the less you will *see* the underlying part of image A.

```python
import numpy as np

from PIL import Image, ImageFilter


def main():
    im = Image.open('./cat.jpg')
    im.putalpha(255)

    em_im = Image.open('./fish.jpg')
    em_im.putalpha(150)

    dst_coord = (360, 310)
    src_coord = (0, 0)

    im.alpha_composite(em_im, dst_coord, src_coord)
    im.save('cat-and-fish.png')


if __name__ == "__main__":
    main()
```

In the title image, I show the generated image using three different alpha values for image B.
The transparency decreases as we increase the value of alpha.

# References

- <https://pillow.readthedocs.io/en/stable/reference/Image.html#PIL.Image.Image.alpha_composite>
