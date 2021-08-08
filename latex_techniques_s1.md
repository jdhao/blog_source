---
title: "Nifty LaTeX Techniques -- Series 1"
date: 2019-10-22 22:06:14+0800
tags: [font, 字体]
categories: [LaTeX]
---

# Change font size

By default, LaTeX provides several command to change the font size to
predefined size. Those command include `\scriptsize`, `\large`, `\Huge` etc.
However, even the font size provided by the `\Huge` command may not be large
enough.

<!--more-->

In this case, we can use the `\fontsize` command to change the font size to
what we want. The signature of `\fontsize` command is:

```latex
\fontsize{<font_size>}{<baseline_skip>}
```

The first parameter is the font size and the second parameter is [baseline
skip](https://tex.stackexchange.com/a/371986/114857). The default font used by
LaTeX does not support arbitrary font size, and we have to use another font
such as [Latin Modern](https://en.wikipedia.org/wiki/Computer_Modern#Latin_Modern).
A minimal example is the follows:

```latex
\documentclass{article}
% use the Latin Modern font
\usepackage{lmodern}
\begin{document}
% turn off page numbering
\pagenumbering{gobble}

{\flushleft \fontsize{50}{10}\selectfont 50pt}

{\flushleft \fontsize{100}{10}\selectfont 100pt}

{\flushleft \fontsize{150}{10}\selectfont 150pt}

{\flushleft \fontsize{200}{10}\selectfont 200pt}

\end{document}
```

In the above code, `\selectfont` is used to make the font change take effect.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20191022220824.png" width="400">
</p>

## References

+ [Font size not available in LaTex](https://tex.stackexchange.com/q/58087/114857)?
+ [Change font size to arbitrary size in LaTeX](http://www.sascha-frank.com/latex-font-size.html).
+ [Change font size in LaTeX](https://texblog.org/2012/08/29/changing-the-font-size-in-latex/).
+ [Artitrary font size](https://en.wikibooks.org/wiki/LaTeX/Fonts#Arbitrary_sizes).
+ [Font larger than Huge in LaTeX](https://tex.stackexchange.com/a/716/114857)?
+ [Parameters of \fontsize command](https://tex.stackexchange.com/q/322256/114857).

# Produce vertical text

Sometimes, we want to produce a vertical text line. This can be achieved by
using the `\rotatebox` box command provided by the
[graphicx](https://ctan.org/pkg/graphicx?lang=en) package. A small runnable
script is provided below:

```latex
% !TEX TS-program = xelatex
\documentclass{article}
\usepackage{graphicx}

\begin{document}
% turn off page numbering
\pagenumbering{gobble}

\rotatebox[origin=c]{90}{\fontsize{50}{5}\selectfont \textbf{Test text.}}
\end{document}
```

Another way to rotate texts is to use the `rotating` package, which provides a
`turn` environment to rotate texts to certain angles:

```latex
% !TEX TS-program = xelatex
\documentclass{article}
\usepackage{lmodern}
\usepackage{rotating}

\begin{document}
% turn off page numbering
\pagenumbering{gobble}

% rotate the text to 90 degrees counterclockwise
\begin{turn}{90}
    \fontsize{50}{5}\selectfont \textbf{Test text.}
\end{turn}

\end{document}
```

## References

+ [Rotate text in LaTeX](https://tex.stackexchange.com/a/93799/114857).
+ [Rotate text with rotating package](https://en.wikibooks.org/wiki/LaTeX/Rotations).
