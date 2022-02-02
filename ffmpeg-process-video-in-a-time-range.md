---
title: "Process video in a certain time range with FFmpeg?"
date: 2021-12-16T00:34:16+08:00
draft: false
tags: [ffmpeg]
categories: [tools]
---

When dealing with videos, we may want to process video in a specific time range,
for example, to only process video from 50 seconds to 70 seconds. How do we
specify the time range in ffmpeg?

<!--more-->

To specify the start position, use `-ss` option. This option can be used as an
input option (before `-i`) or output option (after `-i`). When used as an input
option, ffmpeg will directly seek the video file to that position. When used as
an output option, ffmpeg will still decode frames before that position, but
discards those frames. So using `-ss` as an input option may accelerate the
processing speed dramatically.

To specify the end position, we can use `-to` option[^1]. Another way is to use
`-t` to specify a time duration.

The format for time can be found [here](https://ffmpeg.org/ffmpeg-utils.html#Time-duration):

```
There are two accepted syntaxes for expressing time duration.

[-][HH:]MM:SS[.m...]

HH  expresses the number of hours,  MM  the number of minutes for a maximum of 2 digits, and  SS  the number of seconds for a maximum of 2 digits. The  m  at the end expresses decimal value for  SS.

_or_

[-]S+[.m...][s|ms|us]

S  expresses the number of seconds, with the optional decimal part  m. The optional literal suffixes ‘s’, ‘ms’ or ‘us’ indicate to interpret the value as seconds, milliseconds or microseconds, respectively.

In both expressions, the optional ‘-’ indicates negative duration.
```

So to extract frame from 50 seconds to 70 seconds, we can use either of the
following commands:

```bash
ffmpeg -ss 00:00:50 -to 00:01:10 -i input.mp4 -r 1 out-%2d.jpg
ffmpeg -ss 00:00:50 -t 20 -i input.mp4 -r 1 out-%2d.jpg
ffmpeg -ss 50 -t 20 -i input.mp4 -r 1 out-%2d.jpg
```

# References

+ ffmpeg seeking: https://trac.ffmpeg.org/wiki/Seeking
+ ffmpeg does not recognize `-to` as input option
    + https://askubuntu.com/q/1181319/768311
    + https://stackoverflow.com/q/66876561/6064933

[^1]:  **Note that use `-to` as an input option, you need to use ffmpeg v4.0+.**
