---
title: "How To Read and Write ply File in Python"
date: 2022-05-26T13:26:11+08:00
markup: markdown
tags: [3D]
categories: [Python]
---

In this post, I want to share how to generate [ply files](https://en.wikipedia.org/wiki/PLY_(file_format)) in Python.

<!--more-->

# Open3D

We can use [open3d](https://github.com/isl-org/Open3D). We can use pip to install it:

```python
pip install open3d
```

For CentOs 7, I met error when installing the latest version:

>  /lib64/libc.so.6: version `GLIBC_2.18' not found

A workaround is to use older versions:

```
pip install open3d==0.9.0
```

See also issue [here](https://github.com/isl-org/Open3D/issues/2893) for more discussions.

Below is is a simple snippet showing to read and write ply files using open3d.

```python
import numpy as np
import open3d as o3d

def main():
    pts = np.random.randint(0, 100, (100, 3))

    # whether to write in binary or text format
    write_text = True

    # use open3d
    use_o3d(pts, write_text)


def use_o3d(pts, write_text):
    pcd = o3d.geometry.PointCloud()

    # the method Vector3dVector() will convert numpy array of shape (n, 3) to Open3D format.
    # see http://www.open3d.org/docs/release/python_api/open3d.utility.Vector3dVector.html#open3d.utility.Vector3dVector
    pcd.points = o3d.utility.Vector3dVector(pts)

    # http://www.open3d.org/docs/release/python_api/open3d.io.write_point_cloud.html#open3d.io.write_point_cloud
    o3d.io.write_point_cloud("my_pts.ply", pcd, write_ascii=write_text)

    # read ply file
    pcd = o3d.io.read_point_cloud('my_pts.ply')

    # visualize
    # o3d.visualization.draw_geometries([pcd])
```


Summary: powerful features and good documentation.

Ref:

+ Create ply using open3d
    + https://stackoverflow.com/a/62989523/6064933
    + https://stackoverflow.com/q/71233749/6064933


# PyntCloud

We can also use [PyntCloud](https://github.com/daavoo/pyntcloud):

```
pip install pyntcloud
```

Unlike open3d, for Pyntcloud, we need to convert Numpy array to Pandas data frames.
Here is an example how to use Pyntcloud:

```python
import pandas as pd
from pyntcloud import PyntCloud


def use_pyntcloud(pts, write_text):
    # ref: https://pyntcloud.readthedocs.io/en/latest/io.html
    # the doc is scarce and not complete

    n = len(pts)

    # The points must be written as a dataframe,
    # ref: https://stackoverflow.com/q/70304087/6064933
    data = {'x': pts[:, 0],
            'y': pts[:, 1],
            'z': pts[:, 2],
            'red': np.random.rand(n),
            'blue': np.random.rand(n),
            'green': np.random.rand(n)
            }

    # build a cloud
    cloud = PyntCloud(pd.DataFrame(data))

    # the argument for writing ply file can be found in
    # https://github.com/daavoo/pyntcloud/blob/7dcf5441c3b9cec5bbbfb0c71be32728d74666fe/pyntcloud/io/ply.py#L173
    cloud.to_file('my_pts2.ply', as_text=write_text)

    # read file
    cloud = PyntCloud.from_file('my_pts2.ply')
    # print(cloud)
```

Summary: the documentation is terrible and lacking.

# Python-plyfile

Another package is called [Python-plyfile](https://github.com/dranjan/python-plyfile) (`pip install plyfile`).
Here is how to use it:

```python
from plyfile import PlyData, PlyElement


# use python-plyfile
use_plyfile(pts, write_text)


def use_plyfile(pts, write_text):
    x, y, z = pts[:, 0], pts[:, 1], pts[:, 2]
    pts = list(zip(x, y, z))

    # the vertex are required to a 1-d list
    vertex = np.array(pts, dtype=[('x', 'f4'), ('y', 'f4'), ('z', 'f4')])

    el = PlyElement.describe(vertex, 'vertex')
    PlyData([el], text=write_text).write('my_pts3.ply')
```

Summay: Not intuitive API design, at least for writing a ply file.

# Meshio

[Meshio](https://github.com/nschloe/meshio) (`pip install meshio`) can also do this:

```python
import numpy as np
import meshio

vertices = np.random.rand(100, 3)
meshio.write("test.ply", mesh=meshio.Mesh(points=vertices, cells = []), binary=False)
```

Summary: no doc, hard to use. I need to check source code to know its parameters.

# References

+ Python plyfile vs pymesh: https://stackoverflow.com/q/36920562/6064933
