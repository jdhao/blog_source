---
title: "Mac 系统如何远程连接控制 Windows 电脑"
date: 2020-02-09T21:43:35+08:00
draft: false
tags: []
categories: [Mac]
---

本文简单总结 macOS 系统远程连接 Windows 系统需要的工具以及简单设置。

<!--more-->

# TeamViewer

最简单的方式还是使用 [TeamViewer](https://www.teamviewer.cn/cn/)，非商业应用，建议使用个人版。在 Mac 上和要连接的 Windows 机器上都安装好 TeamViewer，然后在 Windows 机器上打开 TeamViewer，会有显示 TeamViewer ID 以及密码。在 Mac 上打开 TeamViewer，输入 ID 以及密码，即可连接到 Windows 机器。

偶尔由于网络连接等因素影响，连接可能会中断，需要重新连接。这种连接方式非常简单，不需要太多复杂的设置，适合普通用户使用。

# Microsoft Remote Desktop

如果是连接公司内部的电脑，可能需要 VPN 才能连上[^2]，不能使用 TeamViewer 直接连接，VPN 设置好以后，就可以使用 Microsoft Remote Desktop 连接远程电脑。

[Microsoft Remote Desktop](https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac) 是微软提供的连接 Windows 电脑的工具，有多种客户端可以下载。美区的 App Store 账号可以直接从 App store 下载，App Store 有两个版本，Microsoft Remote Desktop 8 和 Microsoft Remote Desktop 10，版本 8 是旧版本，已经停止更新，建议使用版本 10。

国内 App Store 没有上架 Microsoft Remote Desktop，可以直接从 [这里](https://go.microsoft.com/fwlink/?LinkID=619698) 下载 Beta 版本 (可能需要梯子才能访问)安装。

安装完成之后，点击 "Connections --> Add PC" 或者直接使用 <kbd>⌘</kbd> + <kbd>N</kbd> 可以创建新的连接。PC name 一般情况下填写电脑的 IP[^1]。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200209222615.jpg" width="400">
</p>

然后，User account 一栏，在下拉菜单选择 “Add User Account...”，在弹出的窗口，填写自己的用户名，以及电脑的登陆密码。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200209223423.jpg" width="400">
</p>

如果一切设置正确，点击连接，等待一段时间，即可连接上 Windows 电脑，如果通过 VPN 连接 Windows，注意一定要保持 VPN 出于连接状态，否则无法连接到 Windows 电脑。

# Jump Desktop

[Jump Desktop](https://jumpdesktop.com/) 也是一款远程桌面应用，需要付费，支持 14 天免费试用，点击 [这里](https://jumpdesktop.com/downloads/jdmac) 可以下载。安装以后，点击 Quick Connection 按钮，选择 RDP 协议，输入 windows 电脑的 IP 地址。添加完成以后，双击新建的连接，输入远程电脑的用户名还有密码，即可连接远程桌面。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200226144337.jpg" width="400">
</p>

个人感觉，Jump Desktop 的显示效果比 Microsoft Remote Desktop 要好很多，Microsoft Remote Desktop 显示的桌面比较模糊，Jump Desktop 显示的桌面很清晰。

# Remotix

[Remotix](https://nulana.com/remotix-mac/) 也可以用于连接远程的 Windows 桌面，可以免费试用 15 天时间。安装以后，点击界面左上角 「Create New Connection」，选择 RDP 协议，然后在弹出的窗口，输入远程电脑的 IP，在 Authentification 部分，输入用户名还有密码，然后保存连接即可。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200226144916.jpg" width="400">
</p>

Remotix 显示效果也比 Microsoft Remote Desktop 好，但是略差于 Jump Desktop，也可能是默认设置的问题。

# 参考 #

+ https://www.maczd.com/post/microsoft-remote-desktop-mac.html
+ https://blog.devhitao.com/2019/03/23/microsoft-remote-desktop-for-mac/
+ https://www.waitsun.com/tag/yuan-cheng-zhuo-mian

[^1]: IP 地址可以在 Windows 命令行使用 ipconfig 命令查看，查看「IPv4 Address」那一项。
[^2]: 如果需要 VPN，首先需要把 VPN 连接上，否则无法连接上远程的 Windows 电脑。
