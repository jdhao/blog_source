---
title: "How to Plot Only One Colorbar for Multiple Plot Using Matplotlib"
date: 2017-06-11
tags: [Matplotlib]
categories: [Python]
---

In some situations, we have several subplots and we want to use only one
colorbar for all the subplots. How to do this in Matplotlib?

<!--more-->


Two ways can be employed.

# The conventional method

The first method is like normal plotting: first draw the main plot, then add a
colorbar to the main plot. Matplotlib provide different ways to add a colorbar:
explicit or implicit way.

## The explicit way

The idea is to adjust the existing axes manually to make room for an additional
colorbar. Then explicitly add an axes where the colorbar resides. See the code
below for details:

```python
import matplotlib.pyplot as plt
import numpy as np

fig, axes = plt.subplots(nrows=2, ncols=3, figsize=(8.5, 5))

for ax in axes.flat:
    ax.set_axis_off()
    im = ax.imshow(np.random.random((16, 16)), cmap='viridis',
                   vmin=0, vmax=1)

fig.subplots_adjust(bottom=0.1, top=0.9, left=0.1, right=0.8,
                    wspace=0.02, hspace=0.02)

# add an axes, lower left corner in [0.83, 0.1] measured in figure coordinate with axes width 0.02 and height 0.8

cb_ax = fig.add_axes([0.83, 0.1, 0.02, 0.8])
cbar = fig.colorbar(im, cax=cb_ax)

 set the colorbar ticks and tick labels
cbar.set_ticks(np.arange(0, 1.1, 0.5))
cbar.set_ticklabels(['low', 'medium', 'high'])

plt.show()
```

In this way, we can control the position of  colorbar precisely. The output
image is like this:

<img style="float: middle;" src="https://blog-resource-1257868508.file.myqcloud.com/normal_plot_explicit.jpg">

## The implicit way

Matplotlib also offers method which can adjust the existing axes and make room
for a colorbar implicitly. See the code below for an example:

```python
import matplotlib.pyplot as plt
import numpy as np

fig, axes = plt.subplots(nrows=2, ncols=3, figsize=(8.5, 5))

for ax in axes.flat:
    ax.set_axis_off()
    im = ax.imshow(np.random.random((16, 16)), cmap='viridis',
                   vmin=0, vmax=1)

# notice that here we use ax param of figure.colorbar method instead of

# the cax param as the above example

cbar = fig.colorbar(im, ax=axes.ravel().tolist(), shrink=0.95)

cbar.set_ticks(np.arange(0, 1.1, 0.5))
cbar.set_ticklabels(['low', 'medium', 'high'])

plt.show()
```

In this way, you have to manually tweak the `shrink` param of `fig.colorbar`
method to make the main plot and the colorbar  appear the same height.  See the
output image below <img style="float: middle;"
src="https://blog-resource-1257868508.file.myqcloud.com/normal_plot_implicit.jpg">

Both the two methods have an disadvantage that  it is difficult to control the
padding space between subplots. You have to adjust the figure aspect ratio and
also the padding params to make the padding between the subplots appear the
same.  In fact, the padding in horizontal and vertical direction is not the
same for the above two plots even after tweaking.

# Using the axesgrid approach

Matplotlib also provides a [AxesGrid
toolkit](http://matplotlib.org/mpl_toolkits/axes_grid/users/overview.html) to
deal with padding  and colorbar issues arising from plotting multiple subplots.
By using axesgrid, the padding between subplots are guaranted to be the same.
Also the colorbar have exactly the same height as the main plot. Following is a
working example showing how to use axesgrid:

```python
import matplotlib.pyplot as plt
from mpl_toolkits.axes_grid1 import AxesGrid
import numpy as np

fig = plt.figure(figsize=(6, 4))

grid = AxesGrid(fig, 111,
                nrows_ncols=(2, 3),
                axes_pad=0.05,
                cbar_mode='single',
                cbar_location='right',
                cbar_pad=0.1
                )

for ax in grid:
    ax.set_axis_off()
    im = ax.imshow(np.random.random((16,16)), vmin=0, vmax=1)

# when cbar_mode is 'single', for ax in grid, ax.cax = grid.cbar_axes[0]

cbar = ax.cax.colorbar(im)
cbar = grid.cbar_axes[0].colorbar(im)

cbar.ax.set_yticks(np.arange(0, 1.1, 0.5))
cbar.ax.set_yticklabels(['low', 'medium', 'high'])
plt.show()
```

See the output image below.

<img style="float: middle;" src="https://blog-resource-1257868508.file.myqcloud.com/axesgrid_image.jpg">

You can see that the padding between subplots are all the same, also the
colorbar have the same height as the main plot.  Conveniently, isn't it?

# Summary

Using the normal way is more flexible but also annoying because you have to
adjust the paramters by trial and error. By employing the axesgrid, you can
simplify the plotting of multiple plot with just one colorbar, significantly.
In my opinion, the latter way is prefered.

# References

+ <https://stackoverflow.com/questions/13784201/matplotlib-2-subplots-1-colorbar>
+ <https://goo.gl/BT6Maz>
+ <https://goo.gl/lHF3Aa>
+ <https://matplotlib.org/examples/pylab_examples/subplots_adjust.html>
