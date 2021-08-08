---
title: 把 Hexo 的评论系统切换为 gitment
date: 2018-02-01 20:24:08 +0800
tags: [Hexo, NexT]
categories: [Blog]
---

好几个月前就知道有人用 GitHub 的 issue 功能为 Hexo 制作了一个评论系统
[gitment](https://imsun.net/posts/gitment-introduction/)，决定尝试一下 gitment
。

<!--more-->

# 简单配置

我使用的是 NexT 主题，最新版已经引入了 gitment 的支持，因此配置相对比较简单。首
先[点击这里](https://github.com/settings/applications/new)注册 GitHub OAuth，示
例配置如下：

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-2-1/89019898.jpg"
         title="GitHub OAuth 配置"
         style="float: middle;">

在接下来的页面，也会看到自己的 `Client ID` 以及 `Client Secret`，如下图所示，

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-2-1/68143413.jpg"
         title="Client ID 以及 Secret"
         style="float: middle;">

接下来打开 NexT 主题的配置文件，位于 `hexo\blog` 目录下的
`theme\next\_config.yml`。 扎到 gitment 部分，示例配置如下：

```yaml
gitment:
  enable: enable
  mint: true # RECOMMEND, A mint on Gitment, to support count, language and proxy_gateway

  count: true # Show comments count in post meta area

  lazy: false # Comments lazy loading with a button

  cleanly: false # Hide 'Powered by ...' on footer, and more

  language: en
  github_user: jdhao
  github_repo: jdhao.github.io #the name of the repo used to host your blog
  client_id: xxxxxxx
  client_secret: xxxxxx
  proxy_gateway: # Address of api proxy, See: https://github.com/aimingoo/intersect

  redirect_protocol: # Protocol of redirect_uri with force_redirect_protocol when mint enabled

```

设置完成以后重新 generate blog 即可。

# 使用

初次使用，对于一篇博客，想要评论，必须登录自己的 GitHub 帐号，同时如果是第一次
评论，需要点击 `Initialize Comments`:

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-2-1/56553472.jpg"
         title="Initialize Comments"
         style="float: middle;">

然后就可以进行评论了。

# 值得警惕的事情

如果你使用 GitHub 帐号评论别人的博客，博客的主人将有权力读写你的账户下 repo 的
数据，所以[可能会造成一些安全问题
](https://github.com/theme-next/hexo-theme-next/issues/35)，如果介意的话，最好
不要使用 gitment。

---
# 参考

+ <https://github.com/imsun/gitment/issues/18>
