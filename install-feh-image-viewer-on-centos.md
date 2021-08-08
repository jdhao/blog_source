---
title: Install feh Image Viewer from Source on CentOS 7
date: 2017-05-06 16:04:00 +0800
tags: [tools, CentOS]
categories: [Linux]
---

On my CentOS system, I usually use [eog](https://github.com/GNOME/eog) to view
images in the console. But I am a little upset by its speed of opening an
image. I do not need such functionality besides viewing an image. So I decided
to find another lightweight replacement.  <!--more-->

After searching the internet, I found a [post](https://goo.gl/FLreMn) on
stackexchange. According to the answers in the post, I decided to install [feh
](https://feh.finalrewind.org/) on my CentOS 7 system. The install process
turns out to be a little bumpy.

Feh is not in the official yum repo. So we have to build it from source.
Unfortunately, the [offical page](https://feh.finalrewind.org/) only gives
instructions on how to install it on Debian-based systems. What is worse, some
of the package names are different on CentOS. After some trial and error, I
succeeded in installing it on my system. Here is how.

# Install

First, we need to install some dependencies:

```
yum -y install libcurl-devel libX11-devel libXt-devel libXinerama-devel libpng-devel
```

Then we need to manually download other packages and install them:

```
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home:/Kenzy:/modified:/C7/CentOS_7/x86_64/imlib2-1.4.6-2.1.x86_64.rpm
wget ftp://ftp.pbone.net/mirror/ftp5.gwdg.de/pub/opensuse/repositories/home:/Kenzy:/modified:/C7/CentOS_7/x86_64/imlib2-devel-1.4.6-2.1.x86_64.rpm
wget https://jaist.dl.sourceforge.net/project/libjpeg-turbo/1.5.1/libjpeg-turbo-official-1.5.1.x86_64.rpm
yum --nogpgcheck localinstall imlib2-1.4.6-2.1.x86_64.rpm imlib2-devel-1.4.6-2.1.x86_64.rpm libjpeg-turbo-official-1.5.1.x86_64.rpm
```

After that, get the latest feh release from official github repo, compile and
install (make sure that you have root privilege, or you have to install it on
other directories which you have write access):

```
git clone git://github.com/derf/feh.git
cd feh
make -j${nproc} && make install
```

I have put all the command into a bash script, you can find it
[here](https://goo.gl/akuIVA). Simply run the script to install feh.

If nothing went wrong, you should be able to use feh right away.

# Usage

## Limit the window size to a specific size

We can use `-g` or `--geometry` option:

> -g, --geometry WxH[+X+Y]  Limit the window size to DIMENSION[+OFFSET]

For example, to show image with maximum window size of 400x500, use the
following command:

```
feh --geometry 400x500 /home/images/
```

## Keep the image viewing window to screen size

We can use `-.` or `--scale-down` to scale the image to fit the screen size:

```
feh --scale-down /home/images/
```

## Display the image info without showing the image

We can use `-l` or `--list` to show image info (it width, height, etc.) without
showing the image.

```
feh -l /home/images/
```

---
# References

+ feh command cheatsheet: https://github.com/derf/feh/blob/master/src/help.raw
+ feh man page: https://manpages.debian.org/testing/feh/feh.1.en.html



