---
title: Why Did XeLaTeX Suddenly Run Insanely Slow?
date: 2017-03-06 18:43:24 +0800
tags: [LaTeX, font]
categories: [LaTeX]
---

Today I found that compiling LaTeX source is insanely slow(took me several
minutes to compile a simple file). After a lot of trial and error, I found the
reason.<!--more-->

# The problem description

I was trying to compile a tex file on my Windows 8.1 machine and observed that
the compilation speed was insanely slow. I created a test file with only a few
Chinese character and compiled it. The compilation time is around 1.15 minutes.
At first, I thought it was probably due to the Chinese character. So I replaced
the Chinese character with simple English words. After that, I recompiled the
file, but found no noticeable speedup in compilation time. The follwing is my
testing code:

```
\documentclass{article}
\usepackage{xeCJK}
\begin{document}
测试 % change the Chinese character to English does not make a difference
\end{document}
```

If I comment out `\usepackage{xeCJK}`, the compilation process also sped up.

# The solution

After some search on the Internet, I found [a relevant
post](https://goo.gl/oCVd82) on the Stack Exchange. It turned out that it was
caused by a new install of font. After a font is installed or removed, we
should manually run `fc-cache -f` from command line to rebuild the TeX Live
font cache. After this rebuilding process, compiling files with `xelatex`
should be normal again.

----

# References

+ [Discussion of this on the TeX Live mailing list](http://tug.org/pipermail/tex-live/2017-March/039768.html)
