---
title: "Pillow/PIL 缩放索引图像时的一个问题"
date: 2020-11-18T23:38:02+08:00
tags: [PIL, OpenCV]
categories: [Python]
---

今天遇到了[一个有趣的问题](https://www.v2ex.com/t/726518)，有人在 v2ex 上说自己的图片，经过 [PIL](https://python-pillow.org/) 缩放到 800x600 以后，非常模糊，有什么办法可以解决。

<!--more-->

# 现象与问题

我想这不可能啊，我把他的原图下载到本地，然后使用 PIL 进行缩放保存，原始的代码如下：

```python
from PIL import Image


def main():
    img = Image.open('big_image.png')
    im_resize = img.resize((800, 600), resample=Image.LANCZOS)
    im_resize.save('big-resized-pil.png', quality=90)


if __name__ == "__main__":
    main()
```

发现保存的图片中，文字部分的确很模糊，`Image.LANCZOS` 已经是[最好的滤波器](https://pillow.readthedocs.io/en/stable/handbook/concepts.html#filters-comparison-table)了，按理说不应该出现这种问题。我又把 PIL 提供的[其他几种滤波器](https://pillow.readthedocs.io/en/stable/handbook/concepts.html#filters)都试了一下，发现出来的结果没区别，都很差。

我又用 OpenCV 试了一下，发现使用 OpenCV 缩放得到的图片效果还可以，下面是我的代码：

```python
 import cv2

 def main():
     im = cv2.imread('big_image.png')
     # https://docs.opencv.org/master/da/d54/group__imgproc__transform.html#ga47a974309e9102f5f08231edc7e7529d
     im_resize = cv2.resize(im, (800, 600), interpolation=cv2.INTER_LANCZOS4)
     write_param = [cv2.IMWRITE_PNG_COMPRESSION, 5]
     cv2.imwrite('big-resized.png', im_resize, write_param)


 if __name__ == "__main__":
     main()
```

缩放以后的图片明显比 PIL 缩放出来的效果好了很多，上面的 [Interpolation](https://docs.opencv.org/master/da/d54/group__imgproc__transform.html#ga5bb5a1fea74ea38e1a5445ca803ff121) 参数类似于 PIL 中的 resample 参数，用来控制图像缩放时候使用的滤波器，不光 `cv2.INTER_LANCZOS4` 出来的效果可以，甚至 `cv2.INTER_AREA` 出来的效果也不错。

即便 OpenCV 和 PIL 实现某个滤波器的细节有差异，缩放出来的结果差异也不应该如此明显，这样巨大的差异很难用实现细节不同来解释清楚。

# 原因

后面那个问题又有人回复，说可能是因为提问者给的图是索引图 ([index](https://en.wikipedia.org/wiki/Indexed_color) image，也叫 [palette](https://en.wikipedia.org/wiki/Palette_(computing)) image) (索引图读取以后，使用 `print(im.mode)` 会输出 `'P'`)。如果图片本身是索引图，PIL 在读取图片并不会自动转换为 RGB 图片，因此返回的 Image 对象仍然是索引图，这就带来了问题。

如果我们仔细阅读 [`Image.resize()`](https://pillow.readthedocs.io/en/stable/reference/Image.html#PIL.Image.Image.resize) 方法的文档，其中对 resample 参数的解释提到：

> If the image has mode “1” or “P”, it is always set to [`PIL.Image.NEAREST`](https://pillow.readthedocs.io/en/stable/handbook/concepts.html#PIL.Image.NEAREST "PIL.Image.NEAREST")

这样就解释了为什么我们最初使用 `Image.LANCZOS` 滤波器不管用的问题：因为这个图像是索引图像，PIL 只会使用 `Image.NEAREST` 滤波器，我们自己设定的滤波器压根没生效，所以才会出现糟糕的压缩效果。

知道了这个原因，解决方法也很简单，那就是读取图像的时候，使用 [`Image.convert()`](https://pillow.readthedocs.io/en/stable/reference/Image.html#PIL.Image.Image.convert) 把图像转为 `RGB` 模式:

```python
    img = Image.open('big_image.png').convert('RGB')
```

然后对图像进行缩放，得到的结果就与 OpenCV 缩放以后的结果基本一致了。

# 什么是索引图

我们常见的 RGB 图片，图片的每个像素分别用 R, G, B 三个颜色分量表示，一般取值是在 0-255 之间，每个颜色分量占 1 byte，所以一个像素占 3 byte 空间，如果图像大小为 MxN，那么图像占的内存空间大小约为 MxNx3 byte。

索引图是一种特殊的图 ，它的存在主要是为了节省空间，索引图附带了一个 color palette/table 或者叫 color map，对应了 256 种颜色（所以 color table 大小为 256x3）），然后图像像素每个位置值在 0-255 之间，数值代表该处像素在 color table 对应的颜色的索引值，实际展示该图片的时候，我们利用这个索引就能在 color table 中找到真正要展示的颜色。从这个描述可以看出，索引图一个像素只需要 1 比特，所以索引图占的空间大小约为 RGB 图的 1/3 ，大大减少了存储占用[^1]。

我们在 PIL 中可以轻松创建索引图，一个[改编例子](https://effbot.org/zone/creating-palette-images.htm)如下：

```python
from PIL import Image
from PIL import ImageDraw


im = Image.new("P", (400, 400), 0)
palette_color = im.getpalette()
print(palette_color)

im.putpalette([
    0, 0, 0,  # black background
    255, 0, 0,  # index 1 is red
    255, 255, 0,  # index 2 is yellow
    255, 153, 0,  # index 3 is orange
])

# It seems that the image palette is automatically filled with numbers to make
# it have size 256*3 (768), if you do not provide that much number to
# putpaletter() method.
palette_color = im.getpalette()
print(palette_color)

d = ImageDraw.ImageDraw(im)
d.polygon((0, 0, 0, 400, 400, 400), fill=1)
d.rectangle((100, 100, 300, 300), outline=2)
d.ellipse((120, 120, 280, 280), outline=3)

im.save("out.gif")
```

在 PIL 中，如果图像是一个索引图，我们可以使用  [`Image.getpalette()`](https://pillow.readthedocs.io/en/stable/reference/Image.html?highlight=putpalette#PIL.Image.Image.getpalette) 方法得到它的 color palette，该 palette 是一个 768 元素的 list，按照 `[r, g, b, r, g, b, ...]` 这种顺序排布。为了方便找到某个 index 对应的颜色，我们可以把它变形：

```python
palette_color = np.asarray(palette).reshape(256, 3)
```

# 参考

+ [What is the difference between images in 'P' and 'L' mode in PIL?](https://stackoverflow.com/q/52307290/6064933)
+ http://www.manifold.net/doc/mfd9/palette_images.htm

[^1]: 这只是一个粗糙计算，实际中还要考虑图像编码等其他因素。
