---
title: "How to Change Intersection Point of X and Y Axis in Matplotlib"
date: 2018-05-21 11:02:09 +0800
tags: [Matplotlib]
categories: [Python]
---

In this post, I will share how to position the intersection of x and y axis at a specific point using Matplotlib.

<!-- more -->

# The spines

In order to re-position x and y axis, we need to understand an important concept in Matplotlib ---[spines](https://matplotlib.org/api/spines_api.html#module-matplotlib.spines).
According to Matplotlib documentation:

> Spines are the lines connecting the axis tick marks and noting the boundaries
> of the data area. They can be placed at arbitrary positions. See
> function:set_position for more information.

In normal plot, we can clearly see four spines around the plot.
Like the following plot:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-21/96489184.jpg" width="400">
</p>

Two of the 4 spines, i.e., bottom and left spines are used as x and y axis.
Another two spines are used to indicate data boundary.

# Move the spines

In order to move a specific spine, we use the [set_position()](https://matplotlib.org/api/spines_api.html#matplotlib.spines.Spine.set_position) method of spines.
This method accepts a 2-tuple as its parameter.
The first element of the tuple is used to indicate the desired coordinate system[^1], i.e., `axes` or `data`.
If you use `axes`, the value for the second element is in the range $[0.0, 1.0]$.
If you choose `data`, the value for the second element is the range of actual data range.

We can use `ax.spines` to get the four spines.
The returned object will be a dict, whose keys are `bottom`, `left`, `top`, `right`.
Then we use one of these keys to access a specific spine and customize it.

## Position spines in data coordinate

Let us take a concrete example.
Suppose we want to put the intersection of x and y axis at data coordinate $(0, 0)$, we can use the following snippet:

```python
ax.spines['left'].set_position(('data', 0))
ax.spines['bottom'].set_position(('data', 0))
```

The produced image will be:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-21/69572175.jpg" width="400">
</p>

The image looks a bit ugly since top and right spines now seem redundant.
How do we remove them? It is easy:

```python
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
```

Now the image will be prettier:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-21/59113942.jpg" width="400">
</p>

You can move the spines to whatever position you want, see more example below:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-21/67109868.jpg">
</p>

## Position spines in axes coordinate

We can also position spines using the axes coordinate system.
For example, if you want to place the x and y axis in the top right of the axes,
i.e., in the axes coordinate $(1, 1)$, use the following script:

```python
ax.spines['left'].set_position(('axes', 1))
ax.spines['bottom'].set_position(('axes', 1))
```

The produced plot is

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-21/24171757.jpg" width="600">
</p>

# References

+ <https://www.labri.fr/perso/nrougier/teaching/matplotlib/>
+ [How to draw axis in the middle of the figure?](https://stackoverflow.com/q/31556446/6064933)

[^1]: If you are not familiar with coordinate system in Matplotlib, you can check [this tutorial](https://matplotlib.org/users/transforms_tutorial.html#transformations-tutorial).
