---
title: "Nvim-qt Settings on Windows 10"
date: 2019-01-17 22:04:05+0800
tags: [Windows, font, 字体]
categories: [Nvim]
---

To run Neovim on Windows, you can either [use the terminal
Neovim](https://jdhao.github.io/2018/11/15/neovim_configuration_windows/) or
use a GUI for Neovim. If you use terminal Nvim, you may encounter various
annoying issues. Based on my experience, I recommend you using a GUI client for
Nvim instead of the terminal one.

<!--more-->

[Nvim-qt](https://github.com/equalsraf/neovim-qt) is one of the [many GUI front
end](https://github.com/neovim/neovim/wiki/Related-projects#gui) for Neovim,
and it is packaged with Neovim by default on Windows platform. In this post, I
will give a summary on how to solve a few issues with
[Nvim-qt](https://github.com/equalsraf/neovim-qt).

# General settings #

## Where should I put configurations related to nvim-qt?

Nvim-qt will use your Nvim configurations as well as a GUI configuration
file[^1]. The GUI config file is named `ginit.vim`, and you should put it under
the same directory as `init.vim`. The directory is something like
`C:\Users\Administrator\AppData\Local\nvim` on Windows. If you are inside
Neovim, you can also use the command `:echo stdpath('config')` to show the
exact directory.

## How do I pass options to neovim when starting nvim-qt on the command line?

Since nvim-qt has its own options, it will get confused if you simply provide
it with nvim startup options. You need to specify nvim options after `--`. For
example, to [open a file using specific
configuration](https://www.mankier.com/1/nvim#-u), use the following command:

```
nvim-qt some_file.txt -- -u init.vim
```

## Shift+Insert does not work in nvim-qt ##

In Nvim-qt, when we press <kbd>Shift</kbd>+<kbd>Insert</kbd> in insert mode, it
will add a literal `<S-Insert>`, instead of placing the text on the system
clipboard to the cursor position. To fix this issue, we can use the following
mapping:

```vim
inoremap <silent>  <S-Insert>  <C-R>+
```

# GUI settings

Nvim-qt has also provided a list of its own command to configure its behaviors.
I list some of the settings in the following sections.

## How to change the font used?

You can change the font Nvim-qt uses by default. To check the default font
used, use `GuiFont` command without argument inside nvim-qt. On my system, the
output is `Consolas:h11`.

According to [nvim-qt documentation](https://github.com/equalsraf/neovim-qt/blob/master/src/gui/runtime/doc/nvim_gui_shim.txt),
the following attributes for fonts are available:

```
hXX - height is XX in points
b   - bold weight
l   - light weight
i   - italic
```

You can chain different attribute with `:` character. For example, to use font
[Hack](https://sourcefoundry.org/hack/) in 10 point and light weight, use the
following command:

```
:GuiFont Hack:h10:l
```

After issuing the above command, you may see the following warning message:

> Warning: Font "Hack" reports bad fixed pitch metrics.

To suppress this message, use the `bang` version of `GuiFont` command instead:

```
:GuiFont! Hack:h10:l
```

## How to turn off the GUI tabline?

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190117221204.png">
</p>

The GUI tabline of nvim-qt is ugly. We can use `GuiTabline 0` inside
`ginit.vim` to disable GUI tabline and use the TUI tabline.

Although we have set `GuiTabline 0` in `ginit.vim`, but it seems that it is
still not disabled completely. If you open up a file with nvim-qt, the GUI
tabline appears and then disappears quickly.

To disable it completely, i.e., not showing at all, there are two ways
currently. One way is to use the `--no-ext-tabline`  option when invoking
nvim-qt:

```
nvim-qt --no-ext-tabline
```

The second way is to edit the Windows registry for nvim-qt. Go to
`Computer\HKEY_CURRENT_USER\Software\nvim-qt\nvim-qt` and add a new `String
Value`. The Name field should be `ext_tabline` and the Data field should be
`false`. Then the GUI tabline should disappear completely.

## How to turn off GUI completion menu?

The GUI completion menu is also ugly and too long, since it shows the detailed
docstrings of object methods (see image below).

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190117221305.png" width="400">
</p>

You can disable it by adding the following setting in `ginit.vim`:

```vim
GuiPopupmenu 0
```

## How to reduce line space?

You can use command `GuiLinespace` to control the line space in nvim-qt, for
example, `GuiLinespace 1`.

## How to open nvim-qt with maximized window?

Add the following settings to your `ginit.vim` file:

```
call GuiWindowMaximized(1)
```

## How to change cursor color in nvim-qt?

According to [this issue](https://github.com/equalsraf/neovim-qt/issues/599),
currently, it is not possible to change the cursor position color in nvim-qt.
Nvim-qt just sets the cursor to the reverse of background color.

**My complete configurations for Neovim can be found in [my GitHub
repo](https://github.com/jdhao/nvim-config). For How to configure Neovim for
Python development, see
[this post](https://jdhao.github.io/2018/12/24/centos_nvim_install_use_guide_en/).**

# References

+ [nvim-qt does not recognize neovim options?](https://github.com/equalsraf/neovim-qt/issues/125)
+ [nvim-qt and nvim options](https://github.com/equalsraf/neovim-qt/issues/449)
+ [Disable GUI tabline](https://github.com/equalsraf/neovim-qt/issues/361)
+ [Suppress font warning in nvim-qt](https://github.com/equalsraf/neovim-qt/issues/164)
+ [Suppress font warning in nvim-qt](https://github.com/equalsraf/neovim-gui-shim/issues/1)
+ [Nvim-qt documentation](https://github.com/equalsraf/neovim-qt/blob/master/src/gui/runtime/doc/nvim_gui_shim.txt)
+ [Open nvim-qt with maximized window](https://github.com/equalsraf/neovim-qt/issues/83#issuecomment-211997944)
+ [How to disable GUI tabline completely?](https://github.com/equalsraf/neovim-qt/issues/589)

[^1]: See also `:h ginit.vim`
