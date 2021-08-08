---
title: "Set up Sublime Text as Your Ultimate LaTeX Editor"
date: 2018-03-10 12:57:05 +0800
tags: [LaTeX, Sublime-Text]
categories: [technique]
---

There are a lot of [front-end
editors](https://en.wikipedia.org/wiki/Comparison_of_TeX_editors) for writing
LaTeX source, for example, [Texmaker](http://www.xm1math.net/texmaker/),
[TeXstudio](https://github.com/texstudio-org/texstudio/releases),
[WinEdt](http://www.winedt.com/). I have used TeXstudio for a while, but its
user interface is a little dull. So I decided to give [Sublime
Text](https://www.sublimetext.com/) a shot. In this post, I want to share the
configurations necessary to write and compile LaTeX source code and to preview
the produced PDF files.

<!--more-->

# Choose a TeX distribution

$\TeX$ is like a programming language, you need a compiler and other necessary
components such as packages and classes to compile your source files. For
Windows, [TeX Live](https://www.tug.org/texlive/) is an excellent choice. You
can download it [here](https://www.tug.org/texlive/acquire.html). When
installing TeX Live, you can choose the *full scheme*, which will install
everything you need on your computer.

The full installation of TeX Live may take more than half of an hour depending
on various factors. Stay patient. After installation, you should add TeX Live
executables to your system `PATH`. On my system, the directory where these
executables reside is `D:\texlive\2017\bin\win32`.

Restarting your computer may be required for `PATH` change to take effect.

# Install the LaTeXTools package

Now, you should install the
[LaTeXTools](https://packagecontrol.io/packages/LaTeXTools) package, which
provides a lot of functionalities for writing and compiling LaTeX files. If you
have installed [package control](https://packagecontrol.io/), you can install
this package using package control.

# Install Sumatra PDF reader

To preview the produced PDF files, I recommend using the well-known
light-weight [Sumatra PDF
reader](https://www.sumatrapdfreader.org/free-pdf-reader.html). After install,
we should also set its path properly.

# Check your system

After all these installations and settings, we can use the built-in function of
LaTexTools to check our system. To check if there are any issues, follow the
image below on how to do it:

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-10/17922753.jpg"
         title="Check your system"
         style="float: middle;">

The result of `Check System` is shown below:

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-10/38655968.jpg"
         title="The result of system check"
         style="float: middle;">

If there are no issues found, you can now write and compile your LaTeX source
files.

# Write and compile LaTeX source files

Use the shortcut key <kbd>Ctrl</kbd>+<kbd>B</kbd> to compile the file. You may
be prompted to choose a builder. Just choose the basic builder.

# Other packages to install

There are a few packages designed for use with LaTeXTools, for example,
[LaTex-cwl](https://github.com/LaTeXing/LaTeX-cwl) and
[LaTeXYZ](https://github.com/randy3k/LaTeXYZ). You should install them to
enhance your experience of writing LaTeX in Sublime Text.

# Configure inverse search for PDF files

[Inverse search](https://en.wikipedia.org/wiki/Inverse_search) is a handy
feature. First, compile any tex files and open it with Sumatra PDF reader. Then
go to `Settings -> Options`. In the `inverse search` part, use the following
setting:

```bash
"D:\Program Files\Sublime Text 3\sublime_text.exe" "%f:%l"
```

In the above setting, change the directory of Sublime Text executable to its
actual location on your system.

# More questions?

The LaTeXTools package has an online documentation, which is well written. You
can read it [here](https://latextools.readthedocs.io/en/latest/). 

---

# References

+ [Set up inverse search on Sumatra](https://tex.stackexchange.com/q/194645/114857).
