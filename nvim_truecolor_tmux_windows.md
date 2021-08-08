---
title: "Set up true color for nvim and tmux in Zoc terminal and xshell"
date: 2021-03-17 21:06:12+0800
tags: [Nvim, terminal, Tmux]
categories: [tools]
---

In my daily work, I often ssh to a remote server using a terminal emulator. My
local machine is a Windows 10 desktop. In this post, I want to share how to
make [true color](https://gist.github.com/XVilka/8346728) work across terminal emulator, nvim and tmux.

<!--more-->

# Pick a suitable terminal emulator

Not all terminal emulators support true colors. First, we need to use a
terminal emulator that supports true colors. Both [zoc terminal](https://www.emtec.com/zoc/) and [xshell](https://www.netsarang.com/en/xshell/) can
support this.

To test whether true color works or not, run the following script on the command line:

```bash
awk 'BEGIN{
    s="/\\/\\/\\/\\/\\"; s=s s s s s s s s;
    for (colnum = 0; colnum<77; colnum++) {
        r = 255-(colnum*255/76);
        g = (colnum*510/76);
        b = (colnum*255/76);
        if (g>255) g = 510-g;
        printf "\033[48;2;%d;%d;%dm", r,g,b;
        printf "\033[38;2;%d;%d;%dm", 255-r,255-g,255-b;
        printf "%s\033[0m", substr(s,colnum+1,1);
    }
    printf "\n";
}'
```

If you can see a smooth color band, then true color has been activated
successfully.

# Enable true color in terminal emulator

In zoc terminal, true color support is enabled by default. Previously, true
color support is missing in xshell. The latest version of xshell (xshell 7) has
added support for true color, but this feature is disabled by default.

To enable true color support in xshell, go to `Tools --> Options`, click the
`Advanced` Tab page, under `Terminal` section, check the box `Use true color`.
We need to restart xshell to activate this feature. Open a source code file
using nvim, you should see that true color is working properly.

# Make true color work with tmux and nvim

However, if we use nvim inside tmux, the colorscheme is still not right. In
order to make true color work inside tmux, we need a few more configuration.

## Set TERM environment variable

The correct way to set the `TERM` variable is via the terminal emulators.

For zoc terminal, go to `Options --> Edit Session Profile`. Click `Emulations`,
in the left, choose `Xterm` from the list of emulations. Check `TERM` box below,
and set it to `xterm-256color`. On the right, also check the box `256 color
support (TERM=xterm-256color)`.

For xshell, edit the properties of current session (`File --> Current Session
Properties`). Click the `Terminal` category, and change `Terminal Type` to
`xterm-256color`.

Restart the current connection. Run command `echo $TERM` to verify that `TERM`
varialbe has been correctly set to `xterm-256color`.

## Configure tmux

Open tmux config (in `$HOME/.tmux.conf`) and add the following settings:

```tmux
# True color settings, see https://jdhao.github.io/2018/10/19/tmux_nvim_true_color/ for the details.
set -g default-terminal "screen-256color"

# If terminal supports true color, use the following options.
set -ga terminal-overrides ",xterm-256color*:Tc"
```
After these two steps, kill tmux server and start a new tmux session:

```bash
tmux kill-server
tmux new -s "test"
```

Then start nvim, your colorscheme[^1] should work properly now.

[^1]:  A good colorscheme to check is [gruvbox](https://github.com/morhetz/gruvbox), which has great support for true color.
