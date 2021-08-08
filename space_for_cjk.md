---
title: "Space for CJK Languages (CJK 语言中的空格)"
date: 2020-05-16 22:04:23+0800
tags: [Unicode]
categories: [Note]
---

I was doing some typograph for Chinese text and want to insert a space equal to
the width of a Chinese character.

<font color="red">最近在做一些中文排版的工作，有一个需求是在中文字符之间插入空格，并且空格的宽度等于中文字符的宽度，在某些情况下这样排版显得更加美观。</font>

<!--more-->

# Ideographic space(全角空格) #

The usual space we use (`U+0020`) does not met this requirement since it is
very thin compared to Chinese characters. I do not know what this kind of space
is called or whether this character even exists. I searched the web using
Google and found that this space is called `full width space` or `ideographic
space`. It is equal to the width the CJK character so that it is aesthetically
pleasing to insert such a space when needed. The code point for ideographic
space is `U+3000` and it looks like the following:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200516220222.png" width="100">
</p>

<font color="red">我们通常使用空格键打出的空格（Unicode 编码为 `U+0020`）不满足排版的需求，因为这个空格的宽度和汉字字符宽度相比非常窄。起初我不知道是否存在这种宽度等于汉字字符宽度的空格，也不知道它叫什么。不过经过一番查找，我发现这种空格是存在的，我们可以叫它 `全角空格` 或 `表意空格`。这个空格的占的宽度等于中日韩字符的宽度，在某些情况下，如果在排版的时候插入这种空格，结果会更美观。全角空格的 Unicode 编码是 `U+3000`。</font>

<font color="red">在 Mac 系统上，我们可以使用自带的输入法插入全角空格，按下快捷键 <kbd>Shift</kbd>+ <kbd>Option</kbd> + <kbd>B</kbd>，在弹出的菜单选择 “符号”，第六个候选项就是全角空格。</font>

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200517225217.jpg" width="200">
</p>

There are other kind of spaces that I am not aware of, e.g., space (`U+2000`)
that are 1/2 of the height of a font size.

<font color="red">除了全角空格以外，Unicode 字符集实际上还规定了其他类型的空格以满足不同排版的需求。例如，编码为 `U+2000` 的字符，Unicode 规定它的宽度等于字符大小（字符高度）的一半。这些宽度不同的空格可以帮助我们在排版的时候产生更加愉悦的视觉效果，例如，[这篇文章](https://www.zhangxinxu.com/wordpress/2015/01/tips-blank-character-chinese-align/?shrink=1)展示了在前端网页设计时，利用不同空格来两端对齐长短不同的中文。</font>

# References（参考资料）

+ [A good article on Unicode spaces](http://jkorpela.fi/chars/spaces.html).
+ https://en.wikipedia.org/wiki/Whitespace_character
+ http://keithtam.net/designing-with-the-hanzi-script/
+ https://medium.com/@bobtung/best-practice-in-chinese-layout-f933aff1728f
+ https://webdesign.tutsplus.com/articles/the-complete-beginners-guide-to-chinese-fonts--cms-23444
+ [在 Mac 中输入全角空格](https://support.apple.com/zh-cn/guide/chinese-input-method/cim11841/mac).
