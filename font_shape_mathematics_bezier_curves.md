---
title: "The Mathematics behind Font Shapes --- Bézier Curves and More"
date: 2018-11-27 16:14:00 +0800
tags: [font, math]
categories: [technique]
markup: pandoc
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200223203853.png">
</p>

<!--more-->

Steve Jobs once said that "technology alone is not enough". He thinks that
truly great products reside on the intersection of technology and liberal arts.
He had also told a story about how he dropped Reed college and attended the
calligraphy class, which had a great impact on the font he decided to adopt on
Macintosh later[^1].

In my opinion, computer font is a great demonstration of arts and aesthetics
combined seamless with technology. In this post, I try to understand the
mathematics behind the beauty of font. Below is the [Source Han Serif
font](https://source.typekit.com/source-han-serif/) designed by Google and
Adobe.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200223204000.png" width="800">
</p>

You may have noticed already that most fonts in use nowadays can be resized to
arbitrary size and still looks good and smooth. This kind of fonts are called
outline fonts (or vector fonts), as opposed to the already deprecated [bitmap
fonts](https://en.wikipedia.org/wiki/Computer_font#Outline_font_formats). The
fonts we now use are mostly opentype fonts, which is an extension of the
truetype font format[^3].

The word "outline" means that the shape of each glyph in a font is made up of
straight and curved lines. Various fonts mainly use two major outline types ---
truetype outline and postscript outline. Truetype outlines are represented
using quadratic [Bézier
curves](https://en.wikipedia.org/wiki/B%C3%A9zier_curve#Fonts), while
postscript outlines are represented using cubic Bézier curves. Quadratic Bézier
curve is a special case for cubic Bézier curve. As a result, quadratic curves
can be converted to cubic curves without any loss of accuracy, while cubic
curves can only be approximated by several quadratic curves.

Since opentype font can either use truetype or postscript outlines, we can
convert truetype font to opentype format without loss of accuracy, but not the
other way around[^2].

## How does Bézier curves represent the font outline?

Bézier curves are parametric curves whose shapes are controlled by a parameter
`t` and some on and off curve points. In this part, we focus on quadratic
Bézier curves. The shape of quadratic curves are determined by two on-curve
points (or end points) and one off-curve point. The off-curve point is used to
control the shape of the curve. Suppose we have three points： $(A_x, A_y)$
(point A), $(B_x, B_y)$ (point B), and $(C_x, C_y)$ (point C).  A and C are the
end points and B is the control point. The parametric equation for the
underlying curve is:

$$\begin{align}
P_x &= (1-t)^2A_x + 2t(1-t)B_x + t^2C_x \\
P_y &= (1-t)^2A_y + 2t(1-t)B_y + t^2C_y \\
\end{align}$$

When parameter $t$ is changed from 0 to 1 in the above equations, we can get
all the points $(P_x, P_y)$ in the underling curve. That is, this curve can be
uniquely determined once we know the position of point A, B and C.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200223204211.png" width="400"
    title="A quadratic Bezier curve">
</p>

See the figure above for an example of quadratic B-curve. In this example, A is
$(1.0, 1.0)$, B is $(1.5, 2.0)$ and C is $(3.0, 1.5)$. The blue line is the
generated curve when we change $t$ from 0 to 1.

For a glyph in a font, its outline can be split into multiple small curves
until it can be represented by B-curves[^4], which is in turn represented by
three points. Ultimately, if we simplify things a bit, we can represent each
glyph in a font with a set of points.

In the below image, I show the outline for character `e` from a font[^5]:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200223204338.png" width="200">
</p>

In the above image, the character `e` is made up of two outlines. The red dots
are the end points of B-curves and the cross symbols are the control points.
You can see that for complex characters, the outline consists of a dozen small
curves.

## How are the characters filled?

Knowing only the glyph outline is not enough. How do we actually fill the
characters, i.e., how do you decide what is inside the character and what is
outside? In fact, there is a rule. Each point is given a unique number starting
from zero. When you traverse the outline from lower number to higher number,
the "inside" of character is always on your right. So the outer outline of a
character is numbered clockwise and the inner outline is numbered
counter-clockwise. See the below image for an example:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200223205335.png">
</p>

Furthermore, if one on-curve point is exactly in the center of two control
points, it can be omitted without loss of information. So you may see there are
several consecutive control points with no on-curve points in between. In the
above image, for example, point 7, 8, 9 and 10 are both control points. More
discussion regarding this issue can be found
[here](https://stackoverflow.com/q/20733790/6064933).

# Conclusion

In this post, I have roughly covered how font outline works, that is, how the
glyph shapes for a font are represented. But how we should display or print the
font at various size (usually measured in pt) is another long story. Hopefully,
I will try to describe that on another posts soon.

# References

+ <https://fontforge.github.io/bezier.html>
+ [Truetype outlines](http://www.truetype-typography.com/ttoutln.htm)
+ [How outline font is rendered on screen](http://mirror.informatimago.com/next/developer.apple.com/documentation/mac/Text/Text-199.html)
+ [Very good documentation about font outline and how to convert hand design to acutal font outline](https://developer.apple.com/fonts/TrueType-Reference-Manual/RM01/Chap1.html)
+ [Microsoft truetype font documentation](https://docs.microsoft.com/en-us/typography/opentype/spec/ttch01)


[^1]: You can find a detailed discussion [here](https://multimediaman.wordpress.com/2015/01/20/steve-jobs-1955-2011-fonts-and-desktop-publishing/).
[^2]: But cubic curves can be approximated using quadratic curves with arbitary precisions. See [here](https://developer.apple.com/fonts/TrueType-Reference-Manual/RM08/appendixE.html) for more discussions.
[^3]: A good read about history of fonts can be found [here](https://courses.cs.washington.edu/courses/csep590a/06au/projects/font-wars.pdf)
[^4]: You can find more discussion [here](https://developer.apple.com/fonts/TrueType-Reference-Manual/RM01/Chap1.html#necessary).
[^5]: You can use the well-known open source tool [FontForge](https://fontforge.github.io/en-US/) to check the character outlines of a font.
