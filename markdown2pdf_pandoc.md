---
title: "Converting Markdown to Beautiful PDF with Pandoc"
date: 2019-05-30 17:02:57+0800
tags: [Pandoc, Sublime-Text, PDF, Markdown, font, LaTeX, Unicode]
categories: [tools]
---

本文的中文版本参见 [这里](https://jdhao.github.io/2017/12/10/pandoc-markdown-with-chinese/)。

Over the past few years, I have been using some dedicated note-taking software to manage my notes.
However, all these tools I have tried are unsatisfactory: they are either slow or cumbersome in terms of note searching.
Finally, I decided to take my notes in Markdown and convert them to PDF using Pandoc for reading.
In this post, I will summarize how I do it.

<!--more-->

Taking our notes in Markdown format has several benefits:

+ We can edit the Markdown files with our favorite editor, for example, neovim, Sublime Text,
which means more efficient editing and pleasant writing experience.
+ Since a Markdown file is a textual file, we can search it using powerful tool such as `grep` or [`ripgrep`](https://github.com/BurntSushi/ripgrep).
+ We can covert the Markdown files to various formats such as PDF, HTML, epub, mobi etc.,
for better reading experience, with the help of [Pandoc](https://pandoc.org/).
+ The notes are all text files and are small in size, which means
easier and faster syncing or backup between your native PC and the cloud service.

In this post, I would like to share how to generate beautiful PDF files from Markdown.
I will also give solutions to the issues I have encountered during the process.

# Prerequisite #

Before we begin, we need to make sure that the following tools are installed:

+ First, [Pandoc](https://github.com/jgm/pandoc/releases). After installation, you should add the path of the Pandoc executable to the system `PATH`.
+ TeX distribution. Please make sure that TeX has been installed on your system.
You can use either [TeX Live](https://www.tug.org/texlive/) or [MiKTeX](https://miktex.org/) or [MacTeX](https://tug.org/mactex/) base on your platform. You may need to set up the `PATH` variable[^1].
+ A powerful text editor. One of my favorite is [Neovim](https://jdhao.github.io/2019/01/15/markdown_edit_preview_nvim/). You can also choose to use VS Code or Sublime Text.

# Generating PDF from Markdown with Pandoc #

## Background ##

For those who are not familiar with Pandoc, Pandoc is a powerful tool for converting document between different formats.
It is called the Swiss knife of document converter.
There are actually two steps involved in converting a Markdown file to a PDF file:

1. The Markdown source file is converted to a LaTeX source file.
2. Pandoc invokes the `pdflatex` or `xelatex` or other TeX command and converts the `.tex` source file to a PDF file.

When converting Markdown to LaTeX, pandoc uses a template file.
We can run command `pandoc -D latex |less` to check the template file used.

Because I often use non-ASCII characters, quotations, tables, and other complex elements in my Markdown files,
I have met a few problems during the conversion process.
In the following sections of this post, I will share how to solve these issues.

## How to Handle Languages other than English ##

By default, Pandoc uses `pdflatex` to generate PDF files, which can not handle Unicode characters well.
We will encounter errors when we convert Markdown files containing Unicode characters to PDF files.

In order to handle Unicode characters, we need to use `xelatex` instead.
For the [CJK languages](https://en.wikipedia.org/wiki/CJK_characters), we need to use `CJKmainfont` option to specify the proper font that supports the language we are using[^5].
In this post, I will use the Chinese language as an example.

On Windows systems, for Pandoc 2.0 and above, we can use the following command to generate the PDF file:

```
pandoc --pdf-engine=xelatex -V CJKmainfont="KaiTi" test.md -o test.pdf
```

In the above command, `KaiTi` is the name of a font that supports the Chinese.
How do we find a font supporting a particular language?
First, we need to know the [language code](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) for the language in use.
For example, the language code for Chinese is `zh`.
Then, use `fc-list` command to look up the fonts that support this language[^2]:

```
fc-list :lang=zh
```

The output of this command is like the following:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-4/51615620.jpg" width="600">
</p>

The font name is the string after the font location.
Since a font name may contain spaces, we need to quote it when we want to use a particular font,
e.g., `-V CJKmainfont="Source Han Serif CN"`.

In Pandoc version 2.0, [`--pdf-engine` option replaces the old `--latex-engine` option](https://github.com/shd101wyy/markdown-preview-enhanced/issues/665).
On Linux systems where the Pandoc version is old, the above command will not work.
We need to use the following command instead[^3]:

```
pandoc --latex-engine=xelatex -V mainfont='WenQuanYi Micro Hei' test.md -o test.pdf
```

On Linux, the way to find the font supporting your language is the same as Windows.

# Issues and techniques #

## Debug issues effectively

As discussed in the previous section, markdown to PDF is a two-step process.
The key to get what we want is to directly check the intermediate LaTeX file that pandoc generates.
Then we can patch or update the command that pandoc uses.

To check the generated LaTeX file, we can add the `-s` (means `standalone`) and output LaTeX instead:

```
pandoc --pdf-engine=xelatex \
    --highlight-style kate \
    --table-of-contents \
    --number-sections \
    -s \
    test.md -o test.tex
```

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

The reason is that Pandoc requires that you leave an empty line before block quote, list and table environment.

If the lines in the block quote are not correctly broken, i.e., all the lines are merged as one paragraph,
we can add a space after each line to solve this issue.

## Add highlight to block code ##

Pandoc supports block code syntax highlighting for many languages and offers several highlight themes.
To list the highlight themes that Pandoc provides, run the following command:

```
pandoc --list-highlight-styles
```

To list all the languages that Pandoc supports, run the following command:

```
pandoc --list-highlight-languages
```

To use syntax highlighting for different languages,
we need to specify the language in the block quote and use `--highlight-style`, e.g.,:

```
pandoc --pdf-engine=xelatex --highlight-style zenburn test.md -o test.pdf
```

In the above command, we use the `zenburn` theme, I also recommend using the `tango` or `breezedark` theme.

## Add numbered sections and TOC ##

By default, there is no table of contents (TOC) in the generated PDF and no numbers in the headers[^4].
To add TOC, use the `--toc` option; to add section numbers, use the `-N` option.
A complete example is as follows:

```
pandoc --pdf-engine=xelatex --toc -N -o test.pdf test.md
```

## Add colors to links ##

According to the Pandoc user guide, we can add colors to different links via the [`colorlinks`](https://pandoc.org/MANUAL.html#links) option:

> colorlinks
>     add color to link text; automatically enabled if any of linkcolor, filecolor, citecolor, urlcolor, or toccolor are set

To customize the color of different types of links, Pandoc offers different options:

> linkcolor, filecolor, citecolor, urlcolor, toccolor
>     color for internal links, external links, citation links, linked URLs,
>     and links in table of contents, respectively: uses options allowed by
>     xcolor, including the dvipsnames, svgnames, and x11names lists

For example, to set the URL color to `NavyBlue` and set the TOC color to `red`,
we can use the following command:

```
pandoc --pdf-engine=xelatex -V colorlinks -V urlcolor=NavyBlue -V toccolor=red test.md -o test.pdf
```

Note that the `urlcolor` option will not color the raw URL links in the text.
To color those raw links, you can enclose those links with `<>`, e.g., `<www.google.com>`.

## Change the PDF margin ##

The default margin for the generated PDF is too large.
According to the [Pandoc FAQ](https://pandoc.org/faqs.html#how-do-i-change-the-margins-in-pdf-output), you can use the following option to change the margin:

```
-V geometry:"top=2cm, bottom=1.5cm, left=2cm, right=2cm"
```

The complete command is:

```
pandoc --pdf-engine=xelatex -V geometry:"top=2cm, bottom=1.5cm, left=2cm, right=2cm" -o test.pdf test.md
```

## Error when using backslash inside Markdown ##

In ordinary Markdown format, it is fine to use backslash characters inside the files.
However, Pandoc interpret the backslash and string after it as LaTeX command by default.
As a result, you may encounter [weird errors](https://tex.stackexchange.com/q/407744/114857) when trying to compile Markdown files containing backslash characters.
Based on discussions [here](https://github.com/jgm/pandoc/issues/1637) and [here](https://github.com/jgm/pandoc/issues/2790),
the solution is to make Pandoc treat the Markdown file as normal Markdown files and not interpret the LaTeX command.
You need to add the following flag:

```
pandoc -f markdown-raw_tex
```

Or you can use two backslash to represent a literal backslash, e.g., ` \\sometxt `.
If you want to express a LaTeX command, enclose the command with inline code block, like this: ` \textt{} `.

## Add background color to inline code ##

In translating Markdown source file to TeX files, Pandoc use the ` \texttt{}` command to represent the inline code.
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

When converting Markdown files, use the `-H` option to refer the `head.tex` file, e.g.,:

```
pandoc --pdf-engine=xelatex -H head.tex test.md -o test.pdf
```

In the generated PDF, the inline code will have grey background color.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190530235523.png" width="400">
</p>

## Change the default style of block quote ##

By default, when converting Markdown to PDF, Pandoc use the [`quote`](https://tex.stackexchange.com/q/33219/114857) environment for Markdown block quotes.
The texts inside quotation are only indented, making it hard to recognize the environment.

We can create a custom environment to add background color and indentation to
the quotation environment. Add the following setting to `head.tex`:

```tex
% change style of quote, see also https://tex.stackexchange.com/a/436253/114857
\usepackage[most]{tcolorbox}

\definecolor{linequote}{RGB}{224,215,188}
\definecolor{backquote}{RGB}{249,245,233}
\definecolor{bordercolor}{RGB}{221,221,221}

% change left border: https://tex.stackexchange.com/a/475716/114857
% change left margin: https://tex.stackexchange.com/a/457936/114857
\newtcolorbox{myquote}[1][]{%
    enhanced,
    breakable,
    size=minimal,
    left=10pt,
    top=5pt,
    frame hidden,
    boxrule=0pt,
    sharp corners=all,
    colback=backquote,
    borderline west={2pt}{0pt}{bordercolor},
    #1
}

% redefine quote environment to use the myquote environment, see https://tex.stackexchange.com/a/337587/114857
\renewenvironment{quote}{\begin{myquote}}{\end{myquote}}
```

When you want to convert Markdown file to PDF, you can use the following command:

```
pandoc -H head.tex test.md -o test.pdf
```

The produced PDF is like the following:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202207182325510.png" width="600">
</p>

### References

+ [Change background color of quotation](https://tex.stackexchange.com/a/435632/114857).
+ [Redefining existing environment in LaTeX](https://tex.stackexchange.com/a/291238/114857).

## Tricks: put the settings to `head.tex` ##

You may have noticed the clumsiness if you try to customize a lot of settings.
When converting Markdown to PDF, we often need to use several settings.
If we specify all these options on the command line, it would be time consuming and cumbersome to edit.
A good way to ease the issue is to put some settings to `head.tex` and refer to this file during Markdown file conversion.

For example, we can put the settings related to margin, inline code highlighting to `head.tex`:

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
```
</details>

## Nested list level exceed the limit ##

One reader [Karl Liu](https://disqus.com/by/disqus_q3bv7H1Y4D/) mentioned that if the nested list level exceeds 6,
you will encounter the following error when trying to generate PDF file:

> ! LaTeX Error: Too deeply nested.

More detailed discussions can be found [here](https://github.com/jgm/pandoc/issues/2922).
The solution proposed is to add the following settings in `head.tex`:

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

## Add anchors in Markdown ##

I try to use anchors in Markdown following the [discussion here](https://stackoverflow.com/q/5319754/6064933).
Unfortunately, in the generated PDF, the anchor does not work:
when I click the linking text, there is no jump to the destination page.

Instead, we should use the attribute to give an id to the location we want to jump to,
and refer to it in other places using the id.
Here is an example:

```markdown
## head 2 {#my_head2}

Please click [here](#my_head2) to go to head 2.
```

## How to resize image ##

We can also resize images using the attribute.
We can specify width or height in absolute pixel values or as percentage relative to the page or column width.
For example:

```markdown
you can use absolute pixel values

![test image](test.jpg){width=128px}

or you can use relative value to the page or column width

![test image](test.jpg){width=90%}
```

## How to start a new page for each section ##

By default, when we generate PDF from Markdown files,
each section started by the level 1 header does not start from a new page.
It will continue from where the last section ends.
If we want to start a new page when a new section starts,
we need to add the following settings to `head.tex` according to [this](https://superuser.com/q/601469/736190):

```
\usepackage{titlesec}
\newcommand{\sectionbreak}{\clearpage}
```

But when I tried to produce PDF with the updated `head.tex` files, I got an error:

```
! Argument of \paragraph has an extra }.
<inserted text>
                \par
l.1290 \ttl@extract\paragraph

pandoc: Error producing PDF
```

According to discussions [here](https://stackoverflow.com/a/43659664/6064933), it is because Pandoc's default LaTeX redefines the `\pragraph` command.
We have to disable this behaviour.
We need to use `-V subparagraph` when invoking the `pandoc` command:

```bash
pandoc -V subparagraph -o file.pdf file.md
```

### Start a new page only after TOC

What if we only want to add a new page after the table of contents page?
An easy way is to hack the `\tableofcontents` command.
Add the following command to `head.tex` to redefine `\tableofcontents` command:

```
\let\oldtoc\tableofcontents
\renewcommand{\tableofcontents}{\oldtoc\newpage}
```

In the above command, we first save the old command and then redefine it to avoid [recursive calls](https://tex.stackexchange.com/q/47351/114857).

## Line breaks

In Markdown, you can create a hard linebreak by appending two spaces after a line:

```
hello<space><space>
world
```

Using space at the line end for formatting is annoying since it causes the trailing whitespace warning.
The space characters are also not visible.

Pandoc also provides an [`escaped_line_breaks`](https://pandoc.org/MANUAL.html#extension-escaped_line_breaks) extension.
You can use ` \ ` in the end of a line followed by newline character to represent a hard line break:

```
hello\
world
```

## Images references

Pandoc supports LaTeX command inside Markdown, to refer to an image, we can use the LaTeX syntax:

```
![my great image\label{fig-my-great-img}](image_great.jpg)

In Fig.\ref{fig-my-great-img}, I show a great image.
```

# Generate PDF using Sublime Text build system #

It is cumbersome to switch to the terminal and use Pandoc to generate the PDF files and preview it after finishing writing the Markdown files.
To simply the process, I use the Sublime Text build system for building PDF file and previewing.
I use the light-weight [Sumatra PDF reader](http://www.sumatrapdfreader.org/free-pdf-reader.html) for PDF previewing.

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

You can download the build system and `head.tex` file [here](https://gist.github.com/jdhao/90fcc239cfa3bba77e10185580e16170).
An updated version of `head.tex` can be found [here](https://github.com/jdhao/pandoc_md_to_pdf/blob/master/head.tex).

## Pandoc is not recognized on Windows systems ##

For some reasons unknown to me, when using the above build systems to compile Markdown files,
I encountered the following errors:

> ‘pandoc’ is not recognized as an internal or external command, operable program or batch file.

After looking up the Sublime Text documentation, I find that we can add `path` in the build system.
So I adjust the above build system:

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

In this post, I give a complete summary on how to generate beautiful PDF files from Markdown.
I also share several solutions to the issues I have encountered.
I hope that you can now generate beautiful PDF from Markdown files.

# References #

+ [Dealing with Chinese in Pandoc](https://pandoc.org/faqs.html#i-get-a-blank-document-when-i-try-to-convert-a-markdown-document-in-chinese-to-pdf-using-pandoc--o-test.pdf-test.markdown)
+ [Pandoc's handling of block quote](https://pandoc.org/MANUAL.html#block-quotations)
+ [Pandoc syntax highlighting](https://pandoc.org/MANUAL.html#syntax-highlighting)
+ [colors provided by dvipsnames](https://en.wikibooks.org/wiki/LaTeX/Colors#The_68_standard_colors_known_to_dvips)
+ [Pandoc section number](https://stackoverflow.com/q/24208889/6064933)
+ [Pandoc command not found](https://stackoverflow.com/a/47837674/6064933)
+ Anchors in Pandoc
    + https://github.com/jgm/pandoc/issues/1299
    + https://github.com/jgm/pandoc/issues/684
+ [Resize image](https://superuser.com/a/1084323/736190)
+ [start a new page after toc](https://tex.stackexchange.com/a/47641/114857)
+ Pandoc hard line break
    + https://stackoverflow.com/a/48330289/6064933
    + https://stackoverflow.com/a/28283340/6064933
+ [Image reference in Pandoc](https://stackoverflow.com/questions/9434536/how-do-i-make-a-reference-to-a-figure-in-markdown-using-pandoc)

[^1]: Make sure that you can use `latex` command on the command line.
[^2]: For Windows system, you can use `fc-list` command after installing the TeX Live full edition. For Linux systems, this command is usually pre-installed.
[^3]: Tested on Pandoc version 1.12.3.1.
[^4]: Only the font size varies for different header levels.
[^5]: For other languages, you need to use `--mainfont` option.
