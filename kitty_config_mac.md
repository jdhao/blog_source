---
title: "Kitty Terminal Config on Mac"
date: 2020-05-05T20:52:40+08:00
tags: [terminal]
categories: [Mac]
---

[Kitty](https://github.com/kovidgoyal/kitty) is a fast and lightweight terminal
emulator for Mac and Linux. In this post, I will summarize some of its
settings.

<!--more-->

We can download the kitty binary release from its [GitHub release
page](https://github.com/kovidgoyal/kitty/releases). After installation, its
config file is located at `$HOME/.config/kitty/kitty.conf`.

# How to copy and paste in Kitty terminal?

By default, to copy selected text to clipboard in Kitty, we can use the
shortcut <kbd>Ctrl</kbd>+ <kbd>Shift</kbd> + <kbd>C</kbd>. To paste the
selected text, we can use the mouse middle click.

We can also edit the Kitty config file to add more copy and paste shortcut:

```
map cmd+c        copy_to_clipboard
map cmd+v        paste_from_clipboard
map shift+insert paste_from_clipboard
```

Most terminal emulators have this nice feature to copy the selected text to
clipboard by default. In Kitty, we can also do this. Just enable the following
option:

```
copy_on_select yes
```

# How to open URLs?

Iterm2 has the feature to open a URL in default browser if you press
<kbd>Ctrl</kbd> and click the URL link. In Kitty, we can achieve the same
behavior with the following settings:

```
# show single underline when hovering the mouse over URL
url_style single
open_url_modifiers ctrl
```

# Tab configuration

By default, Kitty will show tab when tab number is above 1. The tab will be
shown at the bottom of Kitty window. To change this behaviour, use the
following setting:

```
tab_bar_edge top
```

# Use option as alt key #

By default, `Option + LETTER` will input the corresponding Unicode characters
on macOS. So any shortcut starting with <kbd>Option</kbd> can not work
properly. To use <kbd>Option</kbd> as <kbd>Alt</kbd>, use the following config:

```
macos_option_as_alt yes
```

More documentation [here](https://sw.kovidgoyal.net/kitty/conf.html#opt-kitty.macos_option_as_alt).

# How to change color schemes?

Kitty uses a list of colors for setting the color scheme. Unlike iterm2, we
need to manually change the color config inside `kitty.conf`.

We can find [a lot of color schemes or themes](https://github.com/search?q=kitty+theme)
for Kitty on GitHub. To use these color themes, just copy the color definition
to your Kitty config.

[This repo](https://github.com/dexpota/kitty-themes/tree/master/themes) also
has a lot of Kitty themes for you to choose.

# References #

+ [Copy text on select](https://github.com/kovidgoyal/kitty/issues/125).
+ [Copy and paste in Kitty](https://unix.stackexchange.com/q/500072/221410).
+ [Use option as meta key](https://github.com/kovidgoyal/kitty/issues/123#issuecomment-342702282).
