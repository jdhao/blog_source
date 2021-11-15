---
title: "PyAV for video processing"
date: 2021-11-04T22:17:51+08:00
draft: false
tags: [ffmpeg,pyav,video]
categories: [Python]
---

[ffmpeg](https://www.ffmpeg.org/) is an excellent tool for video processing.
However, using ffmpeg directly inside Python is not convenient enough.
Previously, I have been using [ffmpeg-python](https://github.com/kkroening/ffmpeg-python), which is a thin wrapper around the `ffmpeg` command line executable.

<!--more-->

The main issue with ffmpeg-python is its slow speed in performance-critical applications,
due to its nature as a simple wrapper package.

The [PyAV](https://github.com/PyAV-Org/PyAV) is a more performant package providing ffmpeg library bindings[^1].

To install PyAV, run the following command:

```
pip install av
```

# Simple use case: extract frame every one second

Here is a code snippet for how to extract video frames every one second:

```python
import os

import av


out_dir = "demo"
if not os.path.exist(out_dir):
    os.makedirs(out_dir)

fpath = "test.mp4"
container = av.open(fpath)

# take first video stream
stream = container.streams.video[0]

# get video fps
average_fps = int(stream.average_rate)

for idx, frame in enumerate(container.decode(stream)):
    if idx % average_fps != 0:
        continue

    frame.to_image.save("frame-{idx}.jpg")
```

In the code snippet, we create a [container](https://pyav.org/docs/stable/api/container.html#input-containers), which contains all video and audio streams in the video.

We then use `container.streams.video[0]` to get the video stream.
`container.decode()` is used to decode the stream into frames.

Since videos may have variable fps, there are actually [several frame rates](https://pyav.org/docs/develop/api/stream.html#frame-rates).
`stream.average_rate` is the average fps for video, and it works fine in our case.
However, it is not a native type, we need to convert it to float or int before usage.

Some of the other important information of a stream:

+ video duration in seconds: `float(stream.duration * stream.time_base)`
+ video frame number: `stream.frames`
+ frame width and height: `frame.width`, `frame.height`

# References

+ video frames doc: https://pyav.org/docs/stable/api/video.html#module-av.video.frame

[^1]: It works by invoking the ffmpeg libraries directly, instead of using `ffmpeg` executable.
