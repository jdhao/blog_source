---
title: "Kitty Terminal Config on Mac"
date: 2020-05-05T20:52:40+08:00
tags: [terminal]
categories: [Mac]
---

<details>
<summary><font size="2" color="red">Changelog</font></summary>

+ <font color="blue">2021-08-21: Update the part on how to open URLs since some options are removed.</font>

</details>

[Kitty](https://github.com/kovidgoyal/kitty) is a fast and lightweight terminal
emulator for Mac and Linux. In this post, I will summarize some of its
settings.

<!--more-->

We can download the kitty binary release from its [GitHub release
page](https://github.com/kovidgoyal/kitty/releases). After installation, its
config file is located at `$HOME/.config/kitty/kitty.conf`.

# How to copy and paste in Kitty terminal?

By default, to copy selected text to clipboard in Kitty, use the shortcut <kbd>
Ctrl</kbd>+ <kbd>Shift</kbd> + <kbd>C</kbd>. To paste selected text, we can use
mouse middle click.

We can also edit the Kitty config and add more shortcut for copy-pasting:

```
map cmd+c        copy_to_clipboard
map cmd+v        paste_from_clipboard
map shift+insert paste_from_clipboard
```

Most terminal emulators have this nice feature to copy the selected text to
clipboard automatically. In Kitty, we can also do this by enabling the
following option:

```
copy_on_select yes
```

# How to open URLs?

Iterm2 has a feature to open a URL in default browser if you press <kbd>
Ctrl</kbd> and click the URL link. In kitty, you can press <kbd>Ctrl</kbd> +
<kbd>Shift</kbd> and then click the left mouse to open a URL.

If you are using the lastest version of Kitty (tested on kitty 0.23), you can
also add the following kitty config:

```
mouse_map ctrl+left press ungrabbed,grabbed mouse_click_url
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

+ Offical on kitty conf: https://sw.kovidgoyal.net/kitty/overview/#configuring-kitty
+ [Copy text on select](https://github.com/kovidgoyal/kitty/issues/125).
+ [Copy and paste in Kitty](https://unix.stackexchange.com/q/500072/221410).
+ [Use option as meta key](https://github.com/kovidgoyal/kitty/issues/123#issuecomment-342702282).
