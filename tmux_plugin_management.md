---
title: "Tmux Plugin Install and Management"
date: 2019-01-17 22:03:16+0800
tags: [Tmux, Mac, CentOS]
categories: [Linux]
---

In this post, I want to share how to manage tmux plugins with Tmux plugin
manager (i.e., tpm) and also mention a few useful plugins.

<!--more-->

# Install the plugin manager

Like Vim, to add new tmux plugins, we can either manually install themes or
employ a plugin manager. [Tpm](https://github.com/tmux-plugins/tpm) is designed
for this purpose, which helps to manage your plugins automatically.

## Install tpm ##

First, we need to install tpm itself:

```bash
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

Add the following setting to your tmux configuration file `.tmux.conf`:

```tmux
# List of plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
# .... maybe more plugins here

# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run -b '~/.tmux/plugins/tpm/tpm'
```

Then you should refresh the tmux environment to make the change take effect:

```bash
tmux source ~/.tmux.conf
```

## Plugin management with tpm ##

The following operations assume that you are in a tmux session.

### plugin install ###

1. Add new plugin to `~/.tmux.conf` with `set -g @plugin '...'`

2. Press `<prefix> + I`[^1] (capital I) to fetch the plugin.

### plugin update ###

Press `<prefix> + U` to update existing plugins

### plugin uninstall ###

1. Delete or comment out the plugin in `.tmux.conf`

2. Press `<prefix> + alt + u` to remove the plugin


## Automatic install of tpm on new machines

According to doc [here](https://github.com/tmux-plugins/tpm/blob/master/docs/automatic_tpm_installation.md),
to automatically install tpm on a new machine, you can add the following
setting to your `.tmux.conf`:

```tmux
if "test ! -d ~/.tmux/plugins/tpm" \
   "run 'git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm && ~/.tmux/plugins/tpm/bin/install_plugins'"
```

# Plugin Install

## Themes

Themes are great. Here are a few themes you can try.

### Tmux theme pack

[Tmux theme pack](https://github.com/jimeh/tmux-themepack) is a collection of
themes.

Install it with tpm (add the following setting to `.tmux.conf`):

```tmux
set -g @plugin 'jimeh/tmux-themepack'
```

Pick a theme:

```tmux
set -g @themepack 'powerline/block/cyan'
```

### tmux-colors-solarized

[tmux-colors-solarized](https://github.com/seebi/tmux-colors-solarized) is a
solarized color theme for tmux.

Install it with tpm:

```
set -g @plugin 'seebi/tmux-colors-solarized'
```

Four themes are provided: `256`, `dark`, `light` and `base16`. You can choose
one via `.tmux.conf` option:

```tmux
set -g @colors-solarized '256'
# set -g @colors-solarized 'dark'
# set -g @colors-solarized 'light'
# set -g @colors-solarized 'base16'
```

## tmux-resurrect

[Tmux-resurrect](https://github.com/tmux-plugins/tmux-resurrect) let you easily
restore all your tmux session after a system restart so that you do not need to
do it manually.

Install it with tpm:

```tmux
set -g @plugin 'tmux-plugins/tmux-resurrect'
```

### key bindings

It provides two key bindings for save and restore your tmux environment:

+ Save: `<prefix> + Ctrl-s`
+ Restore: `<prefix> + Ctrl-r`

For more plugins, you can find them [here](https://github.com/rothgar/awesome-tmux) and
[here](https://github.com/tmux-plugins).

[^1]: `<prefix>` is your prefix key for tmux
