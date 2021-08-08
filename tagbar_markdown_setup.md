---
title: "Generating Table of Contents for Markdown with Tagbar"
date: 2019-10-15 01:01:01+0800
tags: [Nvim, Markdown, tags, Vim]
categories: [Note]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20191015001602.png">
</p>

I have been using Vim plugin Tagbar for viewing and navigating tags inside my
source file. However, for Markdown files, there is no support out of the box.
In this post, I would like to share how to set up tagbar to show tags for
Markdown.

<!--more-->

# Markdown set up #

[The tagbar wiki](https://github.com/majutsushi/tagbar/wiki#markdown) has
instructions on setting up Markdown support.
Using [markdown2ctags](https://github.com/jszakmeister/markdown2ctags) is the
best way among the three methods given. Unfortunately, it is not detailed
enough and only works for Linux-like systems.

The original script has some issues when running it in Python 3 and with
Markdown files containing Unicode characters. I have modified the original
script and make it work in Python 3. You can find it
[here](https://github.com/jdhao/nvim-config/blob/master/tools/markdown2ctags.py).

## Linux ##

For Linux, put the script to folder `tools` under Neovim config directory[^1].
You have to give it execution right (this is very crucial but the author fails
to say so):

```
chmod u+x markdown2ctags.py
```

Inside Nvim config, add the following settings:

```
" Add support for markdown files in tagbar.
if has('win32')
    let g:md_ctags_bin=fnamemodify(stdpath('config')."\\tools\\markdown2ctags.exe", ":p")
else
    let g:md_ctags_bin=fnamemodify(stdpath('config')."/tools/markdown2ctags.py", ":p")
endif
let g:tagbar_type_markdown = {
    \ 'ctagstype': 'markdown.pandoc',
    \ 'ctagsbin' : g:md_ctags_bin,
    \ 'ctagsargs' : '-f - --sort=yes',
    \ 'kinds' : [
        \ 's:sections',
        \ 'i:images'
    \ ],
    \ 'sro' : '|',
    \ 'kind2scope' : {
        \ 's' : 'section',
    \ },
    \ 'sort': 0,
    \ }
```

## Windows

For Windows, you can not directly run a script without using `python`. We have
to convert the Python script to a standalone executable, i.e.,
`markdown2ctags.exe`. We can use pyinstaller to convert the script to
executable files:

```
pyinstaller --onefile --console markdown2ctags.py
```

The above command will create the executable under the `dist` folder in the
current working directory[^2]. Then you should copy it to the `tools` folder
under the Neovim config directory.

After all these set up, you should be able to see the table of contents in
tagbar windows after using `TagbarOpen` command.

# References #

+ [Tagbar doc](https://github.com/majutsushi/tagbar/blob/master/doc/tagbar.txt).
+ [Markdown support for tagbar](https://github.com/majutsushi/tagbar/wiki#markdown).
+ [Markdown2ctags repo](https://github.com/jszakmeister/markdown2ctags).

[^1]: Use `echo stdpath('config')` command inside Nvim to show the config path.

[^2]: For details on how to generate exe file from python script, see [this post](https://jdhao.github.io/2019/10/14/python_script_to_exe/)
