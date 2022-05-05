---
title: "Content inside HTML tags missing in Latest Hugo?"
date: 2019-12-29T17:57:05+08:00
tags: [Hugo, Markdown]
categories: [Blog]
---

Due to Markdown's inability to center and resize image properly,
I use the raw HTML tags inside markdown file to include images:

<font color="red">由于 Markdown 格式无法很好居中和设定图像尺寸，我使用 HTML tags 来添加图像(图像可以很好居中以及设定显示大小):</font>

<!--more-->

```html
<p align="center">
<img src="xxx.xx.xx/test.jpg">
</p>
```

Recently, after updating to Hugo version 0.62,
I found that all the images using HTML tags are missing in the generated HTML files.

<font color="red">最近，升级了 Hugo 0.62 版本以后，我发现所有使用 HTML tag 引入的图像在生成的最终 HTML 文件中都消失了。</font>

I checked the generated HTML source file for my post,
and found that the raw HTML tag for image is rendered as the following [comment tag](https://html.com/tags/comment-tag/):

<font color="red">我查看了某个博文渲染而成的 HTML 源文件，发现 Markdown 中的 HTML 图像 tag 被渲染成了 HTML 中的注释：</font>

```html
<!-- raw HTML omitted -->
```

After a little dig, I find out the cause.
Previously, Hugo was using [Blackfriday](https://github.com/russross/blackfriday) to render Markdown files.
Since Hugo version 0.60, the default Markdown renderer has been changed to [goldmark](https://github.com/yuin/goldmark/).
In goldmark, the default behaviour is not to render raw HTML tags.

<font color="red">经过一番查找，我找了问题的原因。在之前的版本中(0.60 版本之前)，Hugo 使用 [Blackfriday](https://github.com/russross/blackfriday) 来渲染 Markdown 文件。从 0.60 版本开始，默认的 Markdown 渲染器已经改成了 [goldmark](https://github.com/yuin/goldmark/)。Goldmark 渲染器默认不回渲染 HTML tags。</font>

To fix this issue, we have two options.
First option, set `blackfriday` as the default Markdown engine. Open `config.toml` and add the following setting:

<font color="red">为了修复这个问题，我们有两种不同的解决方法。第一种选择，重新把 blackfriday 设定为默认的 Markdown 引擎。打开 `config.toml`，加入以下设定：</font>

```toml
[markup]
  defaultMarkdownHandler = "blackfriday"
```

Second option, use `goldmark` and set the `unsafe` option of `markup.goldmark.renderer` to `true`:

<font color="red">第二种方式，使用 goldmark 引擎，加入以下设定，更改 goldmark 默认行为：</font>

```toml
[markup]
  defaultMarkdownHandler = "goldmark"
  [markup.goldmark]
    [markup.goldmark.renderer]
      unsafe = true
```

# References

+ [Hugo 0.60 release note.](https://gohugo.io/news/0.60.0-relnotes/)
+ https://gohugo.io/getting-started/configuration-markup/
+ https://discourse.gohugo.io/t/raw-html-getting-omitted-in-0-60-0/22032/18
+ https://discourse.gohugo.io/t/hugo-0-60-0-raw-html-omitted-issue/22010/9
+ https://github.com/gohugoio/hugo/issues/5963
