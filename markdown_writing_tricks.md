---
title: "Markdown Writing Tricks"
date: 2020-06-01T22:44:14+08:00
tags: [Markdown]
categories: [Note]
---

Some of the tricks when writing Markdown files.

<!--more-->

# How to color text

<a name="color_text"></a> To color some text, we can use the [font tag](https://www.w3schools.com/tags/tag_font.asp), which is deprecated in HTML5.

```html
<font color="red">red text</font>
```

We can also use the [span tag](https://www.w3schools.com/tags/tag_span.asp):

```html
<span style="color:blue">this is blue text</span>.
```

To color entire paragraph, the [p tag](https://www.w3schools.com/tags/tag_p.asp) can
be used:

```html
<p style="color:red">This is a paragraph.</p>
```

# How to center align image and set image size

To center align image and set image size, we can combine the p tag and [img tag](https://www.w3schools.com/tags/tag_img.asp):

```html
<p align="center">
<img src="URL" width="800">
</p>
```

The above HTML code will center the image and also set its width to 800 pixels.

# Create collapsible content

Sometimes, it is handy to create some collapsible content to make the post
concise. Like what is shown below.

<details>
<summary><font size="2" color="red">Click to show the code.</font></summary>

```
import random

nums = list(range(100))
print(random.choices(nums, k=10))
```
</details>

We can use the [detail tag](https://www.w3schools.com/tags/tag_details.asp) to
achieve this:

```````
<details>
<summary><font size="2" color="red">Click to show the code.</font></summary>

```
import random

nums = list(range(100))
print(random.choices(nums, k=10))
```
</details>
```````

# Create anchor for text

Sometimes, we want to anchor a position in the post so that when the reader
click its reference, they will be brought to the referred position. This can be
achieved by using [anchor tag](https://www.w3schools.com/tags/tag_a.asp). For
example, click [here](#color_text) to go to first section.

The code to achieve this is:

```markdown
# <a name="color_text"></a>How to color text?

click [here](#color_text) to go to first section.
```

This works both for Markdown headers and normal text in some Markdown flavor.

For some Markdown variant, to go to a header, remove all punctuations in the
header title and turn all text in header to lowever case and replace space with
`-`, click [here](#how-to-color-text) to go to section 1.

# Literal backticks in inline code or code blocks

## Backtick in ininlie code block

To use backticks inside inline code blocks, we can use more backticks as the openning
and closing inline code delimiter. For example, to produce `` foo` ``, use the
following:

```
`` foo` ``
```

## How to render three backticks in code blocks

Since the usual delimiter for code block is three backticks, using three
literal backticks inside the usual three-backtick code block will result in
rendering problems.

To fix this problem we can use more backticks as the code block delimiter.

# References #

+ [Apply color in Markdown](https://stackoverflow.com/q/35465557/6064933).
+ [Center align image](https://stackoverflow.com/a/12118349/6064933).
+ [Anchored text in Markdown](https://stackoverflow.com/a/7015050/6064933).
+ [Collapsible content in Markdown](https://gist.github.com/pierrejoubert73/902cc94d79424356a8d20be2b382e1ab).
+ Backticks
    + [Escape backtick in inline code block](https://meta.stackexchange.com/q/82718/359653).
    + [Fenced code block inside fenced code block](https://stackoverflow.com/a/49268657/6064933).
