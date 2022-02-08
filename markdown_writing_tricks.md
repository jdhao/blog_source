---
title: "Markdown Writing Tricks"
date: 2020-06-01T22:44:14+08:00
tags: [Markdown]
categories: [Note]
---

<details>
<summary><font size="2" color="red">update log</font></summary>

+ <font color="blue">2022-02-08: Add sections on using CSS with Markdown.</font>
</details>

In this post, I share some of the tricks that will Make the Markdown file better and more beautiful.

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

# Style text with CSS

To create stylish component in Markdown, we may need a little bit CSS.
Below is a `div` element styled with CSS.

<div style="color: #ff0000; background-color: #ffbaba; padding: 10px; border: 3px dashed; border-radius: 10px;">
    this is some demo text
</div>

We can use inline style or internal style to reproduce this effect.

## Inline style

Using style attribute inside a HTML tag. The code is:

```html
<div style="color: #ff0000; background-color: #ffbaba; padding: 10px; border: 3px dashed; border-radius: 10px;">
    this is some demo text
</div>
```

## Internal style

We can also inline CSS in Markdown using HTML [`style` tag](https://www.w3schools.com/tags/tag_style.asp).
The code is:

```
<style>
.demo {
    color: #ff0000;
    background-color: #FFBABA;
    padding: 10px;
    border: 3px dashed;
    border-radius: 10px;
}
</style>

<div class="demo">
    this is some demo text
</div>
```

It will create exactly the same styled text.

# Creat warning/info/success/error boxes

Using the techniques discussed above. We can create beautiful text boxes with different styles.

Using inline style:

```
<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #3c763d; background-color: #dff0d8; border-color: #d6e9c6;">
I am a success message
</div>

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #a94442; background-color: #f2dede; border-color: #ebccd1;">
I am an error message
</div>

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #31708f; background-color: #d9edf7; border-color: #bce8f1;">
<strong>&#9432; Info:<br/></strong> I am a info message
</div>

<div style="padding: 15px; border: 1px solid transparent; border-color: transparent; margin-bottom: 20px; border-radius: 4px; color: #8a6d3b;; background-color: #fcf8e3; border-color: #faebcc;">
<strong>&#9888; Warning:<br/></strong> I am a warning message
</div>
```
The produced text boxes are like this:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202202082242801.jpg" width="600">
</p>


Using internal style (ideas are from [here](https://csshint.com/css-alert-box/)):

```html
<style type="text/css">
@import url('//maxcdn.bootstrapcdn.com/font-awesome/4.2.0/css/font-awesome.min.css');

.info-msg,
.success-msg,
.warning-msg,
.error-msg {
  margin: 5px 0;
  margin-bottom: 20px;
  padding: 10px;
  border-radius: 5px 5px 5px 5px;
  border: 2px solid transparent;
  border-color: transparent;
}
.info-msg {
  color: #059;
  background-color: #BEF;
}
.success-msg {
  color: #270;
  background-color: #DFF2BF;
}
.warning-msg {
  color: #9F6000;
  background-color: #FEEFB3;
}
.error-msg {
  color: #D8000C;
  background-color: #FFBABA;
}

</style>

<div class="info-msg">
  <i class="fa fa-info-circle"></i>
  This is an info message.
</div>

<div class="success-msg">
  <i class="fa fa-check"></i>
  This is a success message.
</div>

<div class="warning-msg">
  <i class="fa fa-warning"></i>
  This is a warning message.
</div>

<div class="error-msg">
  <i class="fa fa-times-circle"></i>
  This is a error message.
</div>
```

The produce text boxes are like:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202202082245523.jpg" width="600">
</p>


# References #

+ [Apply color in Markdown](https://stackoverflow.com/q/35465557/6064933).
+ [Center align image](https://stackoverflow.com/a/12118349/6064933).
+ [Anchored text in Markdown](https://stackoverflow.com/a/7015050/6064933).
+ [Collapsible content in Markdown](https://gist.github.com/pierrejoubert73/902cc94d79424356a8d20be2b382e1ab).
+ Backticks
    + [Escape backtick in inline code block](https://meta.stackexchange.com/q/82718/359653).
    + [Fenced code block inside fenced code block](https://stackoverflow.com/a/49268657/6064933).
+ CSS:
    + https://www.w3schools.com/html/html_css.asp
    + markdown css: https://stackoverflow.com/a/66407948/6064933
    + https://www.w3schools.io/file/markdown-css/
