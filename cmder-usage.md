---
title: "Cmder --- The Ultimate Command Line Tool for Windows"
date: 2017-12-29 20:04:00 +0800
tags: [Cmder, Unicode, encoding, Windows, terminal]
categories: [tools]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-1/79824514.jpg"
         title="A glimpse of Cmder">
</p>

<!--more-->

# Intro to Cmder

As a Windows user, now and then, I have to use the Windows CMD to run some
simple command. Compared to its Linux counterpart (bash, for example), the
Windows CMD is totally disaster: it does not support smart TAB completion, it
does not support pipes and it has a dumb UI... Fortunately, there is a tool
called [cmder](http://cmder.net/) which tries to enhance our experience of
using consoles on Windows systems. It has a decent UI with different color
themes, support for Linux-style tab completion and more... It is far better
than windows CMD.

# Installation

Cmder is a combination of several good tools:
[Conemu](https://conemu.github.io/), [clink](https://mridgers.github.io/clink/)
and [git for Windows](https://git-for-windows.github.io/). In order to use its
full functionality, it is better to install the full version. After download,
just extract the zip package into your program folder. Then you can launch cmder
using launch tools such as [Listary](https://jdhao.github.io/2017/03/01/Listary_intro/).

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/17-12-29/30167064.jpg"
         title="Launch cmder using Listary">
</p>

# How to Use

## Start cmder in a particular directory

Sometimes, we want to start cmder in current directory. We can configure this
easily:
1. Open the cmder executable as an Administrator
2. Go the directory where cmder is extracted, e.g., `D:\Program Files\cmder`
3. Execute `.\cmder.exe /REGISTER ALL` command

Then go to a directory and right click, you will see a contextual menu option
`Cmder Here` (See image below).

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/17-12-29/81815647.jpg"
         title="Adding cmder to contextual menu"
         >
</p>

## keyboard shortcut

|  Shortcut | Description  |
|---|---|
| <kbd>Ctrl</kbd>+<kbd>T</kbd> or <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>1</kbd>  | start a new console tab  |
| <kbd>Ctrl</kbd>+<kbd>W</kbd>  |  close a console tab |
| <kbd>Ctrl</kbd>+<kbd>R</kbd>  | search command history  |
| <kbd>Win</kbd>+<kbd>Alt</kbd>+<kbd>P</kbd>| open settings page |

Fore more keyboard shortcut, see
[here](https://github.com/cmderdev/cmder#keyboard-shortcuts).

## UTF-8 decoding related issue

In order to show Chinese characters properly, we need to set up console to use
UTF-8 decoding. Also, the font used should support Chinese characters. First we
need to set up the font options, as shown below:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/17-12-29/49776200.jpg"
         title="Setting for English and Chinese characters">
</p>

### Show directories with Chinese characters in their names

In its default setting, cmder can not display directories with Chinese
characters in their names as shown below.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/17-12-29/98979673.jpg"
         title="Directory names are not properly shown">
</p>

This can be easily fixed. Open the setting page, and go to `Startup ->
Environment`, in the text box, use the following setting:

~~~
set LANG=zh_CN.utf8
~~~

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/17-12-29/7774197.jpg"
         title="Environemtn settings">
</p>

### Error message containing Chinese characters

When we are executing some command, the error message from the command may
contains Chinese characters, which are not displayed properly:

>$ pandoc --pdf-engine=xelatex -V CJKmainfont=KaiTi test.md -o test.pdf
>Error producing PDF.
>! Undefined control sequence.
>l.67 ...锟斤拷搴︽湁闂锛屽簲璇ユ妸\textwidth鎹㈡垚

This can be fixed by adding `chcp utf-8` or `chcp 65001` in the environment
settings (see the above setting image). After applying this change, restart
cmder and rerun the command, we find that the error message is correctly
displayed now:

>$ pandoc --pdf-engine=xelatex -V CJKmainfont=KaiTi test.md -o test.pdf
>Error producing PDF.
>! Undefined control sequence.
>l.67 ...��度有问题，应该把\textwidth换成

## Spurious character is inserted in command prompt when we press up arrow

When we press up arrow "↑" to bring up the history command, then delete the
command, we may find that spurious character is inserted at command prompt.
This issue is explained more thoroughly [here](https://github.com/cmderdev/cmder/issues/1351),
[here](https://github.com/cmderdev/cmder/issues/553) and [here](https://github.com/cmderdev/cmder/issues/506).

In order to fix it, go to the cmder installation folder, the edit the file
`vendor\clink.lua`.

### For older versions of Cmder

Replace the following snippet

~~~lua
if env == nil then
    lambda = "λ"
else
    lambda = "("..env..") λ"
end
~~~

with

~~~lua
if env == nil then
    lambda = "$"
else
    lambda = "("..env..") $"
end
~~~

### For latest versions of Cmder

For the latest version of cmder (version 1.3.8), it seems that this old issue
still persists. The `clink.lua` file have changed. Find the line

```lua
local lambda = "λ"
```

and replace it with

```lua
local lambda = "$"
```

Restart cmder and the problem should disappear.

# References

+ [Solving the issue of incorrectly displayed Chinese in error message](https://github.com/cmderdev/cmder/issues/1339)
+ [How to display Chinese characters correctly in command output](https://github.com/cmderdev/cmder/issues/985)
+ [How to display Chinese characters correctly in command output, another post](https://github.com/cmderdev/cmder/issues/918)
+ [Unicode support of cmder](https://conemu.github.io/en/UnicodeSupport.html).
