---
title: "解决不蒜子 (busuanzi) 文章计数出错问题"
date: 2020-10-31T21:48:44+08:00
draft: false
tags: [Hugo, HTTP]
categories: [Blog]
---

一个月之前，就发现自己的博客用的计数系统[“不蒜子”](https://busuanzi.ibruce.info/)对每篇博客访问量的统计出现了问题，刚写的博文，访问量都是几十万，显然不正常，但是很神奇，整个博客的访问量统计似乎并没有问题。当时也在 [v2ex](https://v2ex.com/t/707052#reply7) 发帖咨询了，不过没有人给出合理的答案，今天偶然间[发现了原因](https://github.com/flysnow-org/maupassant-hugo/pull/121)，发文记录。

<!--more-->

# 原因

出现这个原因，和 Chrome 85 版本 [Referrer](https://en.wikipedia.org/wiki/HTTP_referer) Policy [更改](https://developers.google.com/web/updates/2020/07/referrer-policy-new-chrome-default)有关。什么是 Referrer，简单理解，就是请求 Web 服务器时，可以在 HTTP Request 的请求头 (header) 中加上当前页面的 URL，例如我们在浏览某个博客页面，需要加载一些图片，从服务器请求这些图片时，referrer 就是当前的博客页面 URL。从这里也可以看出，referrer 可能会暴露请求来源的某些信息或者隐私，有一定的隐私或安全风险。之前版本的 Chrome 浏览器，如果网站没有指定自己的 Referrer Policy，那么 Chrome 默认 policy 是 `no-referrer-when-downgrade`，在 Chrome 85 版本中，为了保护用户的隐私，默认的 Referrer Policy 则变成了 `strict-origin-when-cross-origin`。

不过这到底是什么意思？[Referrer Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy) 用来控制当前网站请求目标网站时， referer 这个字段发送的具体信息，如果 Referrer Policy 为：

+ no-referrer-when-downgrade: 当两个网站的 http 协议安全等级相当，或者目的网站安全协议等级高于当前网站(HTTP --> HTTP, HTTPS --> HTTPS, HTTP --> HTTPS)， referer 将会包含源网站的域名，路径，查询字符串；如果目的网站安全协议等级低于源网站 (HTTPS --> HTTP)，将不会发送这些信息。
+ strict-origin-when-cross-origin： 只有当做同一域名请求时 (源网址和目标网址是同一域名），才发送域名，路径和请求字符串，当两个网站安全协议相当时，发送源网站的域名(没有具体路径信息和查询字符串)，如果目标网站安全协议等级低于源网站，不发送 header 信息。

不蒜子统计博客文章访问量就是通过 referer 来计算的，通过上面的分析，如果 Referrer Policy 是 strict-origin-when-cross-origin，不蒜子接收到的只有博客的域名，没有文章的具体路径，所以具体某个文章的 PV 统计会出现错误。例如 [这篇文章](https://jdhao.github.io/2020/10/30/cn_yang_interview/)，发送给不蒜子的 referer 只是 `https://jdhao.github.io`。

所以，要想正确统计博客每篇文章的 PV，可以把 Referrer Policy 设置为 no-referrer-when-downgrade。

# Hugo even 主题中如何更改

我目前使用的 [Hugo-even](https://github.com/olOwOlo/hugo-theme-even) 这个主题，更改很简单，进入 even 主题的根目录， 找到 `layouts/partials/head.html` 文件中关于不蒜子的部分，把下面的代码

```html
{{- if .Site.Params.busuanzi.enable -}}
  <script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
{{- end -}}
```

改成

```html
{{- if .Site.Params.busuanzi.enable -}}
  <script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
  <meta name="referrer" content="no-referrer-when-downgrade">
{{- end -}}
```

然后重新生成 Hugo 博客即可。
