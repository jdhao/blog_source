---
title: "Vim-like Editing inside Browser"
date: 2019-05-11 12:14:06+0800
tags: [Vim, Nvim]
categories: [tools]
---

Vim/Nvim has powerful ability in editing texts. But if you are in a browser and
want to input some text, can we somehow utilize the editing power of Vim? In
this post, I would like to share several ways to use Vim or Vim-like editing
when you are working inside a browser.

<!--more-->

# GhostText (★★★)

There is also a browser plugin called
[GhostText](https://github.com/GhostText/GhostText) which lets you write in the
text area using your favorite editor. It supports several popular editors such
as Atom, VS Code and Neovim.

For Neovim, you need to install [vim-ghost](https://github.com/raghur/vim-ghost).

## How to use

+ Open nvim-qt and run `:GhostStart` to start the server
+ Put your cursor in text area in the browser and click the GhostText plugin. A
temp file will be opened in nvim-qt.
+ Start editing the file and the text will be synced to the text ares in your
browser in real time.
+ `:bd!` will stop the server and delete the buffer.

## Shortcomings

Current vim-ghost has no way to customize the extensions of the temp file
created, thus limiting its usefulness. The biggest disadvantage is that you
need to perform two steps in order to actually start typing texts.

# Text Editor Anywhere (★★★☆)

[Text Editor Anywhere](https://www.listary.com/text-editor-anywhere) is a tool
to invoke the editors on your system to edit the text areas in your browser.
You can add custom file extensions. When you activate this tool, it lets you
select the file extension to use and create a temp file of that extension so
you can utilize the full power of the various plugins you have installed.

Currently, it is only available on the Windows platform.

# Surfingkeys (★★★★)

[Surfingkeys](https://github.com/brookhong/Surfingkeys) is a Chrome plugin
which serves the same purpose as [Vimium](https://github.com/philc/vimium), if
you know what that is. Basically, it lets you use your browser mouseless by
providing Vim-like key bindings for various operations you may perform inside
the browser.

Surfingkeys can open up [a Vim
editor](https://github.com/brookhong/Surfingkeys#vim-editor-and-emacs-editor)
for various text areas inside the browser. You can activate the editor using
<kbd>Ctrl</kbd>+ <kbd>i</kbd>. After you have finished writing, use
<kbd>Ctrl</kbd> + <kbd>Enter</kbd> (in insert mode) or <kbd>Enter</kbd> (in
normal mode) to write the text.

# wasavi (★★★★)

From the [doc of wasavi](http://appsweets.net/wasavi/):

> wasavi is an extension for Chrome, Opera and Firefox. wasavi transforms
TEXTAREA element of any page into a VI editor, so you can edit the text in VI.
wasavi supports almost all VI commands and some ex commands.

So you can see that wasavi is pretty powerful.

![wasavi](https://blog-resource-1257868508.file.myqcloud.com/20190513233309.png)

You can press <kbd>Ctrl</kbd> + <kbd>Enter</kbd> to activate wasavi when the
cursor is focused in a text area. Then you can use almost all your familiar Vim
command inside it. To write the text into the text area, use `ZZ` or `:wq` as
you would probably do in normal Vim.

# Firenvim (★★★★★) #

Since Neovim support external UIs. This year (2019), another project named
[firenvim](https://github.com/glacambre/firenvim) starts to build an external
UI in the textarea of web browsers, which is really amazing. So what does that
really mean? It means that you can use your favorite Nvim plugins inside the
browser without any compromise. It is a true neovim running inside the browser!

Check the firenvim homepage on how to install it for FireFox or Chrome. You can
also check
[here](https://jdhao.github.io/2020/01/01/firenvim_nvim_inside_browser/) on how
to install firenvim on different platforms.

# Conclusion

In this post, I introduced several tools to help you input text using Vim-like
command or actually using Vim/Nvim. If you work mostly on the Windows platform,
Text Editor Anywhere is also a good choice since it can directly invoke the
editor installed on your computer. If you work cross platform and want powerful
editing ability of Nvim without compromise, then firenvim will definitely be
your choice.

# Update

+ 2020-01-01: Add description about firenvim.

# References

+ https://news.ycombinator.com/item?id=11023428
