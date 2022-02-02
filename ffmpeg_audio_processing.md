---
title: "Audio Processing Using FFmpeg"
date: 2021-11-10T00:24:09+08:00
draft: false
tags: [audio,ffmpeg]
categories: [tools]
---

Collected tips on audio processing with FFmpeg.

<!--more-->

# Remove audio from a video

To remove audio from a video file, i.e., muting the video, run the following
command:

```bash
ffmpeg -i input.mp4 -c copy -an input-silent.mp4
```

Ref:

+ https://superuser.com/q/268985/736190

# Extract audio from FFmpeg

```bash
ffmpeg -i input.mp4 -vn -acodec copy out.acc
```

+ `-vn`: do not use video
+ `-acodec copy`: copy the audio stream (equivalent to `-codec:a copy`, see [stream specifier](https://ffmpeg.org/ffmpeg.html#toc-Stream-specifiers-1))


# Extract audio in a time range

```bash
ffmpeg -i input.mp4 -vn -acodec copy -ss 00:01:00 -t 30 out.aac
```

+ `-ss`: the start time to process the video, in above case, we start from 1 minute
+ `-t`: the video duration to process, in this case, we process 30 seconds of video.

There is also a `-to` option which specifies the stop time to process the video. So the above command can also be written as:

```bash
ffmpeg -i input.mp4 -vn -acodec copy -ss 00:01:00 -to 00:01:30 out.aac
```

Ref:

+ https://stackoverflow.com/q/55668539/6064933
+ https://stackoverflow.com/q/9913032/6064933
+ [ffmpeg video duration format](https://ffmpeg.org/ffmpeg-utils.html#Time-duration)
