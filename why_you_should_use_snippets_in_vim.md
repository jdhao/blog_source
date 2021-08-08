---
title: "Why You Should Use Snippets in Vim/Neovim"
date: 2020-05-27 22:46:19+0800
tags: [snippet, Vim]
categories: [Nvim]
---

If you find yourself repeatedly type some similar-structured text in Vim/Nvim,
you need to ask yourself if there is a better way to do it.

<!--more-->

Snippet is a good way to insert structured text: you just need to type the
trigger word and press the trigger key, then you have the whole text ready for
you to edit. For example, when I am writing my blog post, I use `meta` as the
trigger to generate the post front matter info.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200527234607.gif" width="600">
</p>

[Ultisnips](https://github.com/SirVer/ultisnips) is a good tool to automate
text insertion, an it can increase your efficiency dramatically. For how to
configure ultisnips for Neovim, refer to [this
post](https://jdhao.github.io/2019/04/17/neovim_snippet_s1/). In the following,
I would like to show several cases of using snippet to improve my efficiency.

# Generate debian change log file signature

I saw [a post on stackExchange](https://vi.stackexchange.com/q/25550/15292)
that some one want to insert [debian change file](https://www.debian.org/doc/manuals/maint-guide/dreq.en.html#changelog)
signature of the format:

```
-- Alexis Wilke <alexis@example.com>  Tue, 26 May 2020 18:00:09 -0800
```

Each time when he writes this signature, only the date changes. The date is
also in standard format. This is good case to show how we can save our time
with snippet.

We need create a file named `debchangelog.snippets` in our custom snippet
directory and add the following snippet into it:

```
snippet debsig "debian change log signature" w
-- Alexis Wilke <alexis@example.com>  `!v strftime('%a, %d %b %Y %X %z')`
endsnippet
```

The backticked text use the interpolation feature of Ultisnips. `!v` means to
use Vim language interpolation, and it should be followed by valid Vim script
expressions, like the one given above.

After adding this snippet, when you open a debian changelog file[^1], type
`debsig` and press the trigger key, it will be expanded to the signature
automatically.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200527233636.gif" width="600">
</p>

# Generate main function template for Python

If a Python module can be imported and run as standard alone script, it is often
a good practice to use the following template:

```python
def main():
    # the main function logic here


if __name__ == "__main__":
    main()
```

It is cumbersome and time-wasting to type this every time we create a file.
Let's create a snippet for it. In the file `python.snippet` under your custom
snippet directory, add the following snippet:

```
snippet main "Main function boilerplate" b
def main():
    $0


if __name__ == "__main__":
    main()
endsnippet
```

When you create a Python source file, type `main` and press trigger, the file
template will be created automatically for you.

# Insert center-aligned image in Markdown

The default Markdown syntax for image can not align the image or set the image
display size. We can use the raw HTML tag to achieve this:

```html
<p align="center">
<img src="image_url" width="800">
</p>
```

To avoid typing these HTML tags every time, we can add the following image
snippet to the file `markdown.snippet` under our custom snippet directory:

```
snippet img "Aligned image using HTML tag"
<p align="center">
<img src="${1:URL}" width="${2:800}">
</p>
$0
endsnippet
```

Then we can use `img` to trigger the image snippet. You can then press the
forward jump and backward jump shortcut for Ultisnips to jump back and forth
between the predefined stop positions to insert URL and adjust the image
display size.

[^1]: Make sure that the filetype is `debchangelog`, otherwise set the filetype to `debchangelog` manually.
