---
title: "Ubuntu on Windows Missing after Windows Update"
date: 2019-10-11 22:55:01+0800
tags: [WSL, mintty, Ubuntu]
categories: [Note]
---

I have been using Ubuntu on Windows via WSL for about a year and it works quite
well.

The terminal I use for Ubuntu on Windows is
[wsltty](https://github.com/mintty/wsltty), which is a thin wrapper around
[mintty](https://github.com/mintty/mintty) and other tools for starting WSL
sessions. Recently, when I start clicking the WSL Terminal icon, it start a
Window and then quit silently.

<!--more-->

By looking at the property of "WSL Terminal" icon, I find that the default
command that wsltty uses to start the Ubuntu terminal is:

```
%LOCALAPPDATA%\wsltty\bin\mintty.exe --WSL= --configdir="%APPDATA%\wsltty" -~
```

In order to find possible error message, I did a little search. I find that we
can add the `-h always` option to force the terminal window to stay open even
if there are some error in the process. The whole command is:

```
%LOCALAPPDATA%\wsltty\bin\mintty.exe --WSL= --configdir="%APPDATA%\wsltty" -~ -h always
```

The error message I saw is like the following:

```
wslbridge error: failed to start backend process
note: bash.exe output: Windows Subsystem for Linux has no installed distributions.
Distributions can be installed by visiting the Microsoft Store:
https://aka.ms/wslstore
```

What?? I have apparently installed a WSL distribution. In order to confirm
that, I find from [here](https://github.com/Microsoft/WSL/issues/2578) that the
location for a WSL distribution is something like the following:

```
C:\Users\%USERNAME%\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState\rootfs\
```

This is the directory where the root of the Ubuntu for Windows resides. I
opened the similar folder on my Windows and found that the `rootfs` folder is
indeed gone!

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20191011230510.png">
</p>

Further dig reveals that [the culprit is the Windows
update](https://github.com/microsoft/WSL/issues/4063), which has wiped out my
WSL distribution somehow.

After knowing the causes, the solution is simple: just re-install a WSL
distribution or just dump WSL and use [Cygwin](https://cygwin.com/)!

# References #

+ [Keep the wsltty window open even if there are erros](https://github.com/mintty/wsltty/issues/156#issuecomment-467867300).
