---
title: "Tmux: Open Terminal Failed in Kitty Terminal"
date: 2020-06-14T18:04:06+08:00
tags: [terminal, Tmux]
categories: [Mac]
---

I have several terminal emulators installed on my Mac: [kitty](https://sw.kovidgoyal.net/kitty/),
[Alacritty](https://github.com/alacritty/alacritty) and [iterm2](https://www.iterm2.com/).
When I start a Tmux session in Alacritty and try to create another tmux session
in kitty, I noticed this strange error that I have never met before:

<!--more-->

> open terminal failed: missing or unsuitable terminal: xterm-kitty

For clarity, kitty seems to use its own [terminfo](https://tldp.org/HOWTO/Text-Terminal-HOWTO-16.html) file called `xterm-kitty`, and
Alacritty and iterm2 both use the good-old `xterm-256color`.

The author of kitty [claims that](https://github.com/kovidgoyal/kitty/issues/1241#issuecomment-568147090):

> tmux does not support multiple terminfo. You will need to kill all
existing tmux sessions and start a new one.

I find his claim to be partly true. If I create a session in Alacritty, I can
not attach to this session or create new session in kitty. However, if I
initially create a new session (before that, there is not tmux session running)
in kitty, I can indeed attach to that session or create a new session in
Alacritty.

My version info:

+ kitty: 0.17.2
+ Alacritty: 0.4.0
+ Tmux: 3.1b

So my temporary solution is to kill all sessions in other terminal emulators if
you want to use tmux in kitty.

# Tmux error when sshing to remote server

If you ssh to another server using kitty terminal and met this issue when
starting tmux on that server. The reason may be that `xterm-kitty` terminfo
file may not be available on that server. You can use the following command to
copy the terminfo file to the remote server as indicate
[here](https://sw.kovidgoyal.net/kitty/faq.html#i-get-errors-about-the-terminal-being-unknown-or-opening-the-terminal-failing-when-sshing-into-a-different-computer):

```
kitty +kitten ssh myserver
```

# References

+ [Tmux under kitty terminal](https://unix.stackexchange.com/a/470682/221410).
+ https://github.com/tmux/tmux/issues/1433
+ https://github.com/kovidgoyal/kitty/issues/370
