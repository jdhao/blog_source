---
title: "Mintty Tips and Configurations"
date: 2019-10-16 23:24:35+0800
tags: [mintty, Cygwin]
categories: [tools]
---

In this post, I want to share some useful tips and configurations about
[mintty](https://github.com/mintty/mintty).

<!--more-->

# Share mintty config between Cygwin and wsltty

Since both Cygwin and wsltty use mintty as its terminal emulator, it would be
great if we could use a unified config file for mintty intead of having to
configure separately on two locations. According to [doc
here](https://github.com/mintty/mintty/wiki/Tips#configuring-mintty), mintty
provides such a feature: you need to put configuration in
`%APPDATA%\mintty\config` so that mintty for Cygwin and wsltty can use the same
config.

# Quickly switch to other mintty windows

Unlike iterm or other terminal emulator, mintty does not have a Tabbed window.
However, mintty does provide a virtual tab feature. If you are using mintty
3.0.3 or above, you can switch between different mintty windows with several
shortcuts:

+ ctrl + right-click
+ ctrl + right-click in the window title bar
+ ctrl + menu

## References

- [Virtual tabs](https://github.com/mintty/mintty/wiki/Tips#virtual-tabs).
- [Virtual tab discussions](https://github.com/mintty/mintty/issues/699).

# Open file, directory or links

Use ctrl+left-click to open a file, directory or URL (beginning with `www`).

# Change cursor when IME is on

The option `IMECursorColour` can be used to set the cursor color when IME is
on. For example:

```
IMECursorColour=255,0,0
```

this will set cursor color to red when IME is on.

# Show selection text size

The option `SelectionShowSize` can be used to show the size of selection. `0`
disable this feature. A value of `1-12` shows the size of selected text in a
position corresponding to the clock hours.
