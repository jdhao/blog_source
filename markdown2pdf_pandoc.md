---
title: "Converting Markdown to Beautiful PDF with Pandoc"
date: 2019-05-30 17:02:57+0800
tags: [Pandoc, Sublime-Text, PDF, Markdown, font, LaTeX, Unicode]
categories: [tools]
---

Over the past few years, I have been using some dedicated note-taking software
to manage my notes. But all these tools I have tried are unsatisfactory: they
are either slow or cumbersome when I want to search my notes. Finally, I
decided to take my notes in Markdown and convert them to PDF using Pandoc for
reading. In this post, I will summarize how I do it.

本文的中文版本参见 [这里](https://jdhao.github.io/2017/12/10/pandoc-markdown-with-chinese/)。

<!--more-->

Taking our notes in Markdown has several advantages:

+ We can edit the Markdown files with our favorite editor, for example,
Sublime Text, which means more efficient editing and pleasant writing
experience.

+ Since a Markdown file is a textual file, we can search it using powerful search tool such as `grep` or [`ripgrep`](https://github.com/BurntSushi/ripgrep).
+ We can covert the Markdown files to various formats such as PDF, HTML, epub,
mobi etc., for better reading experience, with the help of [Pandoc](https://pandoc.org/).
+ The notes are all text files and are small in size, which means
easier and faster syncing or backup between your native PC and the cloud
service you use.

In this post, I would like to share how to generate beautiful PDF files from
Markdown and give solutions to the issues I have encountered during the
process.

# Prerequisite #

Before we begin, you need to make sure that you have installed the following
tools:

+ First, [Pandoc](https://github.com/jgm/pandoc/releases). After installation, you should add the path of the Pandoc executable to the system `PATH` variable.

+ TeX distribution. Please make sure that TeX has been installed on your system. You can use either [TeX Live](https://www.tug.org/texlive/) or [MiKTeX](https://miktex.org/) or [MacTeX](https://tug.org/mactex/) base on your platform. You may need to set up the `PATH` variable[^1].

+ A powerful text editor. One of my favorite is [Sublime Text](https://www.sublimetext.com/). You can also choose to use [VS Code](https://code.visualstudio.com/) or even [Neovim](https://jdhao.github.io/2019/01/15/markdown_edit_preview_nvim/).

# Generating PDF from Markdown with Pandoc #

## Background ##

For those who are not familiar with Pandoc, Pandoc is a powerful tool for
converting document between different formats. It is called the Swiss knife of
document converter. There are actually two steps involved in converting
Markdown files to PDF:

1. Markdown files are converted to LaTeX source files.
2. Pandoc invokes the `pdflatex`, `xelatex` or other TeX command and converts `.tex` source file to the final PDF file.

Because I often use non-ASCII characters in my files and my Markdown files use
quotation, table and other complex format, I have met a few problems during the
conversion process. In the following text, I will introduce how to solve these
issues.

## How to Handle Languages other than English ##

By default, Pandoc uses `pdflatex` command to generate PDF files, which can not
handle Unicode characters well. You will encounter errors when you try to
convert Markdown files containing Unicode characters to PDF files.

In order to handle Unicode characters, we need to use `xelatex` command
instead. For the [CJK languages](https://en.wikipedia.org/wiki/CJK_characters), you need to use `CJKmainfont` option to give
the proper font which supports the language you are using[^5]. In this post, I will
use the Chinese language as an example.

On Windows systems, for Pandoc version above 2.0, you can use the following
command to generate the PDF file:

```
pandoc --pdf-engine=xelatex -V CJKmainfont="KaiTi" test.md -o test.pdf
```

In the above command, `KaiTi` is the name of a font which supports the Chinese
characters. How do we find a font supporting a particular language? First,
you need to know the [language code](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes)
for the language you are using. For example, the language code for Chinese is
`zh`. Then, use the `fc-list` command to look up the fonts which support this
language[^2]:

```
fc-list :lang=zh
```

The output of command is like the following:

![](https://blog-resource-1257868508.file.myqcloud.com/18-1-4/51615620.jpg "opt title")

The font name is the string after the font location. Since the font names may
contain spaces, you need to quote the font name when you want to use a
particular font, e.g., `-V CJKmainfont="Source Han Serif CN"`.

In Pandoc version 2.0, [`--pdf-engine` option replaces the old `--latex-engine`
option](https://github.com/shd101wyy/markdown-preview-enhanced/issues/665). On
Linux systems where the Pandoc version may be old, the above command will not
work. You need to use the following command instead[^3]:

```
pandoc --latex-engine=xelatex -V mainfont='WenQuanYi Micro Hei' test.md -o test.pdf
```

On Linux systems, the way to find the font supporting your language is the same
as Windows system.


# Issues and techniques #

## Add title, author and date info

Pandoc supports adding these info via its [YAML header extension](https://pandoc.org/MANUAL.html#extension-yaml_metadata_block).
We can easily add the document title, author and date info like this:

```YAML
---
title: "My demo title"
author: "jdhao"
date: 2021-06-27
---
```

## Block quote, table and list are not correctly rendered ##

The reason is that Pandoc requires that you leave an empty line before block
quote, list and table environment. If the lines in the block quote are not
correctly broken, i.e., all the lines are merged as one paragraph, you can add
a space after each line to solve this issue.

## Add highlight to block code ##

Pandoc supports block code syntax highlighting for many languages and offers
several highlight themes. To list the highlight themes that Pandoc provides,
use the following command:

```
pandoc --list-highlight-styles
```

To list all the languages that Pandoc supports, use the following command:

```
pandoc --list-highlight-languages
```

To use syntax highlighting for different languages, you need to specify the
language in the block quote and use `--highlight-style`, e.g.,:

```
pandoc --pdf-engine=xelatex --highlight-style zenburn test.md -o test.pdf
```

In the above command, we use the `zenburn` theme, I also recommend using the
`tango` or `breezedark` theme.

## Use numbered section and add TOC ##

By default, there is no table of contents (TOC) in the generated PDF and no
numbers in the headers[^4]. To add TOC, use the `--toc` option; to add section
numbers, use the `-N` option. A complete example is as follows:

```
pandoc --pdf-engine=xelatex --toc -N -o test.pdf test.md
```

## Add colors to links ##

According to the Pandoc user guide, we can add colors to different links via
the `colorlinks` option to separate the links from the normal texts:

> colorlinks
>     add color to link text; automatically enabled if any of linkcolor,
filecolor, citecolor, urlcolor, or toccolor are set

To customize the color of different types of links, Pandoc offers different
options:

> linkcolor, filecolor, citecolor, urlcolor, toccolor
>     color for internal links, external links, citation links, linked URLs,
and links in table of contents, respectively: uses options allowed by xcolor,
including the dvipsnames, svgnames, and x11names lists

For example, to set the URL color to `NavyBlue` and set the TOC color to `Red`,
we can use the following command:

```
pandoc --pdf-engine=xelatex -V colorlinks -V urlcolor=NavyBlue -V toccolor=Red test.md -o test.pdf
```

Note that the `urlcolor` option will not color the raw URL links in the text.
To color those raw links, you can enclose those links with `<>`, e.g.,
`<www.google.com>`.

## Change the PDF margin ##

The default margin for the generated PDF is too large. According to the [Pandoc
FAQ](https://pandoc.org/faqs.html#how-do-i-change-the-margins-in-pdf-output),
you can use the following option to change the margin:

```
-V geometry:"top=2cm, bottom=1.5cm, left=2cm, right=2cm"
```

The complete command is:

```
pandoc --pdf-engine=xelatex -V geometry:"top=2cm, bottom=1.5cm, left=2cm,
right=2cm" -o test.pdf test.md
```

## Error when using backslash inside Markdown ##

In ordinary Markdown format, it is fine to use backslash characters inside the files.
But Pandoc interpret the backslash and string after it as LaTeX command by default.
As a result, you may encounter [weird errors](https://tex.stackexchange.com/q/407744/114857)
when trying to compile Markdown files containing backslash characters.
Based on discussions [here](https://github.com/jgm/pandoc/issues/1637) and [here](https://github.com/jgm/pandoc/issues/2790),
the solution is to make Pandoc treat the Markdown file as normal Markdown files and not interpret the LaTeX command.
You need to use the following flag:

```
pandoc -f markdown-raw_tex
```

Or you can use two backslash to represent a literal backslash, e.g., ` \\sometxt `.
If you want to express a LaTeX command, enclose the command with inline code block, like this: ` \textt{} `.

## Add background color to inline code ##

In translating Markdown source file to TeX files,
Pandoc use the [` \texttt`](https://www.tutorialspoint.com/tex_commands/texttt.htm) command to represent the inline code.
So inline code has no background color in the generated PDF files.
To increase the readability of inline code, we can modify the `\texttt` command to add background color to text.

First, we need to create a file named `head.tex` and add the following settings to it:

```latex
% change background color for inline code in
% markdown files. The following code does not work well for
% long text as the text will exceed the page boundary
\definecolor{bgcolor}{HTML}{E0E0E0}
\let\oldtexttt\texttt

\renewcommand{\texttt}[1]{
  \colorbox{bgcolor}{\oldtexttt{#1}}
  }
```

When converting Markdown files, use the `-H` option to refer the `head.tex`
file, e.g.,:

```
pandoc --pdf-engine=xelatex -H head.tex test.md -o test.pdf
```

In the generated PDF, the inline code will have grey background color. You can
change the background color as you wish.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190530235523.png" width="400">
</p>


## Change the default style of block quote ##

By default, when converting Markdown to PDF, Pandoc use the
[`quote`](https://tex.stackexchange.com/q/33219/114857) environment for
Markdown block quotes. The texts inside quotation are only indented, making it
hard to recognize the environment.

We can create a custom environment to add background color and indentation to
the quotation environment. Add the following setting to `head.tex`:

```latex
\usepackage{framed}
\usepackage{quoting}

\definecolor{bgcolor}{HTML}{DADADA}
\colorlet{shadecolor}{bgcolor}
% define a new environment shadedquotation. You can change leftmargin and
% rightmargin as you wish.
\newenvironment{shadedquotation}
 {\begin{shaded*}
  \quoting[leftmargin=1em, rightmargin=0pt, vskip=0pt, font=itshape]
 }
 {\endquoting
 \end{shaded*}
 }

%
\def\quote{\shadedquotation}
\def\endquote{\endshadedquotation}
```

When you want to convert Markdown file to PDF, you can use the following
command:

```
pandoc -H head.tex test.md -o test.pdf
```

The produced PDF is like the following:

<p align="center">
<img
src="https://blog-resource-1257868508.file.myqcloud.com/20191118222418.png">
</p>

### References

+ [Change background color of quotation](https://tex.stackexchange.com/a/435632/114857).
+ [Redefining existing environment in LaTeX](https://tex.stackexchange.com/questions/291235/modifying-an-existing-environment-but-keeping-the-name).

## Put the settings to `head.tex` ##

You may have noticed the clumsiness if you try to customize a lot of settings.
When converting Markdown to PDF, we often need to use several settings. If you
specify all these options on the command line, it would be time consuming and
cumbersome to edit. A good way is ease the issue is to put some command
settings to `head.tex` file and refer to this file during Markdown file conversion.

For example, we can put the settings related to margin, inline code
highlighting, and link color to `head.tex`:

<details>
<summary><font color="red">Click to see the code.</font></summary>

```latex
\usepackage{fancyvrb,newverbs}
\usepackage[top=2cm, bottom=1.5cm, left=2cm, right=2cm]{geometry}

% change background color for inline code in
% markdown files. The following code does not work well for
% long text as the text will exceed the page boundary
\definecolor{bgcolor}{HTML}{E0E0E0}
\let\oldtexttt\texttt

\renewcommand{\texttt}[1]{
\colorbox{bgcolor}{\oldtexttt{#1}}
}

%% color and other settings for hyperref package
\hypersetup{
    bookmarksopen=true,
    linkcolor=blue,
    filecolor=magenta,
    urlcolor=RoyalBlue,
}
```
</details>

## Nested list level exceed the limit ##

One reader [Karl Liu](https://disqus.com/by/disqus_q3bv7H1Y4D/) mentioned that
if the nested list level exceeds 6, you will encounter the following error when
trying to generate PDF file:

> ! LaTeX Error: Too deeply nested.

More detailed discussions can be found
[here](https://github.com/jgm/pandoc/issues/2922). The solution proposed is to
add the following settings in `head.tex`:

<details>
<summary><font color="red">Click to see the code.</font></summary>

```latex
\usepackage{enumitem}
\setlistdepth{9}

\setlist[itemize,1]{label=$\bullet$}
\setlist[itemize,2]{label=$\bullet$}
\setlist[itemize,3]{label=$\bullet$}
\setlist[itemize,4]{label=$\bullet$}
\setlist[itemize,5]{label=$\bullet$}
\setlist[itemize,6]{label=$\bullet$}
\setlist[itemize,7]{label=$\bullet$}
\setlist[itemize,8]{label=$\bullet$}
\setlist[itemize,9]{label=$\bullet$}
\renewlist{itemize}{itemize}{9}

\setlist[enumerate,1]{label=$\arabic*.$}
\setlist[enumerate,2]{label=$\alph*.$}
\setlist[enumerate,3]{label=$\roman*.$}
\setlist[enumerate,4]{label=$\arabic*.$}
\setlist[enumerate,5]{label=$\alpha*$}
\setlist[enumerate,6]{label=$\roman*.$}
\setlist[enumerate,7]{label=$\arabic*.$}
\setlist[enumerate,8]{label=$\alph*.$}
\setlist[enumerate,9]{label=$\roman*.$}
\renewlist{enumerate}{enumerate}{9}
```
</details>

Add the `-H head.tex` option when compiling PDF files.

## Add anchors in Markdown ##

I try to use anchors in Markdown following the [discussion
here](https://stackoverflow.com/questions/5319754/cross-reference-named-anchor-in-markdown).
Unfortunately, in the generated PDF, the anchor does not work: when I click the
linking text, there is no jump to the destination page.

Instead, we should use the attribute to give an id to the location we want to
jump to and then refer to it in other places using the id. Here is an example:

```markdown
## head 2 {#my_head2}

Please click [here](#my_head2) to go to head 2.
```

## How to resize image ##

We can also resize images using the attribute. You can specify width or height
in absolute pixel values or as percentage relative to the page or column width.
For example:

```markdown
you can use absolute pixel values

![test image](test.jpg){width=128px}

or you can use relative value to the page or column width

![test image](test.jpg){width=90%}
```

## How to start a new page for each section ##

By default, when you generate PDF from Markdown files, each section started by
the level 1 header do not start from the new page: it will continue from where
the last section ends. If you want to start a new page when a new section
starts, you need to add the following settings to `head.tex` according to
[this](https://superuser.com/questions/601469/getting-chapters-to-start-on-a-new-page-in-a-pandoc-generated-pdf):

```
\usepackage{titlesec}
\newcommand{\sectionbreak}{\clearpage}
```

But when I tried to produce PDF with the updated `head.tex` files, I got an
error:

```
! Argument of \paragraph has an extra }.
<inserted text>
                \par
l.1290 \ttl@extract\paragraph

pandoc: Error producing PDF
```

According to discussions
[here](https://stackoverflow.com/questions/42916124/not-able-to-use-titlesec-with-markdown-and-pandoc),
it is because Pandoc's default LaTeX redefines the `\pragraph` command and we
have to disable this behaviour. We need to use `-V subparagraph` when invoking
the `pandoc` command:

```bash
pandoc -V subparagraph -o file.pdf file.md
```

### Start a new page only after TOC

What if we only want to add a new page after the table of contents page? An
easy way is to hack the `\tableofcontents` command. Add the following command
to `head.tex` to redefine `\tableofcontents` command:

```
\let\oldtoc\tableofcontents
\renewcommand{\tableofcontents}{\oldtoc\newpage}
```

In the above command, we first save the old command and then redefine it to
avoid [recursive calls](https://tex.stackexchange.com/questions/47351/can-i-redefine-a-command-to-contain-itself).

## Line breaks

In Markdown, you can create a hard linebreak by appending two spaces after a line:

```
hello<space><space>
world
```

Using space at the line end for formatting is annoying since it cause the
trailing whitespace warning. The space characters are also not visible.

Pandoc also provides an
[`escaped_line_breaks`](https://pandoc.org/MANUAL.html#extension-escaped_line_breaks)
extension. You can use ` \ ` in the end of a line followed by newline character
to represent a hard line break:

```
hello\
world
```

## Images references

Pandoc supports LaTeX command inside Markdown, to refer to an image, you can use the LaTeX syntax:

```
![my great image\label{fig-my-great-img}](image_great.jpg)

In Fig.\ref{fig-my-great-img}, I show a great image.
```

# Generate PDF using Sublime Text build system #

It is cumbersome to switch to the terminal and use Pandoc to generate the PDF
files and preview it after finishing writing the Markdown files. To simply the
process, I use the Sublime Text build system for building PDF file and
previewing. I use the light-weight [Sumatra PDF
reader](http://www.sumatrapdfreader.org/free-pdf-reader.html) for PDF
previewing.

An example build system is shown below:

<details>
<summary><font color="red">Click to see the code.</font></summary>

```
{
    "shell_cmd": "pandoc --pdf-engine=xelatex --highlight-style=zenburn -V colorlinks -V CJKmainfont=KaiTi \"${file}\" -o \"${file_path}/${file_base_name}.pdf\" ",
    "file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
    "working_dir": "${file_path}",
    "selector": "text.html.markdown",

    "variants":
    [
        {
            "name": "Convert to PDF and Preview",
            "shell_cmd": "pandoc --pdf-engine=xelatex --highlight-style=zenburn -V colorlinks  -V CJKmainfont=KaiTi \"${file}\" -o \"${file_path}/${file_base_name}.pdf\"  &&SumatraPDF \"${file_path}/${file_base_name}.pdf\" ",
            // "shell_cmd":   "start \"$file_base_name\" call $file_base_name"
        }
    ]
}
```
</details>

You can download the build system and `head.tex` file
[here](https://gist.github.com/jdhao/90fcc239cfa3bba77e10185580e16170).


## Pandoc is not recognized on Windows systems ##

For some reasons unknown to me, when using the above build systems to compile
Markdown files, I encountered the following errors:

> ‘pandoc’ is not recognized as an internal or external command, operable
program or batch file.

After looking up the Sublime Text documentation, I find that we can add `path`
in the build system. So I adjust the above build system:

<details>
<summary><font color="red">Click to see the code.</font></summary>

```
{
    "shell_cmd": "pandoc --pdf-engine=xelatex --highlight-style=zenburn -V colorlinks -V CJKmainfont=\"Source Han Serif SC\" \"${file}\" -o \"${file_path}/${file_base_name}.pdf\" ",
    "path": "C:/Users/east/AppData/Local/Pandoc/;%PATH%",
    "file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
    "working_dir": "${file_path}",
    "selector": "text.html.markdown",

    "variants":
    [
        {
            "name": "Convert to PDF and Preview",
            "shell_cmd": "pandoc --pdf-engine=xelatex --highlight-style=zenburn -V colorlinks  -V CJKmainfont=\"Source Han Serif SC\" \"${file}\" -o \"${file_path}/${file_base_name}.pdf\"  &&SumatraPDF \"${file_path}/${file_base_name}.pdf\" ",
            "path": "C:/Users/east/AppData/Local/Pandoc/;%PATH%",
            // "shell_cmd":   "start \"$file_base_name\" call $file_base_name"
        }
    ]
}
```
</details>

After that, everything goes well.

# Conclusion #

In this post, I give a complete summary on how to generate beautiful PDF files
from Markdown. I also share several solutions to the issues I have encountered.
I hope that you can now generate beautiful PDF from Markdown files.

# References #

+ [Dealing with Chinese in Pandoc](https://pandoc.org/faqs.html#i-get-a-blank-document-when-i-try-to-convert-a-markdown-document-in-chinese-to-pdf-using-pandoc--o-test.pdf-test.markdown)
+ [Pandoc's handling of block quote](https://pandoc.org/MANUAL.html#block-quotations)
+ [Pandoc syntax highlighting](https://pandoc.org/MANUAL.html#syntax-highlighting)
+ [colors provided by dvipsnames](https://en.wikibooks.org/wiki/LaTeX/Colors#The_68_standard_colors_known_to_dvips)
+ [Pandoc section number](https://stackoverflow.com/questions/24208889/how-to-specify-numbered-sections-in-pandocs-front-matter/47738183#47738183)
+ [Pandoc command not found](https://stackoverflow.com/questions/47533072/g-is-not-recognised-as-an-internal-or-external-command-in-sublime-text)
+ Anchors in Pandoc
    + https://github.com/jgm/pandoc/issues/1299
    + https://github.com/jgm/pandoc/issues/684
+ [Resize image](https://superuser.com/questions/796565/correctly-sizing-png-images-in-markdown-with-pandoc-for-html-pdf-docx)
+ [start a new page after toc](https://tex.stackexchange.com/questions/47639/redefine-tableofcontents-to-start-a-new-page-after-the-toc)
+ Pandoc hard line break
    + https://stackoverflow.com/questions/48329455/pandoc-not-maintaining-newlines-from-txt-file-to-word-file
    + https://stackoverflow.com/questions/28283008/preserve-line-breaks-in-title-using-pandoc
+ [Image reference in Pandoc](https://stackoverflow.com/questions/9434536/how-do-i-make-a-reference-to-a-figure-in-markdown-using-pandoc)

[^1]: Make sure that you can use `latex` command on the command line.

[^2]: For Windows system, you can use `fc-list` command after installing the TeX Live full edition. For Linux systems, this command is usually pre-installed.

[^3]: Tested on Pandoc version 1.12.3.1.

[^4]: Only the font size varies for different header levels.

[^5]: For other languages, you need to use `--mainfont` option.
