---
title: "Note on font size"
date: 2020-08-04 23:01:55+0800
tags: [font]
categories: [technique]
---

# Relationship between point and pixel size

In digital typography, the size of text is commonly specified in points (pt).
What is a point? A point is 1/72 of an inch (25.4 mm), i.e., 0.3528 mm
(25.4/72). Therefor, point is a physical unit that has absolute value.


[Pixels](https://en.wikipedia.org/wiki/Pixel) are the smallest unit on screen
to display images and graphics. It is not a physical unit. So there is no fixed
relationship between point and pixel.

## ppi

Their relationship depends on the ppi (pixel per inch), A.K.A., [pixel
density](https://en.wikipedia.org/wiki/Pixel_density).

How do we calculate ppi? It is simple. We need screen resolution and screen
size. Screen resolution refers to how many pixels are there for its width and
height. Ppi is equal to diagonal pixel number divided by screen size in inches.
Here, we use pixel number in diagonal direction because the screen size are
commonly measured diagonally (see
[here](https://en.wikipedia.org/wiki/Display_size#History)).

Take the iPhone 11 for an example. According to [wikipedia](https://en.wikipedia.org/wiki/IPhone_11#Display):

>The iPhone 11 has a 6.1 in (15.5 cm) IPS LCD, unlike the Pro models which have
OLED displays.
>
>The resolution is 1792 × 828 pixels (1.5 megapixels at 326 ppi) with a maximum
brightness of 625 nits and a 1400:1 contrast ratio.

So the screen ppi of iPhone 11 is:

```
sqrt(1792*1792 + 828*828)/6.1 = 323.6
```

which is pretty close to the provided ppi value. There is also [online
tools](https://www.calculatorsoup.com/calculators/technology/ppi-calculator.php)
to help us calculate the ppi values.

Once we know ppi value of a device, we can then know the relationship between
point and pixel:

```
pixel = point/72 * ppi
```

Ref:

+ https://graphicdesign.stackexchange.com/q/199/113389
+ [font size in pixels](https://stackoverflow.com/q/1059101/6064933)
+ https://www.onlineprinters.co.uk/magazine/font-sizes/

# font size for web design

It seems that for web design, there is another set of specification. There is
relationship between pt and px. But `px` here is not referring to pixels in the
digital device (computer screens for example). It is called CSS reference
pixel. Since I am not a web developer right now, I will not dive too deep into
this topic.

Ref:

+ https://webplatform.github.io/docs/tutorials/understanding-css-units/
