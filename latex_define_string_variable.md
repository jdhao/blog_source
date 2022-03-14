---
title: "Define A String Variable in LaTeX"
date: 2022-03-14T12:56:48+08:00
draft: false
tags: []
categories: [LaTeX]
---

Like other programming languages, the best way to to use a value multiple times in LaTeX is to define a variable for it.
Later when you want to change the value, you only need to change it once. Here is how to do it.

<!--more-->

# Use `\newcommand`

We can use `\newcommand` to define a new command for the text like this:

```tex
\newcommand{\toyCommand}{some text}
```

In the main, to insert the text, we can call this command:

```tex
We can use \toyCommand{} like this.

Or we can use \toyCommand\ like this.
```

Note that the double bracket `{}` or escaped space `\ ` after the custom command is necessary,
without which, the inserted text and text after it will stick together with no space.

If we just want to use `\toyCommand`, we can use [xspace package ](https://ctan.org/pkg/xspace?lang=en) and add space when defining the command:

```tex
\usepackage{xspace}

\newcommand{\toyCommand}{some text\xspace}
```

Note that `xspace` do have some drawbacks, see [discussion here](https://tex.stackexchange.com/a/86620/114857).

# Use `\def`

We can also use `\def` to define a variable and use it later[^1].

```tex
\def \myVar {some text}

We can use \myVar{} like this.
```

# References

+ Space after LaTeX commands: https://tex.stackexchange.com/q/31091/114857
+ Is there any way I can define a variable in LaTeX: https://stackoverflow.com/q/1211888/6064933

[^1]: It may override existing macro without warning, so use it with care.
