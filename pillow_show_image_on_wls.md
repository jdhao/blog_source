---
title: "Display Image with Pillow inside Ubuntu on Windows"
date: 2019-02-16 11:42:55+0800
tags: [PIL, Windows, Python, X11, WSL, Ubuntu]
categories: [Note]
---

With the help of [Windows Subsystem for Linux (WSL, for
short)](https://docs.microsoft.com/en-us/windows/wsl/about)[^1], we can use the
*nearly full* Linux environment on Windows system. In this post, I would like
to share how to display images inside Ubuntu on Windows, which is one of the
popular distrubtions.

<!--more-->

If you try to show an image with Pillow using the following code inside WSL:

```python
from PIL import Image

im = Image.open("test.jpg")

im.show()
```

you will find that nothing happens if you run the above code. The reason is
two-fold:

1. There is no valid image viewer on your Ubuntu on Windows (Pillow will use
   the system default image viewer, which is
   [Imagemagick](https://www.imagemagick.org/) on Linux)
2. There is no X server by which you can use GUI applications.

After knowing the reasons, the solution is simple. First, we need to install
Imagemagick for Ubuntu on Windows (**not on Windows 10 itself, this is
important!**):

```bash
sudo apt install imagemagick
```

Then we need to install a X server on the Windows system, which will serve your
GUI applications. A good option for Windows is
[Vcxsrv](https://sourceforge.net/projects/vcxsrv/). You can install it manually
or with the help of [Chocolatey](https://sourceforge.net/projects/vcxsrv/):

```
choco install vcxsrv
```

After install, run it on your system. Inside Ubuntu on Windows, you should also
set the `DISPALY` environment variable in your `.bashrc`:

```
echo "export DISPLAY=localhost:0.0" >> ~/.bashrc && source ~/.bashrc
```

Run your Python code again and you should be able to see the image displayed
properly on your screen.


# References

- [Image.show() can not display image](https://stackoverflow.com/questions/16279441/image-show-wont-display-the-picture)
- [Install X server and set up for WSL](https://askubuntu.com/questions/823352/windows-10-bash-cannot-connect-to-display)
- [`DISPLAY` environment variable](https://askubuntu.com/questions/432255/what-is-the-display-environment-variable)

[^1]: See [here](https://docs.microsoft.com/en-us/windows/wsl/install-win10) on how to install a Linux distribution with WSL enabled.
