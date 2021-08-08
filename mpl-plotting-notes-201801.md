---
title: "Working with Fonts in Matplotlib"
date: 2018-01-18 21:13:00 +0800
tags: [Matplotlib, font, 字体, PDF]
categories: [Python]
---

This post continues my [previous post](https://jdhao.github.io/2017/11/16/matplotlib-plotting-notes/)
on various tips and tricks collected during the process of using Matplotlib.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-18/39303776.jpg"
         title="Different math symbols produced by Matplotlib and LaTeX"
         style="float: middle;">

<!--more-->

# Working with math font

## Default math font is ugly?

If you try to play with the [builtin LaTeX-style math
support](https://matplotlib.org/users/mathtext.html) in the recent version of
Matplotlib, e.g., version 2.0. You may have noticed that the [default math font
is ugly](https://github.com/matplotlib/matplotlib/issues/7921/) compared to the
nicer look of math symbols produced by LaTeX. See the figure above for an
example.

It is because [the default math font has
changed](https://matplotlib.org/users/dflt_style_changes.html#math-text) since
Matplotlib version 2.0. If you want to use the LaTeX-style font. You need to
change the default math font to [computer
modern](https://en.wikipedia.org/wiki/Computer_Modern). There are two ways to
achieve to this.

### Change the math font temporarily

You can change math font in your plotting script so that it only takes effect
in your script. To do this, add the following statement to your script,

```python
import matplotlib as mpl
mpl.rcParams['mathtext.fontset'] = 'cm'
```

### Change the math font globally

You can also change the math font options in your [matplotlibrc
files](https://matplotlib.org/users/customizing.html#matplotlib-rcparams). This
will affect all your scripts afterwards. This file is usually located in
`$HOME` under the directory `~/.config/matplotlib`. But its location may vary.
Use the following command to find where rc file is on your system,

```python
import matplotlib as mpl
print(mpl.matplotlib_fname())
```

Then find the line

```
#mathtext.fontset : dejavusans
```

Uncomment it and change it to

```
mathtext.fontset : cm
```

Now, the math font will look much better. All your script will use the new
settings.

## Mix math text with regular text

If we want to use line break in math expression, it will be useless since
everything inside raw text is interpretted verbatim. In order to combine math
expression with regular text, we can concatenate them together.

```python
import matplotlib.pyplot as plt

fig, ax = plt.subplots()

ax.text(0.5, 0.5, r"$\mu=1$" + "\n" + r"$\sigma=2$",
        transform=ax.transAxes)

plt.show()
```

The produced image is

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-2/65111903.jpg"
         title="Mix line break with math expression"
         style="float: middle;">

# Type 3 font used by Matplotlib PDF backend

If you use the default Matplotlib setting to produce a PDF file and then
include it in your paper, chances are that you will encounter errors when you
test the PDF for compliance with the IEEE conference standard. The error shows
that Type 3 font has been used in your paper. That is because Matplotlib use
Type 3 font to produce PDF file by default.

The disadvantage of Type 3 font is that [it can not be edited by a PDF
editor](https://stackoverflow.com/questions/5956182/cannot-edit-text-in-chart-exported-by-matplotlib-and-opened-in-illustrator)
such as Adobe Acrobat Pro. The "advantage" is that the produced PDF is small in
size.

You can change this behavior in your script to use Type 42 font instead. This
setting will work locally:

```python
import matplotlib as mpl
mpl.rcParams['pdf.fonttype'] = 42
```

If you want to change this settings globally[^1], you can edit your
matplotlibrc file like the example of changing math font.

Find the line,

```
#pdf.fonttype       : 3       # Output Type 3 (Type3) or Type 42 (TrueType)
```

Uncomment it and change it to

```
pdf.fonttype       : 42        # Output Type 3 (Type3) or Type 42 (TrueType)
```

## Reducing the PDF size when Type-42 fonts are used

I have come to find that if you use Chinese font in your image and use the Type
42 font, the [produced PDF size will be much
larger](https://github.com/matplotlib/matplotlib/issues/11303). If you do not
want to edit the produce pdf file, I think using Type 3 font may be fine.

Some publisher explicitly require that Type 3 font is not included. Then we
need to reduce the size of PDF in which Type 42 fonts are used. According to
solutions [here](https://stackoverflow.com/a/14384178/6064933), we can convert
the PDF file to Ghostscript file and convert back to PDF.

```bash
pdf2ps file.pdf file.ps
ps2pdf -dPDFSETTINGS=/prepress file.ps file-optimized.pdf
```

The option `-dPDFSETTINGS` defines the quality of the produced PDF. Possible
options and explanations are listed below:

|  option               | description |
|:-----------------------|:--------------------------------|
|-dPDFSETTINGS=/screen  | (screen-view-only quality, 72 dpi images)|
|-dPDFSETTINGS=/ebook   | (low quality, 150 dpi images)|
|-dPDFSETTINGS=/printer | (high quality, 300 dpi images)|
|-dPDFSETTINGS=/prepress| (high quality, color preserving, 300 dpi imgs)|
|-dPDFSETTINGS=/default | (almost identical to /screen)|

The conversion process will reduce the PDF size considerably. In my case, the
PDF size went from 5.3M to just 29k. The quality of the optimized PDF is
acceptable. So this is an viable solution.

P.S.: The [`pdffonts`](https://stackoverflow.com/a/614694/6064933) shipped by
most Linux system can be used to detect which fonts are embedded in your PDF
files.
[Here](https://superuser.com/questions/183313/view-list-of-embedded-fonts-in-pdf-file-with-preview)
is another example to use `pdffonts` command.

# References

+ <https://stackoverflow.com/questions/2660319/putting-newline-in-matplotlib-label-with-tex-in-python>
+ <https://stackoverflow.com/questions/10284847/python-matplotlib-annotate-line-break-with-and-without-latex>

[^1]: It means that you do not need to set the fonttype in every source file.
