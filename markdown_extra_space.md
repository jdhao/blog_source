---
title: "Markdown 生成 HTML 时汉字之间出现多余空格问题"
date: 2019-05-04 23:26:14+0800
tags: [Markdown, Pandoc, Hugo]
categories: [Blog]
---

在过去很长一段时间，我都在[使用 Sublime Text 来编辑 Markdown 文件](https://jdhao.github.io/2017/03/04/Sublime-Windows-Markdown/)，Sublime Text有一个非常实用的功能，[可以设定 `wrap_width`  和 `word_wrap` 选项](https://stackoverflow.com/q/25900954/6064933)，自动在设定的 80个字符长度处 softwrap 文本。所以即使一个段落写成一行，编辑起来或者看起来都不违和。

<!--more-->

最近将 Markdown 编辑器从 Sublime Text 换成 Neovim 以后，由于 Neovim 当前并没有将文本 softwrap 到特定字符宽度的功能，于是我转而使用[`textwidth`](https://neovim.io/doc/user/options.html#'textwidth')选项，强制所有文本在达到 `textwidth` 以后自动换行。对于英文，这倒没有什么影像，但是对于中文，两行首尾相接位置的字符之间就会莫名其妙地多出一个空格。例如下面的句子

```txt
你好吗
我很好
```

渲染成 HTML 以后，会变成 `你好吗 我很好`，汉字`吗`和`我`之间多了一个空格。

这个问题的原因是当初 HTML 的设计者都是以英语为主要语言，英语中单词之间使用空格来分隔，因此不同行之间的英文单词中间的换行符被转换成了空格，具体参见[这里](https://stackoverflow.com/a/8551033/606493)。但是中文字符之间并不需要空格来分隔，所以就出现了上述的问题。改 HTML 标准是不可能的了，因此只能通过其他方法来处理。

# Hugo 解决办法

通过查找，我发现 Hugo 使用的 blackfriday 引擎是支持处理中文字符之间的空格问题的，使用方法很简单，在 Hugo 的配置文件 `config.toml` 中加入以下配置：

```toml
[blackfriday]
  extensions = ["joinLines"]
```

经过观察发现，该选项不仅对中文有效，对英文也有效，因此上下两行之间的英文单词本来应该有的空格也会被取消，所以以上的方法并不好，谨慎使用，除非你主要使用的是中文。

# Pandoc 解决办法

如果你使用 Pandoc 将 Markdown 转为 HTML，那么可以加入 Extension [`east_asian_line_breaks`](https://pandoc.org/MANUAL.html#non-pandoc-extensions)，这个 Extension 就是专门针对上述的情况设计的。

# 权宜之计

如果中文和英文的博客都写，并且使用 Hugo，目前似乎没有完美的解决方案。只好把一个段落的问题都写成一行，为了达到 softwrap 的效果，我们可以使用[Goyo](https://github.com/junegunn/goyo.vim)，近似实现像 Sublime Text 中那样的结果。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190507012004.png" width="600">
</p>


# 参考资料

+ Hugo 对中文字符之间空格的处理
    + https://lizyn.com/zh/post/2018/md-space-between-cjk/
    + https://github.com/gohugoio/hugo/pull/3574
+ Pandoc 中这种情况的处理
    + https://talk.commonmark.org/t/soft-line-breaks-should-not-introduce-spaces/285/8
    + https://github.com/jgm/pandoc/pull/534
+ 对于 Vim softwrap width 的讨论
    + https://stackoverflow.com/questions/989093/soft-wrap-at-80-characters-in-vim-in-window-of-arbitrary-width
    + https://vi.stackexchange.com/questions/8961/soft-linewrap-width
    + https://github.com/neovim/neovim/issues/4386
    + https://www.reddit.com/r/vim/comments/8ov6l6/soft_wrap_at_a_certain_number_of_characters/
