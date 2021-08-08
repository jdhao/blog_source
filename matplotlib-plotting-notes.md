---
title: "Matplotlib Plotting Notes -- Series 1"
date: 2017-11-16 21:41:00 +0800
tags: [Matplotlib]
categories: [Python]
---

This post records my collected notes on various tricks in using Matplotlib.

<!--more-->

# Twin axes in the same plot with one legend

Often times, we need to make two axes which share the same x axis, but with
different scale of y axis. Fortunately, Matplotlib's Axes class has a
[`twinx()`](https://matplotlib.org/api/_as_gen/matplotlib.axes.Axes.twinx.html#matplotlib.axes.Axes.twinx)
which fulfills our need. But another question arises: how to make the two axes
share one legend?

If we have two axes and use their
[`legend()`](https://matplotlib.org/api/_as_gen/matplotlib.axes.Axes.legend.html#matplotlib-axes-axes-legend)
method seperately. The figure will have two separate legends, which is a bit
redundant. In order to use one legend to show the labels of lines in both axes,
we need to collect all the lines as well as their corresponding labels.

## Two different ways to achieve what we want

We can use two slightly different ways to achieve this. First, you can try the
following:

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 100)
y1 = 2*x
y2 = np.sqrt(x)
y3 = np.exp(0.5*x)

fig = plt.figure()
ax1 = fig.add_subplot(111)
line1 = ax1.plot(x, y1, 'r', label="2x")
line2 = ax1.plot(x, y2, 'g', label="sqrt(x)")

ax2 = ax1.twinx()
line3 = ax2.plot(x, y3, 'b', label="exp(0.5x)")
lines = line1+line2+line3
labels = [l.get_label() for l in lines]
ax2.legend(lines, labels)
plt.show()
```

Or you can try the second method:

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 100)
y1 = 2*x
y2 = np.sqrt(x)
y3 = np.exp(0.5*x)

fig = plt.figure()
ax1 = fig.add_subplot(111)
ax1.plot(x, y1, 'r', label="2x")
ax1.plot(x, y2, 'g', label="sqrt(x)")
ax2 = ax1.twinx()
ax2.plot(x, y3, 'b', label="exp(0.5x)")

handles, labels = [], []
for ax in [ax1, ax2]:
    h, l = ax.get_legend_handles_labels()
    handles.extend(h)
    labels.extend(l)
ax2.legend(handles, labels)

plt.show()
```

Both will produce the same output plot as shown below,

<img src="https://blog-resource-1257868508.file.myqcloud.com/twin_axes_one_legend.jpg"
         title="twin axes with one legend"
         style="float: middle;"
         >

## A side note

Also, what is worth notice is that in first snippet, we use
`line1+line2+line3`. That is possible because the three lines are actually
Python `list`, which is in turn because [Matplotlib's `plot()` method return a
list of lines even if you plot just one
line](https://stackoverflow.com/questions/11983024/matplotlib-legends-not-working).

# Log-scale plot with correct ticks and tick labels

Sometimes, we want to plot in log scale. This is easy to achieve in Matplotlib.
We can use the normal plotting command and then [set the x axis to log
scale](https://matplotlib.org/api/_as_gen/matplotlib.axes.Axes.set_xscale.html#matplotlib.axes.Axes.set_xscale).
Or we can directly plot in log scale using [semi-log
method](https://matplotlib.org/api/_as_gen/matplotlib.axes.Axes.semilogx.html#matplotlib.axes.Axes.semilogx).
A simple snippet the shown below,

```python
import matplotlib.pyplot as plt
import numpy as np
import matplotlib

colors = ["#e6194b",
          "#3cb44b",
          "#ffe119"]

m1 = np.array([ 0.6125, 0.775, 0.8375, 0.875, 0.9125, 0.9875])
m2 = np.array([0.6750, 0.8125, 0.8625, 0.9000, 0.9375, 0.9750])
m3 = np.array([0.8000, 0.8625, 0.9250, 0.9625, 0.9625, 0.9750])

position = [1, 2, 4, 8, 16, 32]
fig, ax = plt.subplots()

ax.semilogx(position, m1*100, linestyle='-', color=colors[0])
ax.semilogx(position, m2*100, linestyle='-', color=colors[1])
ax.semilogx(position, m3*100, linestyle='-', color=colors[2])

ax.set_xticks(position)
ax.grid(linestyle='--')
ax.set_xlabel("K")
ax.set_ylabel("Recall@k score (%)")

plt.show()
```

The above snippet will produce the following figure,

<img src="https://blog-resource-1257868508.file.myqcloud.com/17-12-23/40170211.jpg"
         title="log scale image"
         style="float: middle;"
         >

Notice that in the above figure, some minor ticks appear in the plot and also
the tick labels are not properly shown.

We can use [tick_params()
method](https://matplotlib.org/api/_as_gen/matplotlib.axes.Axes.tick_params.html#matplotlib.axes.Axes.tick_params)
of axes and [minorticks_off()
method](https://matplotlib.org/api/_as_gen/matplotlib.axes.Axes.minorticks_off.html#matplotlib.axes.Axes.minorticks_off)
to turn off the axis minor ticks. As for the im-properly displayed ticks in x
axis, we can use
[set_major_formatter](https://matplotlib.org/api/_as_gen/matplotlib.axis.XAxis.set_major_formatter.html#matplotlib-axis-xaxis-set-major-formatter)
to correct it. A work example is shown below,

```python
import matplotlib.pyplot as plt
import numpy as np
import matplotlib

colors = ["#e6194b",
          "#3cb44b",
          "#ffe119"]

m1 = np.array([ 0.6125, 0.775, 0.8375, 0.875, 0.9125, 0.9875])
m2 = np.array([0.6750, 0.8125, 0.8625, 0.9000, 0.9375, 0.9750])
m3 = np.array([0.8000, 0.8625, 0.9250, 0.9625, 0.9625, 0.9750])

position = [1, 2, 4, 8, 16, 32]
fig, ax = plt.subplots()

ax.semilogx(position, m1*100, linestyle='-', color=colors[0])
ax.semilogx(position, m2*100, linestyle='-', color=colors[1])
ax.semilogx(position, m3*100, linestyle='-', color=colors[2])

ax.set_xticks(position)
ax.get_xaxis().set_major_formatter(matplotlib.ticker.ScalarFormatter())

# use the following method to turn off minor ticks

ax.tick_params(axis='x', which='minor', bottom='off')
# ax.minorticks_off()  # or use minorticks_off method

ax.grid(linestyle='--')
ax.set_xlabel("K")
ax.set_ylabel("Recall@k score (%)")

plt.show()
```

The new plot generate is shown below,

<img src="https://blog-resource-1257868508.file.myqcloud.com/17-12-23/57603299.jpg"
         title="log scale plot without minor ticks"
         style="float: middle;"
         >

We can see that all the issues of the first-version plot have been corrected.

## Change line width in the legend

Sometimes, we want to increase the line width in the legend for better
readability. The following snippet shows how to do this in Matplotlib using the
object oriented API:

```python
import numpy as np
import matplotlib.pyplot as plt

# make some data

x = np.linspace(0, 2*np.pi)
y1 = np.sin(x)
y2 = np.cos(x)

fig, ax = plt.subplots()
ax.plot(x, y1, linewidth=1.0, label='sin(x)')
ax.plot(x, y2, linewidth=1.0, label='cos(x)')

leg = ax.legend()

for line in leg.get_lines():
    line.set_linewidth(4.0)

plt.show()
```

The generated image is shown below,

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-18/61060969.jpg"
     title="Change linewidth in legend"
     style="float: middle;">


# References

+ [Discussions on Stack Overflow](https://stackoverflow.com/questions/5484922/secondary-axis-with-twinx-how-to-add-to-legend).
+ [plot log scale, post in SO](https://stackoverflow.com/questions/14530113/set-ticks-with-logarithmic-scale).
+ [Increase line width in legend](https://stackoverflow.com/questions/9706845/increase-the-linewidth-of-the-legend-lines-in-matplotlib)
