---
title: "Notes on FFmpeg (1)"
date: 2021-04-16T00:33:01+08:00
draft: false
tags: [ffmpeg, video]
categories: [tools]
---

<!--more-->

# Install ffmpeg

If we have root rights, we can install ffmpeg via package manager. On Centos 7,
run the following command:

```bash
yum install epel-release
yum localinstall --nogpgcheck https://download1.rpmfusion.org/free/el/rpmfusion-free-release-7.noarch.rpm
yum install ffmpeg ffmpeg-devel
```

If you want to download binary releases, [this site](https://ffbinaries.com/downloads)
can help you. It has binary release for ffmpeg, ffprobe for different systems.
It seems that the provided ffmpeg is statically linked, so all you need is that
one file to get to work.

[This site](https://johnvansickle.com/ffmpeg/) alao provides binary release for ffmpeg.

Run command `ffmpeg -version` to verify that ffmpeg has been correctly
installed. You will see the following output:

```
ffmpeg version 3.4.8 Copyright (c) 2000-2020 the FFmpeg developers
built with gcc 4.8.5 (GCC) 20150623 (Red Hat 4.8.5-39)
configuration: --prefix=/usr --bindir=/usr/bin --datadir=/usr/share/ffmpeg
--docdir=/usr/share/doc/ffmpeg --incdir=/usr/include/ffmpeg --libdir=/usr/lib64
--mandir=/usr/share/man --arch=x86_64 --optflags='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2
-fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64
-mtune=generic' --extra-ldflags='-Wl,-z,relro ' --extra-cflags=' ' --enable-libopencore-amrnb
--enable-libopencore-amrwb --enable-libvo-amrwbenc --enable-version3 --enable-bzlib --disable-crystalhd
--enable-fontconfig --enable-gcrypt --enable-gnutls --enable-ladspa --enable-libass --enable-libbluray
--enable-libcdio --enable-libdrm --enable-indev=jack --enable-libfreetype --enable-libfribidi --enable-libgsm
--enable-libmp3lame --enable-nvenc --enable-openal --enable-opencl --enable-opengl --enable-libopenjpeg
--enable-libopus --disable-encoder=libopus --enable-libpulse --enable-librsvg --enable-libsoxr --enable-libspeex
--enable-libtheora --enable-libvorbis --enable-libv4l2 --enable-libvidstab --enable-libvpx --enable-libx264
--enable-libx265 --enable-libxvid --enable-libzvbi --enable-avfilter --enable-avresample --enable-libmodplug
--enable-postproc --enable-pthreads --disable-static --enable-shared --enable-gpl --disable-debug
--disable-stripping --shlibdir=/usr/lib64 --enable-libmfx --enable-runtime-cpudetect
libavutil      55. 78.100 / 55. 78.100
libavcodec     57.107.100 / 57.107.100
libavformat    57. 83.100 / 57. 83.100
libavdevice    57. 10.100 / 57. 10.100
libavfilter     6.107.100 /  6.107.100
libavresample   3.  7.  0 /  3.  7.  0
libswscale      4.  8.100 /  4.  8.100
libswresample   2.  9.100 /  2.  9.100
libpostproc    54.  7.100 / 54.  7.100
```

Ref:

+ https://linuxize.com/post/how-to-install-ffmpeg-on-centos-7/

# Suppress excessive logging

Every time I run ffmpeg command, its version, compilation configuration and
libary info is printed on the command line. This can be disabled using
`-hide_banner` option:

```
ffmpeg -hide_banner ...
```

We can also use `-loglevel` option to specify the verbosity of logging.

```
ffmpeg -loglevel warning ...
ffmpeg -loglevel info
```

The log level `quite` will silence any logging. In case you use `-loglevel`, it
is useful to specify `-stats` to print some running stats for ffmpeg.

Ref:

+ https://superuser.com/q/555289/736190
+ https://superuser.com/q/326629/736190

# Extract a frame every N seconds

How do we extract a frame from a video every 5 seconds?

```
ffmpeg -i input.mp4 -vf "fps=1/5" output_%3d.jpg
```

or

```
ffmpeg -i input.mp4 -r 0.2 output_%3d.jpg
```

Ref:

+ https://superuser.com/q/135117/736190
+ https://trac.ffmpeg.org/wiki/Create%20a%20thumbnail%20image%20every%20X%20seconds%20of%20the%20video
+ https://stackoverflow.com/q/51143100/6064933
+ https://superuser.com/q/584760/736190

# Extrac all frames from a video

If you do not provide the `-r` option, ffmpeg will extract all frame from the
video.

```bash
ffmpeg -i test.mp4 output/img_%4d.jpg
```

# Extract audio from video file

Run the following command to extract audio from a video file:

```
ffmpeg -i input.mp4 -vn -acodec copy output.aac
```

If we use this command, we need to know the audio format (i.e., acc, or other
formats). Use `ffprobe` to get the audio format. The output contains something
like the following:

```
Stream #0:1(und): Audio: aac (LC) (mp4a / 0x6134706D), 44100 Hz, stereo, fltp, 128 kb/s (default)
```

Other option to use to extract audio from video:

```
ffmpeg -i input.mp4 -map a output.mp3
```

or

```
ffmpeg -i input.mp4 -map a output.aac
```

Ref:

+ https://stackoverflow.com/q/9913032/6064933
