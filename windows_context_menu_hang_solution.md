---
title: "解决 Windows 下文件管理器中右键单击无响应问题"
date: 2020-04-26 22:44:37+0800
tags: [Windows]
categories: [technique]
---

# 引子 #

最近使用工作电脑的时候，不知道为什么，在文件资源管理器里面鼠标右键单击打开菜单(也叫[context menu](https://en.wikipedia.org/wiki/Context_menu))的时候，系统很大概率会卡顿，鼠标指针开始转圈，文件管理器白屏，然后会提示"未响应",过一会儿，右键菜单才会出现。以至于我每次点击右键之前，都很烦躁，希望不要出现卡顿的情况。

<!--more-->

使用 Windows，总有一种碰运气的感觉，如果什么东西不能正常工作了，很多时候，我们并不清楚为什么？可能重启一下，就没有这个问题了。另外一些问题，重启也无法解决，譬如这个右键菜单卡顿的问题，我\*\*重启了好几次也没有用。从这个角度来看，我更喜欢 Linux 系统，基本上出现了问题，从谷歌总能找到确定性（definitive）的答案，而不是像 Windows 一样，你需要尝试 N 种你也不确定能不能成功的方案。

# 解决方案 #

回归到这个问题，经过各种查找，大概确定这个卡顿的原因可能是第三方软件在右键菜单添加了太多项目，所以我们需要把一些没用的软件快捷方式从右键菜单里面清除出去。另外一些没用的软件，可以一起清理掉。

第一步：清理无用的应用。打开 Win10 设置（快捷键 `Win + I`），进入应用，没用的，长时间未使用的应用全部卸载。

第二步：清理右键菜单快捷方式。下载 [ShellMenuView](http://www.nirsoft.net/utils/shell_menu_view.html)这个小工具(下载地址[这里](http://www.nirsoft.net/utils/shmnview.zip))。无需安装，解压即可运行。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200426225057.png" width="600">
</p>

然后使用 `Ctrl-F` 快捷键查找希望禁用的右键快捷方式，找到某个项目以后，右键单击，然后选择 "Disable Selected Items" 或者直接使用 <kbd>F7</kbd> 快捷键。如果要查看该项目在注册表中的位置，可以用选择 "Open in RegEdit" 或者直接使用 <kbd>F6</kbd> 快捷键。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200426225205.png" width="400">
</p>

进行上述操作之后，重启系统，在文件管理器使用右键菜单，发现卡顿消失，可以确定卡顿就是第三方在右键菜单添加项目导致的。

# 参考 #

+ https://superuser.com/q/5011/736190
+ [Right click crashes Windows 10 Pro](https://answers.microsoft.com/en-us/windows/forum/all/right-click-crashes-explorer-on-windows-10-pro/3fd432f3-6b11-40be-b75f-babc038fcbff).
+ https://windowsreport.com/windows-explorer-crash-right-click/
+ https://www.zhihu.com/question/41279815/answer/139007395
