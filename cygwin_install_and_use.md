---
title: "Execellent Development Experience with Cygwin and Mintty Terminal on Windows 10"
date: 2018-10-23 15:00:00 +0800
tags: [Windows, ssh, mintty, Cygwin]
categories: [tools]
---

Linux is great for programming compared to Windows. If you miss the experience
you get on Linux, maybe you should try [Cygwin](https://www.cygwin.com/), which
set up a Linux-like environment on Windows platform. In this post, I want to
talk about how to configure Cygwin and how to use mintty terminal on Windows 10
to get excellent programming experience.

<!--more-->

# How to install new packages?

There is no official way to install a new package. Just run the Cygwin setup
tool one more time and select the package you want to install. It will not
rewrite the package you have installed, only the new package will be installed.

If the package download speed is slow, you can choose a local mirror for
Cygwin. In China, [TUNA](https://mirror.tuna.tsinghua.edu.cn/help/cygwin/) is
good.

## Reference

+ <https://superuser.com/a/304543/736190>

# When I use ssh to connect to my server, I got an error message "Pseudo-terminal will not be allocated because stdin is not a terminal"

On Windows 10, there is a built-in ssh program under
`C:/Windows/System32/OpenSSH/ssh.exe`. You are probably using this program. If
that is the case, you have to install `openssh` yourself. Just run the Cygwin
setup program one more time to install the additional package. Then this issue
should disappear.

## Reference
+ <https://stackoverflow.com/a/18091711/6064933>

# Mintty settings

Cygwin use the popular [mintty](https://mintty.github.io/) terminal as its
terminal emulator. In this part, I document several settings related to mintty.

## How do I set up the mintty terminal emulator type, i.e., `TERM` variable?

Right-click Cygwin window title and choose `Options...`, go to `Terminal`, and
choose Type to be `xterm-256color`. That is all.


## How to use an external color theme for mintty?

By default, mintty provides a few color themes. The color theme for mintty are
just a list of settings for different colors. There are several places where
you can find mintty themes. A non-exhaustive list is:

+ https://github.com/oumu/mintty-color-schemes
+ A python tool with several built-in color themes, https://github.com/joakimkarlsson/mintty-colors
+ [How do I change colors on-the-fly in mintty?](https://superuser.com/q/223198/736190)

Save the color theme file under `$CYGWIN_ROOT/usr/share/mintty/themes`. Open
mintty options (right click on Cygwin title bar), go to `Looks` pane and choose
from the `Theme` pull-down menu a color theme you want to use.

Alternatively, create a `.minttyrc` file under your HOME. Paste the content of
color theme in this file and save it. The next time you open Cygwin, mintty
will automatically use the configurations in `.minttyrc`.

### References

+ https://github.com/mintty/mintty/wiki/Tips#using-colour-schemes-themes

## How do I configure mintty to use the settings from a config file and what are the available options?

As I have said, create a `.minttyrc` under your HOME, and put mintty settings
in this file. To see all the options available, visit [this page](https://mintty.github.io/mintty.1.html#CONFIGURATION).

## How to open URL in browser

In the iTerm terminal, we can click a URL to open it in the browser, which is
convenient. Actually mintty also supports this feature.

But sometimes, <kbd>Ctrl</kbd>+`click` won't work properly. It has something to
do with the mouse mode of various programs. When mouse mode is enabled for
Neovim and Tmux, to open a URL in browser, I have to use
<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+`click` . If the mouse mode is turned off, I
can open a URL with just <kbd>Ctrl</kbd>+`click`.

### References

+ <https://github.com/mintty/mintty/issues/694>
+ <https://github.com/mintty/mintty/issues/164>

## How to update mintty?

It is the same as you install or update other packages. Run `setup_x86_64.exe`
and install the latest version of Mintty.
