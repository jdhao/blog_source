---
title: "How to Insert LaTeX Equations in Microsoft Office Word and PowerPoint"
date: 2015-10-13 20:04:00 +0800
tags: [LaTeX, Office]
categories: [tools]
---

In this post, I would like to share how to insert beautiful latex equations
into Microsoft Office Word and PowerPoint.

<!--more-->

I assume that one of the LaTeX distributions has been installed on your system.
On Windows, either [TeX Live](https://www.tug.org/texlive/) or [MiKTeX](https://miktex.org/) is fine.

# Insert LaTeX equation in Word

## Free software

There is a free plugin called
[texsword](https://sourceforge.net/projects/texsword/) which can generate good
quality LaTeX equations in Word.

### How to install

Download the package from sourceforge, extract it. You will see something like
this,

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-22/60547302.jpg">
</p>


For Office 2007 and later versions, copy the file `texsword.dotm` to the
directory `C:\Users\<USER_NAME>\AppData\Roaming\Microsoft\Word\STARTUP`,
`<USER_NAME>` represents your user name. In case that you can not locate
this directory in this way, here is how you can find it in your computer:

- Click on the File ribbon (Office 2010) or Office button (Office 2007)
- Click Options
- Click Advanced
- Scroll down and find a button File Locations. Click on it.
- Find Startup folder in the list of the Word system folders.
- Remember where it is and close the Word. Copy the file.

If everything went well, upon next Word launch, you will see the TeXsword
buttons in the Add-Ins ribbon.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-22/1349293.jpg">
</p>


### How to use

To insert equations, click the button with `TeX` label (see image above). There
is also a shorcut to insert LaTeX equations: just press
<kbd>ALT</kbd>+<kbd>T</kbd>.

For more usages, please consult the `texword_readme.docx` file bundled with the
`texsword.dotm`. It has detailed instructions on how to use it.

## Commercial and shareware

There is a well-known software MathType which support LaTeX syntax. The only
problem is that the license fee is too high for a student. I have found another
software which is cheaper and can do the same thing. It is called
[AxMath](http://www.amyxun.com/) and is developed by a Chinese company.

You can use the software free with limited features[^1] or you can purchase a
license for about \$6.12 (￥39) to activate full feature.

After you have installed this software, a ribbon will appear in the Word menu.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-23/78958653.jpg">
</p>

You can insert inline, display and numbered equations. To change to the LaTeX
input mode, click the button in the lower right of panel or press shortcut key
<kbd>Ctrl</kbd>+<kbd>Tab</kbd>.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-23/59342078.jpg" width="400">
</p>

It support LaTeX command auto-completion once you start typing ` \ `, which is
convenient.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-23/57107489.jpg" width="400">
</p>

After finishing equations, you can use <kbd>Shift</kbd>+<kbd>Enter</kbd> to
render the equations.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-23/74376869.jpg" width="400">
</p>

# Insert LaTeX equation in PowerPoint

## Install IguanaTex

To type LaTeX in PowerPoint, you can use
[IguanaTex](http://www.jonathanleroux.org/software/iguanatex/index.html).
Download it from
[here](http://www.jonathanleroux.org/software/iguanatex/download.html).
Following instructions on the website (there is a `installation` part) and set
up IguanaTex properly. Then a ribbon with name `IguanaTeX` will appear in
PowerPoint,

<p align="center">
<img
src="https://blog-resource-1257868508.file.myqcloud.com/18-1-22/49850008.jpg">
</p>

## How to use

Type the LaTeX code just as you do in LaTeX,

<p align="center">
<img
src="https://blog-resource-1257868508.file.myqcloud.com/18-1-22/62790029.jpg">
</p>

Click the `Generate` button to generate LaTeX equations. If you want to update
the equation, select the generated image, then click the `Edit LaTeX display`
button under the `IguanaTeX` ribbon. After finishing editting, click the button
`Regenerate` to regenerate the equation.

## How use Chinese character inside math mode?

By default the math mode in IguanTeX does not support Chinese character even if
you have wrapped the characters with `\text{}` command.

To use Chinese, you have to do several things:

+ Install [GhostScript](https://www.ghostscript.com/)[^2].
+ Install [Imagemagick](https://imagemagick.org/index.php).
+ Use `pdflatex` instead of the default `latex` command.

You also need to the set the path of GhostScript and Imagemagick executable
files correctly in the IguanaTex settings, as shown below

<p align="center">
<img
src="https://blog-resource-1257868508.file.myqcloud.com/20190606224802.png">
</p>

I test the following script and it works as expected:

```latex
\documentclass[UTF8]{ctexart}
\usepackage{amsmath}
\pagestyle{empty}

\begin{document}

$\text{比例} = \frac{\text{部分}}{\text{全部}}$

\end{document}
```

[^1]: But I think the free version is good enough for normal use.

[^2]: If you have installed the full version of Tex Live, you don not need to
  install GhostScript. It is packaged with Tex Live under
  `TEXLIVEROOT/YEAR/tlpkg/tlgs/bin` and the name is something like
  `gswin32c.exe` if you use Windows.

