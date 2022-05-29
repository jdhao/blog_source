---
title: "Bash Emacs-style Shortcut Cheatsheet"
date: 2020-10-26T22:44:21+08:00
draft: false
tags: [Bash]
categories: [Linux]
---

In Bash shell, when editting in the command line.
There are two different modes, [vi mode or Emacs mode](https://unix.stackexchange.com/q/85390/221410). Emacs-mode is the default mode.
Below are some of the handy shortcuts in Emacs-mode.

<!--more-->

# Shortcuts

+ `ALT-b` and `ALT-f`: go backward(toward begining of the line)/forward (toward end of the line) a word. Note that `-` is a word separator in this case.
+ `Ctrl-D`: delete one character to the right of the cursor.
+ `Ctrl-A` and `Ctrl-E`: go to begining/end of the line.
+ `Ctrl-K` and `Ctrl-U`: delete till the end/begining of the line.
+ `Ctrl-R`: search history command. Press `Enter` to run a command, or press movement key to edit it.
+ `ALT-D`: Delete from current cursor position to the end of the word.
+ `Ctrl-_`: Undo edit.
+ `ALT-R`: Revert command to original.
+ `Ctrl-W`: Delete a word backward. Note that here word is separate by space.
So if we have `python util/test.py|` (`|` indicate cursor) , pressing `Ctrl-W` leave us with only `python `. `-` is not a word separator in this case.
A little inconsistent, IMHO.

# Refs

+ https://askubuntu.com/a/269048/768311
+ https://mehmandarov.com/navigating-and-editing-the-command-line/
+ https://superuser.com/q/705807/736190`
+ http://teohm.com/blog/shortcuts-to-move-faster-in-bash-command-line/
+ https://lornajane.net/posts/2011/navigating-bash-history-with-ctrlr
