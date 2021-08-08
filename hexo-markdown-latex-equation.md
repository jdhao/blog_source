---
title: "Hexo 书写 LaTeX 公式时的一些问题及解决方法"
date: 2017-10-06 23:59:00 +0800
tags: [Hexo, LaTeX, NexT, Markdown, MathJax, Pandoc]
categories: [Blog]
---

在写作博文的时候，有时候需要输入一些数学公式，如果公式过长，我们通常希望把方程分成多行显示，在 LaTeX 中，我们可以使用 `aligned` 环境来书写多行公式，但是写出来的 Markdown 经过 Hexo 的处理以后显示不正确，原因何在？原来这与 Hexo 的渲染引擎有关系。

<!--more-->

# 开启公式支持

Hexo 的自带的 Markdown 引擎并不支持 LaTeX 公式。 但是 MathJax 支持，因此首先要启用 MathJax 才能渲染 LaTeX 公式。如果你已经安装了 [NexT theme](http://theme-next.iissnan.com/)，开启 MathJax 支持非常容易，在最新版的NexT 主题的 `_config.yml` 文件里，找到 MathJax 相关部分，使用以下配置，

```yaml
math:
  enable: true
  # Default(true) will load mathjax/katex script on demand
  # That is it only render those page who has 'mathjax: true' in Front Matter.
  # If you set it to false, it will load mathjax/katex srcipt EVERY PAGE.
  per_page: false
  engine: mathjax
```

但是，由于 LaTeX 中的一些符号在 Markdown 也中有特殊含义，例如 两个 `_` 在 Markdown中用来表示斜体（italic），因此两者的配合使用会产生一些问题。

# 从一个例子说起

假如我们想要显示下面的方程，

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/multiline_equation_correct.png">
</p>

如果使用如下的 LaTeX 代码

```latex
\begin{align}
x &= a +b+c+d\\
 &= e +f\\
 &= g
\end{align}
```

使用 Hexo 渲染以后，得到的结果如下图所示，

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/multiline_equation_wrong.png">
</p>

可以看到，换行并未起作用，这是因为 Hexo 对 Markdown 文件的处理实际上分为两个步骤：

1. Hexo 中的 Markdown 引擎把 Markdown 变为 html 文件
2. MathJax 负责解释 html 的数学公式

第一步中，由于 backslash 在 Markdown 属于特殊字符，用于字符转义，所以两个 backslash 经过 Markdown 引擎处理，只剩下一个，等到 MathJax 引擎处理时，实际上 MathJax 只看到一个 backslash，MathJax 把它当作 LaTeX 中的空格，因此我们见到了上图所示的渲染结果。

知道了渲染错误的原因，解决办法也很简单，就是用四个 backslash 代替两个 backslash，确保 MathJax 的引擎看到的是两个 backslash，也就是把上述代码变成

```latex
\begin{align}
x &= a +b+c+d\\\\
 &= e +f\\\\
 &= g
\end{align}
```

这时产生的公式如下图所示，

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/multiline_equation_not_perfect.png">
</p>

如果想要去掉公式的编号，在 LaTeX 中，我们通常会选择[加星号的 `align` 环境](https://tex.stackexchange.com/questions/2597/how-do-i-turn-off-equation-auto-numbering)，但是注意到星号 （star）在 Markdown 也有特殊含义(一对单星号表示斜体，一对双星号表示粗体)，因此必须对星号进行转义，才能确保 MathJax 见到星号，正确的代码是

```
\begin{align\*}
x &= a +b+c+d\\\\
 &= e +f\\\\
 &= g
\end{align\*}
```

如果按照上面的方法在 Markdown 文件中写公式，我们将会十分痛苦，并且写出来的 LaTeX 公式不能被标准的 LaTeX 处理引擎识别，因此这个解决办法是非常糟糕的解决办法。有没有更好的办法？答案是：有的！

# 解决方法

我试用了多款号称能够解决这个问题的 hexo renderer，例如 `hexo-renderer-markdown-it-plus` 以及 `hexo-math`，效果都差强人意。最后发现使用 [hexo-renderer-pandoc](https://github.com/wzpan/hexo-renderer-pandoc) 十分靠谱。使用这个 renderer 之前请确保你已经[安装了Pandoc](https://github.com/jgm/pandoc/releases)，然后卸载 Hexo 自带的 renderer，安装 Pandoc renderer:

```bash
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-pandoc --save
```

## 注意事项

如果你使用这款 Pandoc renderer，那么书写 Markdown 时候需要遵循 [Pandoc 对 Markdown 的规定](https://pandoc.org/MANUAL.html#pandocs-markdown)。

有一些比较明显的需要注意的事项：正常的文字后面如果跟的是[`list`](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#lists), [`table`](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#tables)或者[`quotation`](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#blockquotes)，文字后面需要空一行，如果不空行，这些环境将不能被 Pandoc renderer 正常渲染。

另外，文中的 URL 使用 Pandoc 渲染以后是普通的文本格式，不能点击，可以通过用`<>` 包围 URL 的方式把 URL 变成可点击的 URL。

---

# 参考

+ [Discussion about correct way to use align environment in Markdown](https://math.meta.stackexchange.com/questions/3514/using-the-align-environment).
+ [Hexo 插入多行数学公式](http://kubicode.me/2016/03/18/Hexo/The-Trick-about-Hexo-Support-MutliLine-Equation-using-MathJax/).
+ [How to make URL clickable](https://stackoverflow.com/questions/35414121/pandoc-automatically-convert-urls-into-hyperlinks)
