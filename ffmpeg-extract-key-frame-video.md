---
title: "How to Extract Key Frames from A Video with FFmpeg"
date: 2021-12-25T17:58:47+08:00
draft: false
tags: [ffmpeg, video]
categories: [tools]
---

In order to get enough information from videos, we often need to extract video frames.
However, we do not want to extract every frame from a video due to:
+ information redundancy in successive video frames.
+ huge storage space needed by these video frames.

In this post, I want to share how to extract *key* frames from video to reduce the storage,
but still capture enough information from a video.

<!--more-->

# Extract I-frame using ffmpeg

In video compression, they use the so-called [IPB frames](https://en.wikipedia.org/wiki/Video_compression_picture_types) to reduce storage space:

+ I frames (Intra picture): a complete picture
+ P frames (predictive picture): p frames stores difference between current frame and previous frame.
+ B frames (Bidirectionally predicted picture): b-frame stores difference between current frame and previous frame and later frame.

So we can extract I-frames only to get a fair amount of information from the video and reduce the extracted frame number.

To extract I-frames, we can run the following command:

```bash
ffmpeg -skip_frame nokey -i test.mp4 -vsync vfr -frame_pts true out-%02d.jpeg
```

+ `-vsync vfr`: discard the unused frames
+ `-frame_pts true`: use the frame index for image names, otherwise, the index starts from 1 and increments 1 each time

We can also use the filter syntax to extract keyframes:

```bash
ffmpeg -i test.mp4 -vf "select='eq(pict_type,I)'" -vsync vfr out-%02d.jpeg
```

The produced results from the two commands are the same.

# Extract scene-changing frames

If we only want to retain enough info from the video, extracting I-frames only may not be enough.
The extracted key frames may still exhibit too much information redundancy.
For example, if you have a slow-changing video, the difference between a frame and its subsequent frames will be negligible.
To further reduce the number of images generated,
we can also use [scene](http://www.ffmpeg.org/ffmpeg-filters.html#select_002c-aselect) filter to select frames that are likely to be a scene-changing frame.

It works like this:

```bash
ffmpeg -i input.mp4 -vf "select='gt(scene,0.4)'" -vsync vfr frame-%2d.jpg
```

The value for `scene` is between 0 and 1, which measures the pixel level difference between current frame and previous frame.
We can tweak this value to generate different number of output images.
Intuitively, the smaller the value, the larger number of images we will extract.
Typically, a value of 0.3 or 0.4 may be enough.

In order to extract suitable number of frames from the video, for videos with fast-changing frames,
we should set this value high, and for videos with mostly still frames,
we should set this value low (maybe 0.1 or even less depending on the video content).

# References

+ How to extract I frames:
     + https://superuser.com/a/1421195/736190
     + https://superuser.com/a/669733/736190
+ How to extract scene-changing frames: https://stackoverflow.com/q/35675529/6064933
