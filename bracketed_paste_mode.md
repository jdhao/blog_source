---
title: "Bracketed Paste Mode in Terminal"
date: 2021-02-01T23:34:02+08:00
draft: false
tags: [terminal, Nvim, Vim]
categories: [tools]
---

A note on bracketed paste mode in terminal and Neovim.

<!--more-->

# Annoying character inserted when pasting text

I use [ZOC terminal](https://www.emtec.com/zoc/) to connect to my remote
server. Today, I noticed a strange issue when I copied text from elsewhere to
the terminal shell command line. When I copied some text and pasted it in the
terminal, I found that `0~` was added in the beginning of text, and `1~` was
added in the end of text.

I found [a similar issue](https://unix.stackexchange.com/q/196098/221410) on
stackexchange. It turns out that this has something to do with [bracketed paste
mode](https://cirw.in/blog/bracketed-paste) in terminal emulators.

# What is bracketed paste mode

Typically, when we paste code snippet into a terminal application, it can not
tell whether we are typing the text or we are pasting it. This may have
unintended side affects for some application. For example, Vim will auto-indent
the code pasted into it when it sees a newline, breaking the code structure.

Here comes the bracketed paste mode. When this feature is enabled, if we paste
text into terminal, the terminal will add some special sequence in the
beginning and end of the text (`\e[200~` in the beginning and `\e[201~` in the
end).

## How enable and disale it in terminal

To check if bracketed paste mode is on and supported, first use `printf "\e[?2004h"`
to turn on it. Then copy some text, run `xxd`, and paste some text into
terminal. If you terminal supports bracketed paste mode, then you will see
output like the following:

```
^[[200~some test^[[201~
```

To disable this mode, use `printf '\e[?2004l'`.

## Why is it important?

But how is this even important? If a terminal program also supports bracketed
paste mode, it can interpret the special terminal sequence and know that the
user is pasting text instead of typing it.

A noticeable example is when we paste multi-line code into Vim. Since the code
have already been properly indented, we do not want Vim to alter it. Without
this terminal ability, Vim does not know whether you are typing the text or
paste it from the terminal. So when it meets a newline character, it will try
to re-indent the following line, causing a complete mess. If Vim supports
bracketed paste mode, when it see the special terminal sequence, it knows that
the user is trying to paste text into it. It will not auto-indent your pasted
text any more, thus you do not need to use `set paste` in order to paste
something.

# Enable bracketed paste mode in Vim/Neovim

For plain Vim, see [this post](https://vi.stackexchange.com/q/25311/15292) on
how to enable bracketed paste mode. If you are using Neovim, bracketed paste
mode in built-in, if your terminal supports it, you do not need to do any setup.
Neovim will just work, see also `:h bracketed-paste-mode` inside Neovim.

# Ref

+ https://github.com/ChrisJohnsen/tmux-MacOSX-pasteboard/issues/31
+ [Getting strange characters when pasting into my iterm2 terminal](https://stackoverflow.com/q/44848979/6064933)
+ [How do I disable the weird characters from “bracketed paste mode” on the Mac OS X default terminal?](https://stackoverflow.com/a/50654284/6064933)
+ https://news.ycombinator.com/item?id=18329305
