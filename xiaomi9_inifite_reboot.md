---
title: "小米9 MIUI 12 系统开机界面无限重启"
date: 2021-06-24T01:36:41+08:00
draft: false
tags: [小米]
categories: [Android]
---

最近更新到了 MIUI 12.5 系统，晚上手机突然开始不停重启（bootloop），手机在不停重启，进入 MIUI logo，然后又开始重启，有时候能进入到系统，但是还没等到我开始设置，又开始重启了。

同时按电源和音量加，进入 recovery 模式，显示有三个选项：

+ 重启 （reboot）
+ 清除数据（wipe out data)
+ 连接小米手机助手

不过这时候只能按音量键选择选项，但是按电源键确认不起作用，手机会自动进入选择的选项，导致不小心手机进入了清除数据模式，数据全部被清除了。

后来经过几次重启，手机奇迹般恢复了正常，期间我拿掉了 sim 卡，不知道是否和这个有关系。

从网上也没找到靠谱的解决办法，有人说等电池耗尽，然后插上电源，重启就好了；也有人像我一样，重启多次以后，奇迹般又恢复了正常。使用两年多 MIUI，第一次遇到了这种问题，手机里面的照片和 app 数据都被清空了，又要重新配置一遍。

# 参考

+ https://www.reddit.com/r/Xiaomi/comments/mvilwk/miui_12_stable_update_keeps_restarting_cant/
+ https://www.reddit.com/r/Xiaomi/comments/k3dylc/mi_9_stuck_on_infinite_boot_loop_out_of_nowhere/
+ https://forum.xda-developers.com/t/mi-9-stuck-loop-boot-no-fastboot-no-hard-reset.4060119/
+ 一样的问题：https://www.bilibili.com/video/BV1hp4y1U7ai?spm_id_from=333.905.b_7570566964656f.3
