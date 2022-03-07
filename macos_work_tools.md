---
title: "macOS 上常用工具"
date: 2021-08-09T23:55:13+08:00
draft: false
tags: []
categories: [Mac]
---

<details>
<summary><font size="2" color="red">点击查看更新记录</font></summary>

+ 2022-03-07：增加 shotrr
+ 2022-02-10: 增加 wezterm 介绍
</details>

记录我在 macOS 下常用到的工具。

<!--more-->

# 按键显示(keystroke displaying)

平时录一些 Neovim 或者其他工具/软件操作视频，把按键记录下来会更方便读者理解你干了点啥。可以使用开源的 [KeyCastr](https://github.com/keycastr/keycastr)。

**注意**：安装以后，要想正常使用，需要进行一些设置，打开「Security & Privacy」，进入 Privacy 部分，需要在 「Accessibility」和 「Input Monitoring」把 KeyCastr 加上，否则该 App 无法正常使用。

# 键盘按键映射

功能强大的按键映射工具，当然是 [Karabiner Elements](https://github.com/pqrs-org/Karabiner-Elements)。

# 截图

开源的可以使用非常有名的 [Snipaste](https://www.snipaste.com/)。另外 [iShot](https://apps.apple.com/cn/app/ishot-%E4%BC%98%E7%A7%80%E7%9A%84%E6%88%AA%E5%9B%BE%E5%BD%95%E5%B1%8F%E5%B7%A5%E5%85%B7/id1485844094?mt=12) 以及 [xnip](https://xnipapp.com/) 也不错，这俩是收费的，不过也有免费使用功能。

[shottr](https://shottr.cc/) 是另外一款截图工具，免费。

# 录制视频/GIF

经常用 [LICEcap](https://github.com/justinfrankel/licecap)，体积小巧，用起来方便。[kap](https://github.com/wulkano/kap) 也是一款类似的开源工具（安装包真大！bloat ware）。

# 图床上传

一直在用 [PicGo](https://github.com/Molunerfinn/PicGo)，开源免费，简单好用，支持主流云服务，GitHub，Gitee 等。其他好用的似乎都要付费使用。

# 鼠标平滑滚动

mac 上鼠标滚动方向和 Windows 不同，用起来有点难受，我们可以用 [mos](https://mos.caldis.me/) 帮助我们实现更平滑的鼠标滚动和反向滚动（这样 mac 鼠标和 Windows 操作就统一了）。

# 终端模拟器

一直在使用 [kitty](https://github.com/kovidgoyal/kitty)，简洁而强大，支持多 tab。相比之下，感觉 [iterm2](https://iterm2.com/) 略显笨重。[Alacritty](https://github.com/alacritty/alacritty) 不支持多 tab 就更不考虑了。

在 kitty 里面，打开 Neovim，输入汉字的时候，显示会有一些问题，最近换到了 [wezterm](https://github.com/wez/wezterm)，功能没有 kitty 全面，不过对汉字输入支持似乎更好一些，就先用它了。我的 wezterm 配置参见 [这里](https://github.com/jdhao/dotfiles/blob/master/.config/wezterm/wezterm.lua) 。

# APP 删除清理

在用 [appcleaner](https://freemacsoft.net/appcleaner/)，功能简单好用。

# 图片查看

目前在用 [XnViewMP](https://www.xnview.com/en/xnviewmp/)。

# 图片压缩

压缩图片，往图床上传时节省空间，在用 [ImageOptim](https://github.com/ImageOptim/ImageOptim)。

[TinyPNG4Mac](https://github.com/kyleduo/TinyPNG4Mac) is also an interesting project.

# 视频播放

使用 [IINA](https://github.com/iina/iina)，基于 [mpv](https://github.com/mpv-player/mpv) 开发。

# 视频字幕制作

[ArcTime](https://arctime.org/) 是一款不错的工具，曾用它制作过 [Bram 在 vimconf 2018 上的演讲字幕](https://jdhao.github.io/2019/12/15/mac_video_subtitle_make/)。

# 窗口管理（window manager）

有时候需要让两个窗口并排显示，便于参照和对比，手工排放窗口位置调整大小比较痛苦，就需要用到窗口管理工具，目前在用 [rectangle](https://github.com/rxhanson/Rectangle)，也是看到别人推荐才用的，效果还不错，满足我的简单需求。

# 参考

+ awesome mac: https://github.com/jaywcjlove/awesome-mac
+ https://blog.skk.moe/post/mac-software/
