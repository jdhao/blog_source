---
title: "Copy from Remote Server to Local Clipboard via OSC 52 in Neovim"
date: 2021-01-05T22:55:51+08:00
draft: false
tags: [mintty]
categories: [Nvim]
---

In my daily work, I usually log into a remote server via a terminal emulator
and do my development work there. Sometimes, I need to copy text from remote
server to my local machine's clipboard. Previously, I use mouse to select and
copy the text, which I find to be quite cumbersome.

<!--more-->

# What is OSC 52

Recently, I have found out the [OSC 52 terminal sequence](https://github.com/microsoft/terminal/issues/2946)[^1],
which is really useful when we want to copy text from remote to local
clipboard.

We can use OSC 52 sequence to tell terminal emulator that we want to put some
text into the clipboard of local machine. The actual text is base64 encoded. If
the terminal emulator supports it, then it can decode the text and put it into
the system clipboard.

# Set up OSC 52 support in Mintty terminal

On Windows, [mintty terminal](https://mintty.github.io/) as old as [version 2.6.1](https://github.com/mintty/mintty/issues/258#issuecomment-248059240)
supports this feature. Mintty config is in directory `C:
\Users\Administrator\AppData\Roaming\mintty`, and the name is `config`. Open
the file and add the following option:

```
AllowSetSelection=yes
```

Restart mintty and this feature will take effect. To verify, log into the
remote server using mintty terminal, and run the following command:

```bash
printf "\033]52;c;$(printf "%s" "hello" | base64)\a"
```

Now your local clipboard content should become "hello".

# Set up OSC 52 support in ZOC terminal

[ZOC terminal](https://www.emtec.com/zoc/) has added support for OSC-52 terminal sequence in version 8.01.0,
as indicated by [its change log](https://www.emtec.com/downloads/zoc/zoc_changes.txt).

To enable this feature, go to `Options --> Edit Session Profile`,  in the
`Advanced` section, we need to activate the following option:

>  Activate control sequences that enable remote hosts to execute commands locally.

Currently, osc-52 in ZOC only works for pure ASCII characters and does not work
for non-ASCII characters. The dev said that it will be fixed in the next
release.

# Copy text inside Neovim to local clipboard

With terminal's support for OSC 52, we can copy text from Neovim to our local
clipboard directly.

Install [vim-oscyank](https://github.com/ojroques/vim-oscyank):

```vim
Plug 'ojroques/vim-oscyank'
```

Select a text region and use command `OSCYank` to copy the text to your local
clipboard.

# Refs

+ https://medium.com/free-code-camp/tmux-in-practice-integration-with-system-clipboard-bcd72c62ff7b
+ https://mintty.github.io/mintty.1.html#CONFIGURATION
+ https://github.com/mintty/mintty/issues/258#issuecomment-246123745
+ https://chromium.googlesource.com/apps/libapps/+/master/hterm/etc/osc52.vim

[^1]: **OSC** stands for Operating System Commands, see https://en.wikipedia.org/wiki/ANSI_escape_code#OSC
