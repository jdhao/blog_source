---
title: "Hexo 使用，博客写作相关问题及解决方法"
date: 2017-02-26 15:52:00 +0800
tags: [Markdown, NexT, Hexo, LaTeX, 图床, 对象存储]
categories: [Blog]
---

本文主要总结 Hexo 博客使用，博客写作以及更新 Hexo 和 NexT 主题的内容。关于如何安装配置 Hexo 和 NexT 主题，可以参考[这篇文章](https://jdhao.github.io/2017/02/26/hexo-install-use-issue/)。

<!--more-->

# 博客使用相关问题

## 博客顶部的菜单的语言在英语与德语乱跳

这个是由于在站点配置文件没有指定语言，指定语言为 `en` 可以解决这个问题 。

```yaml
language: en
```

如果想在菜单栏显示汉字,那么语言就指定为 `zh-Hans`，参考了[这个答案](https://www.zhihu.com/question/41625825)。

## 添加 sitemap.xml 到谷歌搜索引擎

首先按照[博客一](http://lindaxiao-hust.github.io/2016/04/06/hexo-next-sitemap/)给出的步骤生成正确的 `sitemap.xml` 文件，这里我遇到的问题是生成的 sitemap.xml文件里面都是 yousite.com 这种格式，原因很简单，因为没有在站点配置文件里把博客的URL 改成自己的实际地址(见下面的代码)，把  `url` 改成自己博客的地址，再生成一遍就不会有这个问题。

```yaml
url: https://jdhao.github.io
```

生成 `sitemap.xml` 以后，需要验证自己的网站(也就是证明网站是你的)，继续参考[博客一](http://lindaxiao-hust.github.io/2016/04/06/hexo-next-sitemap/)的方法(有的博客上提到的方法属于过去式，譬如[博客二](http://fionamood.com/)第三步中提到的手动添加验证的方法对于 NexT 主题已经不再需要，那篇博文的名字叫 「｜Hexo优化｜如何向google提交sitemap（详细）」)。验证网站以后，就可以添加 `sitemap.xml` 到谷歌了，这一步参考[博客二](http://fionamood.com/)中博文从第四步开始的内容。

这一部分还参考了[博客三](http://www.jianshu.com/p/86557c34b671)，加入了`robots.txt` 文件，不过我没有设置百度的 sitemap。

## 更改了 NexT 主题，部署以后发现更改未生效

使用 `hexo s -g` 本地预览网页没问题，部署以后发现主题没有改变，解决办法是先使用`hexo clean`，并且删除 `blog` 目录下的 `.deploy_git` 目录，重新部署，然后打开博客网页，按 `CTRL+F5`，强制刷新，更新资源，然后就能看到更新以后的主题了。

## 点击某个标题，生成指向该标题的 URL

很多网页都使用 [named anchor](http://freepages.computers.rootsweb.ancestry.com/~pasher/anchors.htm)功能，当点击目录中的某个标题的题目，就会自动跳转到该标题在网页中位置，并且浏览器地址栏随之改变，URL 增加了 `#header_name` 部分，如果想要打开该博文的时候自动到达该标题对应的位置，而不是到达该页面的顶端，只需要使用这个新的 URL 即可，这个功能可以通过替换 Hexo 自带的 renderer 实现，具体可以看[这里](http://hujichn.github.io/2016/04/06/Win10%E4%B8%8A%E5%88%A9%E7%94%A8github+hexo%E5%BB%BA%E7%AB%8B%E5%8D%9A%E5%AE%A2%E7%9A%84%E6%96%B9%E6%B3%95/#8-增加脚注功能footnotes)，不再多说。设置完成以后，鼠标移动到某个标题最右边，可以看到「¶」。效果如下图

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/header_anchor.png" width="800">
</p>

点击它，可以看到 URL 发生改变，如下图

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/url_anchor.png" width="800">
</p>

## 更改一篇 post 的 categories 或者 tags 的大小写，部署以后发现更改无效

写好一篇 post，部署以后，想要更改 categories 或者 tags 的大小写，更改以后，再次部署，打开博客的 categories 页面，发现更改是生效的，但是点击刚才更改名字大小写的类别，你会得到 *404 not found error* 。这个问题是由于在 case-insensitive 系统上 (譬如 Windows 和 Mac OSX)，git 会忽略对文件或者目录大小写的更改，hexo 暂时还未修复这个问题，具体的 workaround 可以参见[这里](https://github.com/hexojs/hexo/issues/2600)。

## 在博文中增加文件的下载链接

可以在 source 目录下创建一个目录来存放你的文件，例如可以创建 `source/files/` 目录，然后把文件放在该目录下面，假设文件名称为 `test.pdf`，在博文中需要引用该文件的地方，直接使用以下 Markdown 代码即可

```markdown
[点击这里下载文件](/files/test.pdf)
```

更详细的讨论，可以参考[这里](https://github.com/hexojs/hexo/issues/1724)。

## 使用 Hexo 输入 LaTeX 公式遇到的奇怪 bug

使用 Hexo 输入公式的时候，遇到一个奇怪的 bug，提示的错误信息是：

> FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
> Template render error: (unknown path) \[Line 1, Column 10\] expected variable end

经过查找，找到了对这个 bug 的一个说明，参见[这里](https://github.com/hexojs/hexo/issues/2064)，同时也感谢提这个 issue 的人，他也在自己的博客里[提到了这个问题](https://scjiaer.github.io/mathjax-atom-hexo/)，赞赏这种探索精神。具体来说就是，写 LaTeX 公式的时候，两个花括号 `{` 不能紧挨着，中间必须有空格隔开，否则使用 `hexo generate` 的时候就会出现上述的错误提示信息。官方目前还没有修复这个 bug，写公式的时候一定要注意。

# 博客写作相关问题

## 生成博客的时候遇到编译错误

生成博客的时候，遇到下面的跟 YAML 有关的错误提示：

>YAMLException: can not read a block mapping entry; a multiline key may not be an implicit key at line 5, column 1:

这个原因是 YAML 对格式有严格要求，key 后面的冒号和 value 之间之间 **必须用空格隔开**，不能连起来写，加上空格即可解决这个问题。

## 一篇博客使用多个 categories 和 tags

如何生成 tags 和 categories 页面，参考[这篇](http://qiufengyu.me/2016/10/14/next-theme-advanced/)，然后一篇博文如果想要指定多个 tags，或者使用多个 categories，直接谷歌 “hexo multiple tags” 就能找到答案了，值得注意的是 categories 是层级结构的，写在后面的 category 名字被认为是前一类的子类，但是 tags 没有层级结构，各个 tag 之间是平等的。

## 图片相关

### 使用云存储存放图片

图片可以放到 `D:/hexo/blog/source/_data` 目录下，然后在 Markdown 文件中使用相对路径引用，但是由于博客托管在 Github，仓库容量有限制，图片数量多了，会占据不小的空间，因此最好选择云存储来存放图片。我用的是[七牛](https://www.qiniu.com/)，有 10 G 免费空间，用来存放图片应该足够了。

### 如何向七牛云存储上传图片

首先新建一个存储空间，然后点击「内容管理」，然后点击「文件上传」即可。参考的[这里](http://jingyan.baidu.com/article/495ba841092a5338b20ede5c.html)。采用这种方式，每次上传图片都要打开网页，然后上传文件，拷贝图片的外链，太耗费时间，可以使用 [极简图床](https://jiantuku.com/#/) 来加快贴图的速度。它还提供了 Chrome 浏览器的插件，可以方便上传图片。上传图片需要设置好自己的七牛帐号，进入极简图床的主界面，点击右上角的 “设置按钮” 进入七牛云存储的设置界面，如下图，

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/17-12-17/4793797.jpg" title="进入设置七牛云存储界面" style="float: middle;">
</p>

然后进入了如下界面，

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/17-12-17/16756508.jpg" width="400">
</p>

具体设置可以参考 [官方说明](https://jiantuku.com/help/faq.html?src=footer)。

### 如何设置图片大小

有多种方法可以实现，最笨的办法就是人工缩放到具体大小，，七牛本身提供了多种图片处理方法，具体参见[七牛官方文档](https://developer.qiniu.com/dora/api/the-advanced-treatment-of-images-imagemogr2)。例如下面的示例代码：

```html
<img src="https://blog-resource-1257868508.file.myqcloud.com/file_transfer_speed_test.jpg"
         title="不同应用在不同情况下的传输速度"
         style="float: middle;"
         >
```

### 如何居中图片

直接使用 HTML 的 `<img>` tag 即可，例如  `<img src="some_img.jpg">`。在网上还看到一种[使用 `<div>` 来居中图片](https://www.zhihu.com/question/23378396)的方法，但是发现生成的网页中图片与上下文间距太大，遂放弃。

## 换行与分段有问题

写好文章，预览的时候，发现本该是一个段落，不该分行的地方突然分行了，也就是页面上一行还没到头，就开始了第二行，因此段落的排版显得非常丑，在网上找了一圈好像也没有找到到底什么原因。后面突然意识到，Markdown 文件与 LaTeX 文件不同，即使文字在一起，中间没有空行，也不会被认为是一个段落，一段文章在写  `*.md`  文件的时候应该是一行，不应该人为使用回车分段，如果使用了回车，那么在生成的网页中对应的位置就会有一个换行。

## 博客首页设置为显示每篇博文一部分而不是全文

NexT 官方的推荐的方式是在需要截断的地方使用 `<!--more-->`，当然也可以使用[另外两种方式](http://theme-next.iissnan.com/faqs.html#read-more)。

## 如何在一篇博文引用另外一篇博文

最简单的方式就是找到要引用的博文对应的网页地址，按照引用普通网页的方式引用即可，另外也可以使用 Hexo 提供的 post link tag 功能，引用文章，假如有一篇文章的题目是`hello_world.md`，那么在博文中引用的方法如下：

```
{% post_link hello_world %}
```

参考[这里](https://github.com/hexojs/hexo/issues/2176)。


## 如何在博客的题目中使用冒号

如果博客的题目（title）包含冒号，在渲染的时候回出现问题，因为 YAML 格式中冒号属于分隔 key 和 value 的分隔符，如果需要在题目中使用冒号，可以把题目用引号包围，参见[这里](https://github.com/hexojs/hexo/issues/834)。下面给出一个示例：

```yaml
title: "An Introduction to Listary: A Blazing Fast Search Tool"
```

# 更新 Hexo 以及 NexT 主题

## Update Hexo

Updating Hexo is easy. Go to your blog root (on my computer, it is D:\hexo\blog). Issue the following command:

```bash
npm install hexo
```

Now check that your hexo is updated successfully by running

```bash
hexo --version
```

## Update NexT

I followed [the official guide](https://github.com/iissnan/hexo-theme-next#update) on how to update NexT but without success. After doing what is instructed, I can not successfully generate the site.

You can try the brute-force way. Backup all your settings for the NexT theme, then remove the directory which contains the NexT theme. Then `git clone` the newest repo into your computer. After that, modify the theme configuration file based on your backup file. I think it is the most simple way. No bell and whistles.

### NexT development discontinued?

According to [this post](https://github.com/iissnan/hexo-theme-next/issues/2061), the creator of NexT theme seems to have discontinued his maintenance of this repo. So one of the contributor ([ivan-nginx](https://github.com/ivan-nginx)) to this theme decide to create a new orgnization repo named [hexo-theme-next](https://github.com/theme-next) and continue to update the theme there.

## 使用新的 theme-next

正如前面所说，NexT 的原开发者由于某种不可知原因，已经很长时间没有对 NexT repo commit 过了，所以其他开发者 fork 了 NexT，重新开了一个 repo。从原 NexT 过渡到现在的 NexT 相当顺畅，相当于重新安装了一个新的主题，直接把原主题的配置拷贝过去就可以，改动很小。具体参见[这里](https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/UPDATE-FROM-5.1.X.md)的更新说明。
