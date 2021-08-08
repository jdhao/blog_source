---
title: "File Sharing between Android and Mac Wirelessly without Pain"
date: 2018-10-21T15:01:03+08:00
tags: []
categories: [Mac, Android]
---

If you connect your Android device to a Macbook, you will notice that your
phone is not recognized. Unlike Windows, Apple's Mac is not so friendly to
Android devices. Of course, you can install [Android File Transfer](https://www.android.com/filetransfer/)
provided by Google and connect your phone to Mac. But it is kinda cumbersome
and does work so well. In this post, I want to share several ways to transfer
files between Android and Mac wirelessly.

<!--more-->

I have tested the three different methods listed below on my Mac and Android.
For your reference, the device info is:

+ Mac: Macbook Pro 2018 with the latest macOS Mojave (version 10.14)
+ Phone: OnePlus 3T with Android 8.0.0

# Bluetooth

In order to share files between Android and Mac using Bluetooth, you need to
set up Mac first. Open the system preference setting and open the *Sharing*
panel. Turn on the *Bluetooth Sharing* service and set up the destination
folder properly. Then you can share files via Bluetooth easily.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181021232821.png" width="600">
</p>

## Pros and Cons

The advantage of this method is that you do not need to install any additional
software since Bluetooth is the standard utility across different OS. The
disadvantage is also obvious: the file transfer speed is slow. I have observed
that the transfer speed is about 100kb/s. For large files, it will take a lot
of time. Unless you are always transferring small files, I would not recommend
using Bluetooth.

# KDE connect and Soduto

From the [KDE connect GitHub repo](https://github.com/KDE/kdeconnect-kde):

> KDE Connect is a multi-platform app that allows your devices to communicate (eg: your phone and your computer).

First, you need to install [kdeconnect for Android](https://play.google.com/store/apps/details?id=org.kde.kdeconnect_tp)
from the Google Play store. There is no official package for Mac. Fortunately,
there is a software called [Soduto](https://soduto.com/) which is like KDE
connect. So you can install Soduto on Mac instead.

After installing these applications, you should make sure that your Mac and
Android device is connected to the same wireless router. Open kdeconnect on
your phone, you should be able to see your Macbook automatically like the image
below.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/Screenshot_20181021-235828__01.jpg" width="400">
</p>

Tap the Macbook device and let your phone pair with it. Now, you can browse
files on your phone easily and share files freely.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181022001240.png" width="400">
</p>

With kdeconnect, you can not only share files, but also share the clipboard
between your Android phone and Mac. When you copy text on one device, it will
be automatically synced to another device. Amazing, isn't it? The transfer
speed is also high.

The disadvantage is that you need to install an application both on your phone
and on your Mac.

# MiXplorer

The third way is to install [MiXplorer](https://labs.xda-developers.com/store/app/com.mixplorer) file
manager on your phone. MiXplore is a light-weight yet powerful file manager.
With it, you can create FTP server or http servers on your phone, which your
Mac can later connect to.

## Create a HTTP server

Open MiXplorer and click top-right button and click *servers...* menu, you will
be prompted to choose a server type. Choose *Start HTTP Server*.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181022003712.png" width="400">
</p>

You will see a notification on your notification bar like the following:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181022003729.png" width="400">
</p>


Open the HTTP address in your browser, and you should be able to see all the
files on your phone. Click a file to download it to your Mac[^1]. To upload
files to your phone, first choose a file and then hit the *upload* button in
the interface.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181022004433.png" width="800">
</p>

The transfer speed is high and the process is stable.

## Create a FTP server

Like the way to create a HTTP server, we can also create a FTP server on our
phone.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181022010247.png" width="400">
</p>

After starting the FTP server, we need to connect to it to share files. To
connect to the FTP server. We need a FTP client. You may try the free
software such as [cyberbuck](https://cyberduck.io/) or [filezilla](https://filezilla-project.org/).
Let's take filezilla for an example. Install this application, open it and set
up the FTP server, you will see an interface like the image below:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181022011133.png" width="800">
</p>

The file transfer speed is also quite high.

# Conclusion

In this post, I introduced three ways to share files between Android device and
Mac wirelessly. Overall, I recommend the second (using Soduto) and third way
(using MiXplorer), since these two methods can all guarantee high transfer
speed. If you need to exchange texts, URLs and other small text snippet between
Android and Mac frequently, maybe Soduto suits you better.

# References

+ [Set up Bluetooth sharing on Mac](https://apple.stackexchange.com/q/170864/285595)

[^1]: This may not work on Apple Safari. On Chrome, it works.
