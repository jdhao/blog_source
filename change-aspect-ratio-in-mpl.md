---
title: "How to Set the Aspect Ratio of Image (or Axes) in Matplotlib?"
date: 2017-06-03 00:58:00 +0800
tags: [Matplotlib]
categories: [Python]
markup: pandoc
---

In Matplotlib, [`Axes`](https://matplotlib.org/api/axes_api.html) is the
primary place where we put plot elements, such as lines, texts and legends.
However, for a long time, I failed to grasp the meaning of aspect ratio in
Matplotlib, thus was constantly frustrated by the behavior of Maplotlib every
time I attempted to change the aspect ratio of a plot. This post is the result
of my attempt to understand it and my findings. Hope it can help you too.

<!--more-->

# A first unsuccessful try

Suppose I have the following code:

```python
import matplotlib.pyplot as plt
import numpy as np

plt.style.use('ggplot')
x = np.linspace(-5, 5, 100)
y1 = np.exp(0.5*x)
y2 = np.sin(x)

fig = plt.figure()
ax = fig.add_subplot(111)
ax.plot(x, y1)
ax.plot(x, y2)
plt.show()
```

The code snippet above produces the following image

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/original.png">
</p>

This image is a bit *thin*. What if I want the width of the output image to be
longer than its height, for example , an image with aspect ratio 0.6? According
to the [official documentation of Matplotlib](https://goo.gl/9IM9CC), we can
use [set_aspect](https://matplotlib.org/devdocs/api/_as_gen/matplotlib.axes.Axes.set_aspect.html)
method of `Axes` class to set aspect ratio of an Axes object. This method has a
parameter `aspect` which can be any positive number `num`.  The description for
`num` is rather vague:

> a circle will be stretched such that the height is num times the width.
> aspect=1 is the same as aspect='equal'.

After I add a statement

```python
ax.set_aspect(aspect=0.5)
```

in the above script, the rendered output image becomes

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/data_aspect_ratio_05.png">
</p>

Apparently, this is not what I want: the new output image is even thinner.  But
what has gone wrong? It took me a lot of efforts to find out.

# The meaning of aspect ratio in Matplotlib

Before we get into __*aspect ratio*__, we should first know that there are four
different [coordinate systems in
Matplotlib](https://matplotlib.org/users/transforms_tutorial.html), which you
are dealing with implicitly. Below is a brief description:

|Coordinate system | Description|
|:------|:------------------------------------------------|
|data|The userland data coordinate system, controlled by the xlim and ylim  |
|axes| The coordinate system of the Axes; (0,0) is bottom left of the axes, and (1,1) is top right of the axes. |
|figure| The coordinate system of the Figure; (0,0) is bottom left of the figure, and (1,1) is top right of the figure. |
|display| This is the pixel coordinate system of the display; (0,0) is the bottom left of the display, and (width, height) is the top right of the display in pixels.|

Usually, these coordinate systems will work under the hood and you can hardly
notice their existence. In our case, what we really want to set is the aspect
ratio in the display coordinate system, i.e., the physical length of axes
height divided by its width. But the aspect ratio in the `set_aspect()` method
refers to the aspect ratio in **data coordinate system**. For example, if the
aspect ratio equals 1, then in the display coordinate, the same length in data
coordinate have the same displayed length. See the following image for an
example (aspect=1):

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/data_aspect_ratio_010.png">
</p>

You can easily verify that for the same interval in data coordinate system in x
and y axis, they have the same length in display coordinate system.

# Transform between data and display coordinate

Now that we know this distinction, the issue boils down to calculating the
right aspect ratio to use in data coordinate system given the desired aspect
ratio in display coordinate system. Suppose the axes height and width of the
output plot are denoted as $disp_h$ and $disp_w$, then the desired display
aspect ratio is

$$disp_r=\frac{disp_h}{disp_w}\ .$$

If we denote the height and width in the data coordinate system as $data_h$ and
$data_w$, we have the following equation:

$$\frac{disp_w}{data_w}*aspect=\frac{disp_h}{data_h}\ .$$

Then the aspect we need to use in the `set_aspect()` method is

\begin{equation}\begin{aligned}
aspect & = \frac{data_w}{data_h}*\frac{disp_h}{disp_w}\\
       & =\frac{data_w}{data_h}*disp_r \\
       & = \frac{1}{data_r}*disp_r
\end{aligned}\end{equation}

$data_h$ and $data_w$  in the above equation can be easily calculated once we
get the x and y axis limit using [`get_xlim()`](https://goo.gl/FRFzIz) and
[`get_ylim()` ](https://goo.gl/t000Gi)methods of an Axes class object. Or we
can directly calculate the `dataRatio` using the
[`get_data_ratio()` method](https://matplotlib.org/devdocs/api/_as_gen/matplotlib.axes.Axes.get_data_ratio.html#matplotlib.axes.Axes.get_data_ratio)
of Axes class.

# The final plot

Now we are ready to set the display aspect ratio to whatever value we want
using the following code:

<details>
<summary><font size="2" color="red">Click to show the code.</font></summary>

```python
import matplotlib.pyplot as plt
import numpy as np

plt.style.use('ggplot')
x = np.linspace(-5, 5, 100)
y1 = np.exp(0.8*x)
y2 = np.sin(x)

fig = plt.figure()
ax = fig.add_subplot(111)
ax.plot(x, y1)
ax.plot(x, y2)

ratio = 0.3
xleft, xright = ax.get_xlim()
ybottom, ytop = ax.get_ylim()
# the abs method is used to make sure that all numbers are positive
# because x and y axis of an axes maybe inversed.
ax.set_aspect(abs((xright-xleft)/(ybottom-ytop))*ratio)

# or we can utilise the get_data_ratio method which is more concise
# ax.set_aspect(1.0/ax.get_data_ratio()*ratio)
plt.show()
```
</details>

The following images are plotted using the same data but with different display
aspect ratios.

![](https://blog-resource-1257868508.file.myqcloud.com/various_disp_ratio.png)
(red numbers in each plot denote their axes aspect ratio)

## When we have shared x axis

Usually, when we plot two subplots in a 1*2 layout, they have the same x axis
but different y axis limit. If we share their x axis, we must set the parameter
`adjustable` to "box-forced" in order to set the aspect ratios correctly. Below
is a valid example showing how to do this

<details>
<summary><font size="2" color="red">Click to show the code.</font></summary>

```python
import matplotlib.pyplot as plt
import numpy as np

y1 = np.random.uniform(0.1, 0.7, size=(167,))
y2 = np.random.uniform(1, 100, size=(167,))
y1 = sorted(y1)
y2 = sorted(y2)

fig = plt.figure(figsize=(10,3))
fig.set_edgecolor('red')
ax1 = fig.add_subplot(121)
ax2 = fig.add_subplot(122, sharex=ax1)

ax1.plot(y1)
ax1.set_ylim([min(y1)*0.9, max(y1)*1.1])
ax1.set_ylabel('y1')

ax2.plot(y2)
ax2.set_ylim([min(y2)*0.9, max(y2)*1.1])
ax2.set_ylabel('y2')

ratio = 0.4

for ax in [ax1, ax2]:
    xmin, xmax = ax.get_xlim()
    ymin, ymax = ax.get_ylim()
    print((xmax-xmin)/(ymax-ymin))
    ax.set_aspect(abs((xmax-xmin)/(ymax-ymin))*ratio, adjustable='box-forced')

plt.show()
```
</details>

The produced figure is

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/two_subplots_same_ratio.png">
</p>

The two subplots in the above figure have exactly the same display aspect
ratio.

OK, this is the end of this post, I hope that now you can finally under this
and set the desired image aspect ratio without any difficulty.

# References

+ <https://stackoverflow.com/a/14911939/6064933>
+ <https://stackoverflow.com/a/43806072/6064933>
+ <https://stackoverflow.com/q/18572234/6064933>
