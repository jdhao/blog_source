---
title: "【2021 年】iPad 安装 Shadowrocket"
date: 2021-12-12T13:33:41+08:00
draft: false
tags: [iPad]
categories: [Digital]
---

在苹果系统上安装一个工具真麻烦，简单总结如何在 iPad 上安装 Shadowrocket 小火箭。

<!--more-->

Shadowrocket 在中国区 App Store 下架，无法下载。iPad 也无法像安卓一样，方便直接安装第三方 App，导致安装一个 Shadowrocket 非常麻烦。

# 亲测可行的方法

第一步，把 apple id 地址改为美国 (地址写个 fake 地址，可以用 [这个](https://www.fakenamegenerator.com/)帮你生成 )，关于如何更改地区，也可以参考 [官方教程](https://support.apple.com/zh-cn/HT201389) 或者 [这里](https://zhuanlan.zhihu.com/p/138058796)

第二步，在 [apple 美国官网](https://www.apple.com/shop/gift-cards)[^1]，购买 gift card，按照提示操作即可，亲测国内的 visa 卡也能支付成功，注意选择 email 邮寄

第三步，打开 app store 充值，我用 iPad 的 App store 不知道为什么不行，一直提示

> this card must be redeemed in us storefront

后面用 MacBook 再打开邮件，然后点击 redeem 在电脑上充值成功

第四步，购买应用，会自动从礼品卡扣款


# <font color="red" size=5>一些经过验证并不可行的方法</font>

+ 从 GitHub 下载了别人提取好的 Shadowrocket ipa 文件，通过爱思助手直接安装 ipa，打开应用以后，提示要输入购买应用时候用的 apple id 和密码，我肯定没有啊，这种方法不行。
+ 使用网上提供的所谓购买过 Shadowrocket 的美区 id 和密码，App store 退出自己的账号，用该账号密码登陆，直接验证不通过，怀疑已经被封了。
+ 把 apple id 改为美区，用国内的 visa 卡支付也不行，支付的时候无法支付，据说是因为国内银行的 visa 卡也会被认为是国内信用卡，所以不行。


[^1]: 在这里 https://www.apple.com/choose-your-country 把地区切换为 United States
