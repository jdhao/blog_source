---
title: "LaTeX Equation Numbering Done Right in Hexo"
date: 2018-01-25 23:19:00 +0800
tags: [Hexo, LaTeX, MathJax, NexT, Markdown]
categories: [Blog]
markup: pandoc
---

Recently I plan to transplant one of my old post from other site to this new
site (you can find the new post [here](https://jdhao.github.io/2016/01/19/back-propagation-in-mlp-explained/)).
The old post contains quite a lot of equations in LaTeX format.
With so much equations in one post, I find it really hard to refer to some equations without using proper numbering.
So I decide to figure out how to use LaTeX equation numbering properly in my post.

<!--more-->

# Enable MathJax and equation numbering

Update 2018-05-16: This feature has been incorporated into NexT and [released in version 6.3.0](https://github.com/theme-next/hexo-theme-next/releases/tag/v6.3.0).
You can skip this section safely now.

If you use [NexT theme](http://theme-next.iissnan.com/), see [here](https://jdhao.github.io/2017/10/06/hexo-markdown-latex-equation/) on how to enable MathJax.
By default, equation numbering is disable by Mathjax, you can enable equation numbering in two ways.

## First way

According to MathJax official documentation, just paste the following script
into the front of your Markdown file,

```html
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  TeX: { equationNumbers: { autoNumber: "AMS" } }
});
</script>
```

## Second way

Find the file `mathjax.swig` under theme NexT root (it is in the folder
`layout\_third-party`). Change the following snippet

```html
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      inlineMath: [ ['$','$'], ["\\(","\\)"]  ],
      processEscapes: true,
      skipTags: ['script', 'noscript', 'style', 'textarea', 'pre', 'code']
    }
  });
</script>
```

to

```html
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      inlineMath: [ ['$','$'], ["\\(","\\)"]  ],
      processEscapes: true,
      skipTags: ['script', 'noscript', 'style', 'textarea', 'pre', 'code']
    },
    TeX: {equationNumbers: { autoNumber: "AMS" }}
  });
</script>
```

Thanks to [post here](https://notes.mengxin.science/2017/07/07/hexo-next-renderer-kramed-mathjax/).

# Make the automatic equation numbering work

In general, to make the automatic equation numbering work, you have to wrap your LaTeX equations in `equation` environment.
Using the plain old style (i.e., wrap an equation with two dollar signs in each side) will not work.
How to refer to an equation? Just give a `\label{}` tag and then in your later text, use `\ref{}` or `\eqref{}` to refer it.
Using `\eqref{}` is preferred since if you use `\ref{}`, there are no parentheses around the equation number.
There are some variations I would like to elaborate.

## Simple equation

For simple equations like the following,

$$\begin{equation}
E=mc^2
\end{equation}\label{eq1}$$

It is easy to write and cite. Just use `$\eqref{eq1}$` to refer to equation
$\eqref{eq1}$. The code to produce the equation $\eqref{eq1}$ is

```latex
$$\begin{equation}
E=mc^2
\end{equation}\label{eq1}$$
```


## Multi-line equation

For multi-line equations, inside the `equation` environment, you can use the
`aligned` environment to split it into multiple lines. For example,

$$\begin{equation}
\begin{aligned}
a &= b + c \\
  &= d + e + f + g \\
  &= h + i
\end{aligned}
\end{equation}\label{eq2}$$

Equation $\eqref{eq2}$ is a multi-line equation. The code to produce equation
$\eqref{eq2}$ is

```latex
$$\begin{equation}
\begin{aligned}
a &= b + c \\
  &= d + e + f + g \\
  &= h + i
\end{aligned}
\end{equation}\label{eq2}$$
```

## Multiple aligned equations

We can use `align` environment to align multiple equations. Each of these
equations will get its own numbers.

$$\begin{align}
a &= b + c \label{eq3} \\
x &= yz \label{eq4}\\
l &= m - n \label{eq5}
\end{align}$$

There are three aligned equations above: equation $\eqref{eq3}$, equation
$\eqref{eq4}$ and equation $\eqref{eq5}$.

Use the following code to reproduce,

```
$$\begin{align}
a &= b + c \label{eq3} \\
x &= yz \label{eq4}\\
l &= m - n \label{eq5}
\end{align}$$
```

Since `align` in and of itself is a complete equation environment[^1].
You do not need to wrap it with `equation` environment.

### What if you do not want to number some equations

In the `align` environment, if you do not want to number one or some equations,
just [use `\nonumber`](https://tex.stackexchange.com/q/17528/114857) right behind these equations. Like the following.

$$\begin{align}
-4 + 5x &= 2+y \nonumber   \\
 w+2 &= -1+w \\
 ab &= cb
\end{align}$$

The code to reproduce is

```
$$\begin{align}
-4 + 5x &= 2+y \nonumber  \\
 w+2 &= -1+w \\
 ab &= cb
\end{align}$$
```

# Use `\tag` to tag equations

Sometimes, you want to use more "exotic" style to refer your equation. You can
use `\tag{}` to achieve this. For example

$$x+1\over\sqrt{1-x^2} \tag{i}\label{eq_tag}$$

Equation $\eqref{eq_tag}$ use `\tag{}` instead of automatic numbering. The code
to produce it is

```
$$x+1\over\sqrt{1-x^2} \tag{i}\label{eq_tag}$$
```

# References

+ <http://docs.mathjax.org/en/latest/tex.html>
+ <https://github.com/mathjax/MathJax/blob/master/test/sample-eqrefs.html>
+ <https://minenet.me/2016/04/05/how-to-use-mathjax.html#mjx-eqn-1>
+ <http://kchen.cc/2016/12/06/latex-in-markdown/>

[^1]: Check [here](https://tex.stackexchange.com/q/95402/114857) about the difference between `aligned` and `align` in LaTeX
