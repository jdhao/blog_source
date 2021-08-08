---
title: "Configure Windows Terminal in 2021"
date: 2021-08-06T00:23:42+08:00
draft: false
tags: [terminal]
categories: [tools]
---

After updating my Windows system to the latest version. I can finally use the
new [Windows Terminal](https://github.com/microsoft/terminal).

<!--more-->

# How to install?

Directly install from GitHub binary release, link [here](https://github.com/microsoft/terminal/releases).

# Use git-bash in Windows Terminal

Install [git for Windows](https://gitforwindows.org/). During installation,
there is an option to create a profile for Windows terminal. Remember to tick
it on.

Go check windows terminal settings, and choose `Git Bash` as the default
profile.

## Remove annoying flash

When I use Git-bash and press backspace, the whole screen flashes which is
really annoying. Based on discussion [here](https://github.com/microsoft/terminal/issues/7200), we can add following conf to
`~/.inputrc`:

```bash
set bell-style none
```

# Unable to load a font?

I have set up `JetBrainsMono NF` as my default font. However, when I open
Windows Terminal for the first time, it warns:

> Unable to find the selected font "JetBrainsMono NF"

Similar issue has been reported [here](https://github.com/microsoft/terminal/issues/9375). The solution is to re-install the
font and **install it for every user** instead of the current user.

# Add a new colorscheme

[This site](https://windowsterminalthemes.dev/) contains a lot of custom
colorschemes for windows Terminal. Press `Get Theme` to copy the theme
configuration.

Then open Terminal config, and paste the theme config to the `schemes` section
and restart Terminal.

There are also some themes [here](https://github.com/rjcarneiro/windows-terminals).
