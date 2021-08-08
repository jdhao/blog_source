---
title : "Configuring Ultisnips for Neovim"
date: 2019-04-17 00:28:45+0800
tags: [snippet]
categories: [Nvim]
---

If you have used Sublime Text before, you may be familiar with its
[snippet](http://sublimetext.info/docs/en/extensibility/snippets.html) feature.
[Snippets](https://en.wikipedia.org/wiki/Snippet_(programming)) let us type a
trigger word and expand it to some boilerplate code or texts that we do not
want to repeat typing. We will increase our efficiency dramatically with the
help of snippets.

<!--more-->

Several snippet plugins are available for Vim/Neovim:
[snipmate](https://github.com/garbas/vim-snipmate), [neosnippet](https://github.com/Shougo/neosnippet.vim) and
[ultisnips](https://github.com/SirVer/ultisnips) etc. All these plugins are
good enough for simple use cases. If you don't know which one to use, just try
ultisnips. In this post, I would like to share how to configure ultisnips and
use your own snippets in Neovim.

# Install

In fact, ultisnips is just a snippet engine. To use the real snippets, you also
need to install [vim-snippets](https://github.com/honza/vim-snippets), which
provides a comprehensive list of snippets for various filetypes. You can
install the two plugins with your favorite plugin manager.

Ultisnips integrates automatically with the auto-completion engine
[deoplete](https://github.com/Shougo/deoplete.nvim). If you have installed
deoplete, you will see the auto-completion menu for your snippet keywords
without any settings.

The minimum settings for ultisnips are:

```vim
" Trigger configuration. Do not use <tab> if you use
" https://github.com/Valloric/YouCompleteMe.
let g:UltiSnipsExpandTrigger='<tab>'

" shortcut to go to next position
let g:UltiSnipsJumpForwardTrigger='<c-j>'

" shortcut to go to previous position
let g:UltiSnipsJumpBackwardTrigger='<c-k>'
```

In the above setting, we set the trigger key to <kbd>Tab</kbd>, i.e., after
typing the trigger word for a snippet, we can then press <kbd>Tab</kbd> to
trigger snippet expansion. We may define several positions (these positions may
optionally contains placeholder texts) in a snippet, which are called tabstops
by Ultisnips. In the above settings, `Ctrl-J` and `Ctrl-K` are used to jump
forward and backward in these tabstops.

# How to use

All the snippets provided by vim-snippets can be found
[here](https://github.com/honza/vim-snippets/tree/master/UltiSnips). The
snippet file for filetype `FOO` has name `FOO.snippets`, e.g., for python, the
snippet file is `python.snippets`. Filetype snippets are only available for the
specific file types. There is also a special snippet file named `all.snippets`,
which means that snippets inside are available for all file types.

A snippet has the following form:

```vim
snippet KEYWORD "some description" [FLAG]

snippet body...

endsnippet
```

, where `KEYWORD` is the trigger for the snippet. If you type `KEYWORD` and
press the trigger key, it will be expanded to the snippet body.

For example, there is a `date` keyword in `all.snippets`, if you type `date`,
and then press <kbd>Tab</kbd>, it will be expanded to the current date.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/date_snippet.gif">
</p>

# How to use your own snippets

It is easy to write a simple snippet. For example, to create a [kbd tag](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/kbd)
quickly when writing Markdown files, we can create a snippet for it. Here is
how to do it.

First, create a file named `markdown.snippets` and add the following content:

```
snippet kbd "HTML kbd tag"
<kbd>$1</kbd>
endsnippet
```

This will create a snippet named `kbd` for filetype markdown.

## Where to store my custom snippets?

After creating the snippet file, we should put it in a directory where
ultisnips can find it. This is a little tricky for Neovim.

The documentation of UltiSnips has explained how the snippet files are searched
(see `:h UltiSnips-how-snippets-are-loaded`). However, it is still not crystal
clear how to configure so that our custom snippets can be found by UltiSnips.

From the documentation of UltiSnips:

> UltiSnips will search each 'runtimepath' directory for the subdirectory names
> defined in g:UltiSnipsSnippetDirectories in the order they are defined. For
> example, if you keep your snippets in `~/.vim/mycoolsnippets`and you want to
> make use of the UltiSnips snippets that come with other plugins, add the
> following to your vimrc file.
>
>>  let g:UltiSnipsSnippetDirectories=["UltiSnips", "mycoolsnippets"]

The description above is both informative and confusing since it is written for
Vim users, not Neovim users. Because the Neovim config file follows the [XDG
Base Directory specification](https://wiki.archlinux.org/index.php/XDG_Base_Directory)
and is stored under `$HOME/.config/nvim`, not `$HOME/.vim`! As a result, if you
follow the above example, you will find that your custom snippets are not
available.

In the following, I will summarize what is working for **Neovim**.

First, open nvim and use the command `:echo &runtimepath`. This command will
print all the runtime paths that Neovim will search. According to the
documentation, your custom snippets directory should be put under one of these
runtime paths. On my Windows machine, the output is like (the full output is
omitted for brevity):

> C:\Users\Administrator\AppData\Local\nvim,C:\Users\Administrator\AppData\Local\nvim\plugged\deoplete.nvim,C:\Users\Administrator\AppData\Local\nvim\plugged\deoplete-jedi,C:\Users\Administrator\AppData\Local\nvim\plugged\neco-vim,.....

On my Linux machine, the output is like (the full output is omitted):

> /home/jdhao/.config/nvim,/home/jdhao/.local/share/nvim/plugged/deoplete.nvim/,/home/jdhao/.local/share/nvim/plugged/deoplete-jedi/,/home/jdhao/.local/share/nvim/plugged/jedi-vim/,.....

Different runtime paths are separated by a comma. The steps of configuring
custom snippets are the same for both Windows and Linux. So in the following
part, I will only focus on Linux since it is more popular.

One of the runtime paths on Linux is `/home/jdhao/.config/nvim`. This directory
is where the neovim config file `init.vim`[^1] is stored. We choose this
directory and create a folder named `my_snippets` under it. Then move the
`markdown.snippets` file into this folder[^2].

```bash
mkdir -p $HOME/.config/nvim/my_snippets
mv markdown.snippets $HOME/.config/nvim/my_snippets
```

In the third step, we add the following setting to `init.vim`:

```vim
" `my_snippets` is the directory we created before
let g:UltiSnipsSnippetDirectories=["UltiSnips", "my_snippets"]
```

Now, open a Markdown file and start typing `kbd` and you should be able to see
the `kbd` auto-complete item:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190421181446.png">
</p>

The completion engine I am using is deoplete and `US` means that this
completion item is from UltiSnips.

## A common error to avoid ##

You may have write your own snippet for a filetype and put it under the custom
snippet directory. However, the snippet does not seem to work.

One reason for this is that **you have used the wrong name** for the filetype.
For example, for LaTeX, the filetype is actually `tex`. So you need to create a
file named `tex.snippets`, **not** `latex.snippets` under your custom snippet
directory.

# References

+ <https://castel.dev/post/lecture-notes-1/>
+ https://github.com/SirVer/ultisnips/issues/830
+ [Difference between different snippet plugins](https://vi.stackexchange.com/q/7466/15292).

[^1]: To check the location of this directory for your computer, use command `:echo stdpath('config')` inside Neovim.
[^2]: You can find all my snippets [here](https://github.com/jdhao/nvim-config/tree/master/my_snippets).
