---
title: "Why Doesn't Visdom Work Fine on CentOS?"
date: 2017-10-16 23:37:33 +0800
tags: [CentOS]
categories: [life]
---

Today, I came across the open source project
[visdom](https://github.com/facebookresearch/visdom) and hope to use it in my
future [PyTorch](http://pytorch.org/) project. I installed it on my Linux
server and met with an issue that I have no way to deal with. I would like to
share it in case someone also meet it.

<!--more-->

After installing visdom successfully, I started to play with it. But I find
that it can not work properly on my CentOS server. After a long time of
loading, the final webpage do not contain the tool bar that normal visdom
webpages would have.

<img src="https://blog-resource-1257868508.file.myqcloud.com/visdom_issue.png"
title="normal and abnormal visdom page"
style="float: middle;">

In order to find the cause, I also installed one on my Windows computer and
find that it works properly. So I added an [issue](https://github.com/facebookresearch/visdom/issues/174) on github.

After a while, a contributor to visdom responded. We started this nearly 2-hour
journey to find what the hell was wrong. Finally, with the help of this kind
and patient man, I find that it is because some of the javascript resources can
not be loaded on my CentOS server. Why does not that happen on my Windows
machine? I tried to switch off my proxy manager. Suddenly, visdom can not work
on Windows too. So the root cause is that our great firewall has blokced some
of resource addresses used by visdom. Plain and simple.

I love you, my great country. I have nothing to say.
