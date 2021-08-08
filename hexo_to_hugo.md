---
title: "把博客从 Hexo 迁移到 Hugo"
date: 2018-10-10T23:09:57+08:00
tags: [Hugo, Hexo, MathJax, LaTeX, Pandoc, Markdown]
categories: [Blog]
---

这个博客于 2017 年 2 月开始搭建运行，最初使用的是[Hexo](https://hexo.io/zh-cn/) 搭建，配合精美的[NexT](https://github.com/theme-next/hexo-theme-next) 主题，经过[鼓捣](https://jdhao.github.io/tags/Hexo/)，差不多算是做出了让自己满意的博客。不过每次安装 Hexo，要配置的东西太多了，总觉得不是很简洁，另外，随着文章数量的增加，Hexo 生成博客的速度也慢了下来。怀着尝鲜的心态，我把 Hexo 博客迁移到了 [Hugo](https://gohugo.io/)。

<!--more-->

# Hugo 安装与配置

## Hugo 安装

首先[安装 Hugo](https://gohugo.io/getting-started/installing)，在不同系统上安装都很简单，在 Mac 上，使用 Homebrew 可以安装 Hugo：

```bash
brew install hugo
```

## 建立博客站点

安装以后，首先我们需要建立一个博客目录，下面存放的是博客的所有配置以及博文等资料，例如要建立一个名为 `Blog` 的站点，使用下面的命令：

```bash
hugo new site Blog
```

会看到以下提示内容：

> Congratulations! Your new Hugo site is created in /Users/jdhao/Blog.
>
>Just a few more steps and you're ready to go:
>
>1. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/, or
   create your own with the "hugo new theme <THEMENAME>" command.
>2. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
>3. Start the built-in live server via "hugo server".
>
>Visit https://gohugo.io/ for quickstart guide and full documentation.

建立站点以后，博客根目录下默认有这些文件和子目录：

```
archetypes/  config.toml  content/     data/        layouts/     static/      themes/
```

`config.toml` 就是博客的配置文件，`archetypes` 目录下有一个 `default.md`，存放的是建立新博文时候默认使用的模板，可以根据自己需求修改。`content` 目录用来存放博文，`static` 可以存放一些自己的文件，`themes` 文件夹用于存放不同的主题。

## 安装主题

建立站点以后，我们需要安装主题，Hugo 提供了一个[主题浏览网站](https://themes.gohugo.io/)，可以浏览各种主题的效果，找了一圈，没有发现自己特别中意的。也可以直接在 GitHub 上搜索 Hugo 的主题，最后经过各种查找比较，选择了[even](https://github.com/olOwOlo/hugo-theme-even) 主题。

在博客根目录下，使用以下命令安装 even 主题：

```bash
git init
git submodule add https://github.com/olOwOlo/hugo-theme-even.git themes/even
```

然后编辑 `config.toml`，设置主题为 even:

```toml
theme = "even"
```

## 新建博文

要建立新的博文 `test.md`，使用下面的命令：

```bash
hugo new post/test.md
```

这个命令会在 `content` 目录下建立 `post` 目录，并在 `post` 下生成 `test.md` 文件，博文书写就在这个文件里使用 Markdown 语法完成。博文的 front matter 里`draft` 选项默认为 `true`，需要改为 `false` 才能发表博文，建议直接更改上面说的`archetypes` 目录下的 `default` 文件，把 `draft: true` 改为 `draft: false`，这样生成的博文就是默认可以发表的。

和 Hexo 一样，在 front matter 里面，使用 `tags` 指定文章的 tag，使用`categories` 指定文章的类别，示例如下：

```yaml
tags: [LaTeX, Hexo, font]
categories: [Mac, Linux]
```

把 Hexo 博客里的所有博文 Markdown 文件拷贝到 `content/post/` 目录下，就完成了博文的迁移。

## 查看生成效果

为了查看生成的博客的效果，可以使用 `hugo server` 命令，示例输出如下：

```
                   | EN
+------------------+-----+
  Pages            | 196
  Paginator pages  |   9
  Non-page files   |   0
  Static files     |  63
  Processed images |   0
  Aliases          |  53
  Sitemaps         |   1
  Cleaned          |   0

Total in 90 ms
Watching for changes in /Users/jdhao/Blog/{content,data,layouts,static,themes}
Watching for config changes in /Users/jdhao/Blog/config.toml
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```

博客有七十多篇文章，生成时间才 90 ms，速度果然惊人。该命令会建立一个本地的博客服务器，直接在浏览器访问网址 http://localhost:1313/ 即可查看生成的博客效果。

## 部署到 github pages

确定生成的博客效果无误后，就可以将博客部署到服务器了，我使用的是免费的 [GitHub pages](https://pages.github.com/) 服务。本部分假定我们在 github pages 建立的是 *User/Organization Pages* ，地址名称形式为`https://<USERNAME|ORGANIZATION>.github.io/`，如果不是这种类型地址，请参考 Hugo [官方指导](https://gohugo.io/hosting-and-deployment/hosting-on-github/)。

如果之前已经建立了 xxxx.github.io 这种形式的仓库，并且使用 Hexo 往这个仓库部署过，但是不想要之前的东西了，在博客的根目录下，运行下面的命令即可：

```bash
mkdir public
cd public
git init
git remote add upstream https://github.com/jdhao/jdhao.github.io.git
```

执行 `hugo -t even`，hugo 会生成 blog 内容，放到 public 目录。然后，我们把 public 下面的内容 push 到 github 即可。

```bash
git add .
git commit -m  "switch to hugo"
git push -f upstream master # 第一次 push 必须使用 -f，因为此时远程仓库非空，和本地有冲突
```

这样就部署成功了，一般等待几分钟，访问自己的博客，就能看到更新以后的内容。

每次部署都输入一大堆命令，很繁琐，参考[官方教程](https://gohugo.io/hosting-and-deployment/hosting-on-github/)，做一个`deploy.sh` 脚本，方便每次把博客 push 到 github。我的 `deploy.sh` 脚本内容为：

<details>
<summary><font color="red">点击查看脚本内容。</font></summary>

```bash
#!/bin/bash

echo -e "\033[0;32mDeploying updates to GitHub...\033[0m"

# build the project
hugo -t even

cd public

git add .

msg="rebuilding site `date`"

if [ $# -eq 1 ]
  then msg="$1"
fi

git commit -m "$msg"

# push source to github

git push upstream master

# come back to blog root

cd ..
```
</details>

赋予 `deploy.sh` 文件执行权限：

```bash
chmod u+x deploy.sh
```

以后执行 `./deploy.sh` 就可以部署博客到 github 了。

注：第一次 push 需要使用 `-f`，以后 push 就不需要该参数了，force push 以后，github 仓库的 commit 数量会清零，原有的 commit 信息会丢失，不过这个仓库就是为了存放博客，不需要和其他人合作，所以 commit 信息丢失问题不大。

# 主题设置与迁移细节

## 时间格式

Hexo 中时间格式与 Hugo 格式不太相同，Hugo 对时间格式的要求比较严格，Hexo 中`YYYY-MM-DD HH:MM` 格式的时间在 Hugo 中无法被正确识别。[Hugo 中使用的时间格式](https://scripter.co/time-formatting-in-go/)如下：

```
YYYY-MM-DD HH:MM:SS +0800
```

最后的 `+0800` 代表[中国的时区是GMT+8](https://zh.wikipedia.org/wiki/%E5%8C%97%E4%BA%AC%E6%97%B6%E9%97%B4)。

另外，Hexo 中 front matter 代表博客更新时间的 `updated` 选项在 Hugo 中是 `lastmod`。

具体时间格式的替换，我采用 Sublime Text 配合正则表达式匹配来替换。

## 文章的 permalinks 设置

Hexo 中，文章的链接都是 `YYYY/MM/DD/post_name/` 的格式，在 Hugo 中，可以通过设置 `permalinks` 参数来实现平滑过渡，在 `config.toml` 中加入以下设置：

```toml
[permalinks]
  post = "/:year/:month/:day/:filename/"
```

## 大小写问题

[Hugo 默认会把 url 中的大写字符转为小写](https://discourse.gohugo.io/t/disable-hugo-case-sensitive-url-matching/2498/4)，如果不想转换，在配置文件中加入

```toml
disablePathToLower = true
```

Hugo 默认会把分类（`categories`）名称转为小写，如果不想转换，在配置中加入，

```toml
preserveTaxonomyNames = true
```

## 如何完美支持 LaTeX 公式

由于 Markdown 中的 \\，`_` 等字符在 LaTeX 公式也有特殊含义，两者会有冲突，之前为了配置 Hexo 博客的数学公式支持也费了很大功夫，具体过程可以参考[这篇博客](https://jdhao.github.io/2018/01/25/hexo-mathjax-equation-number/)。迁移到Hugo以后，经过搜索、查找与验证，找到了两种比较好的解决方法。

### 使用 mmark 格式

[Mmark](https://github.com/miekg/mmark) 是一个 Markdown 引擎，Hugo 原生支持。对于包含 LaTeX 公式的文档，首先把后缀名改为 `mmark`（即名称由 `*.md` --> `*.mmark`），然后在文章的 front matter，加上一行设置

```toml
markup: mmark
```

### 使用 Pandoc

Hugo 也支持使用外部的处理程序来处理 Markdown 文件，例如 asciidoc，reStructuredText 和 Pandoc。这里选择 Pandoc，首先[安装Pandoc](https://pandoc.org/installing.html)。然后把包含 LaTeX 公式的博文后缀为`pdc`，重新生成博客即可，不需要其他设置。

### 小问题

Hugo 中，为了支持 LaTeX，如果使用 mmark 或者 pandoc 来处理 Markdown，生成的博客没有目录。对于 mmark 格式，[暂时不支持目录](https://discourse.gohugo.io/t/table-of-contents-for-mmark/7713/8)；对于Pandoc，无法生成 TOC 是因为 hugo 在调用 Pandoc 将 Markdown 文件转换为 HTML 的时候，只使用了 `--mathjax` 选项，没有使用 `--toc` 选项，具体 code 见 [这里](https://github.com/gohugoio/hugo/blob/master/helpers/content.go#L735), 如果加上 `--toc` 选项应该是可以生成 TOC 的。不妨试着加上 `--toc` 选项，然后 [自己编译hugo](https://github.com/gohugoio/hugo#build-and-install-the-binaries-from-source-advanced-install)看看能不能生成 TOC。

#### 参考

+ <https://github.com/gohugoio/hugo/pull/4060>
+ <https://stackoverflow.com/questions/37533412/md-with-latex-to-html-with-mathjax-with-pandoc/55106932#55106932>
+ <https://github.com/olOwOlo/hugo-theme-even/issues/139>

## 文章缩略的格式

要在文章某个地方缩略，要使用 `<!--more-->`。注意 `more` 的周围不要有空格，Hugo无法识别这种有空格的缩略格式，[这里](https://www.smslit.top/2018/07/07/hexo2hugo/#%E5%85%B6%E5%AE%83%E9%97%AE%E9%A2%98)的记录也证实了这个情况。同时，也可以参考[Hugo 官方文档的说明](https://gohugo.io/content-management/summaries/#manual-summary-splitting)。

## 个人文件的位置

如果博客中有一些文章设定了下载链接，建议把文件放在博客根目录下的 `static` 目录，可以在该目录下建立名为 `files` 的目录（该名称仅仅是举例，可以根据自己喜好更改），然后把文件放在 `files` 目录下。假如 `files` 目录下有一个文件 `test.pdf`，参考[这里](https://discourse.gohugo.io/t/link-to-static-file/78)，在博客中引用它的正确方式是:

```markdown
[点击这里下载 test.pdf](/files/test.pdf)
```

完整的配置文件见[这里](https://gist.github.com/jdhao/c512109907b55debd24623641c4e8fef)。

# busuanzi 博客计数失效？

Even 主题默认使用 [busuanzi](http://busuanzi.ibruce.info/) 服务来给博客的 pv 和sv 计数，最近打开博客，发现计数部分的数字无法显示，一直在转圈。查了一下，原来是busuanzi 在七牛云上的域名失效了，所以服务挂了。

解决办法如下，打开 even 主题的目录，打开主题目录下`/layouts/partials/head.html` 这个文件，把[^1]

```
<script async src="//dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"></script>
```

改为

```html
<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
```

然后重新生成博客，部署到博客托管网站服务器即可。

# 参考

+ Hugo quick start, https://gohugo.io/getting-started/quick-start/
+ http://www.flysnow.org/2018/07/29/from-hexo-to-hugo.html
+ 禁止转换分类名称为小写，https://discourse.gohugo.io/t/solved-disablepathtolower-doesnt-work-for-taxonomic-terms/9429
+ Hugo configuration, https://gohugo.io/getting-started/configuration/
+ Hugo 支持的博文格式，内有详细介绍，https://gohugo.io/content-management/formats/
+ Hugo 对 Pandoc 的支持，https://github.com/gohugoio/hugo/pull/4060

[^1]: 如何快速定位到这个文件，参考[这篇文章](https://jdhao.github.io/2018/10/07/nvim_fast_keyword_search/)
