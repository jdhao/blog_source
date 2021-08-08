---
title: "Hugo Post Missing (Hugo 博客文章缺失问题)"
date: 2020-01-11T22:04:56+08:00
tags: [Hugo]
categories: [Blog]
---

Recently, when I was trying to generate my blog site using [Hugo](https://gohugo.io/),
I found that all my posts were successfully rendered, except for one new post.
This was rather strange.

<font color="red">最近在使用 [Hugo](https://gohugo.io/) 生成博客文章的时候，发现有一篇新写的文章无法生成，而其他博文都可以成功渲染，非常奇怪。</font>

<!--more-->

After some debugging, I found the culprit is the date format I use in the post
front matter. I did not add the timezone info when I wrote the date info (only
year, month, date, hour, minute, second were used). The date field info was
like `2020-01-10 23:48:25`. I added the timezone info to the date info:
`2020-01-10 23:48:25+0800` and regenerated the blog site using Hugo. After that,
the missing post was shown in the blog site. So we can conclude that the
culprit is the incomplete date info.

<font color="red">经过比较和查找，觉得问题可能出在文章 front matter 的 date 书写格式上：当时 date 没有写时区信息，只写了年月日和时分秒信息，具体写的是 `2020-01-10 23:48:25`。后面再加上了时区信息， date 变成了 `2020-01-10 23:48:25+0800`。然后使用 hugo 重新生成博客，果然新写的文章可以显示在博客里面了，由此确定是博文的日期格式问题。</font>

# The cause（原因） #

After pinpointing the issue, I searched the web and found the root cause.
Whether a post will be rendered by Hugo depends on when this post will be
published. If the publish time of a post is later than the time when we build
the blog site, i.e., Hugo thinks that we want to publish this post in future
time, then Hugo will not render this post by default. The post mentioned
earlier is not rendered, because Hugo thinks we have not reached its publish
date, so it skips rendering that post.

<font color="red">确定了问题以后，在网上经过一番搜索，我发现了问题的根源，原来 Hugo 是否会渲染一篇博文依赖该文章的发布时间。如果一个博文的发布时间比 Hugo 构建当前站点的时间还要晚，也就是 Hugo 认为博文的发布时间在未来，就不会渲染该篇博文。前面没有写时区的博文，就是被 Hugo 认为发布时间还未到，所以没有渲染出来。</font>

# Solution（解决方法） #

To solve this problem, we need to force Hugo to render posts that have publish
time in the future. We can fix this in two ways. First, add the following
setting to `config.toml`:

<font color="red">要解决这个问题，可以强制 Hugo 渲染发布时间在未来的博文，有两个办法。第一个是在 `config.toml` 中加入以下设置</font>

```toml
buildFuture = true
```

The second way is to add option `--buildFuture` when we are using Hugo to build
the blog site.

<font color="red">第二个方法是在 hugo build 博客的时候，加上 `--buildFuture` 选项。</font>

# Reference（参考） #

+ https://www.jvt.me/posts/2019/03/24/datetime-hugo/
+ https://discourse.gohugo.io/t/issue-with-generating-site-missing-posts/12149/3
+ https://github.com/bppwrite/bppwrite.github.io/issues/44
+ https://github.com/gohugoio/hugo/issues/3977
