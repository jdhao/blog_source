---
title: "Markdown Writing and Previewing in Neovim -- A Complete Guide"
date: 2019-01-15 15:27:57+08:00
tags: [Markdown]
categories: [Nvim]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200214144759.png" width="800">
</p>

<!--more-->

# Introduction

As a Neovim user who has been using Sublime Text for code editing for some
time, I miss the experience I get when dealing with Markdown files with the
help plugins such as [MarkdownEditing](https://github.com/SublimeText-Markdown/MarkdownEditing) and [MarkdownPreview](https://github.com/facelessuser/MarkdownPreview). In this
post, I would like to share my settings for Markdown editing inside Neovim to
get similar experience, even better!

# Markdown editing

When we are editing Markdown files, it is nice to have some code snippets to
improve efficiency. Fortunately,
[UltiSnips](https://github.com/SirVer/ultisnips) combined with [vim-snippets](https://github.com/honza/vim-snippets)[^1] provides a lot of
useful snippets for Markdown files.

The two plugins can be install by [vim-plug](https://github.com/junegunn/vim-plug):

```vim
Plug 'SirVer/ultisnips'
Plug 'honza/vim-snippets'
```

We need to set up UltiSnips to use it. The following is an example setting:

```vim
" Trigger configuration. Do not use <tab> if you use https://github.com/Valloric/YouCompleteMe.
let g:UltiSnipsExpandTrigger="<tab>"  " use <Tab> to trigger autocompletion
let g:UltiSnipsJumpForwardTrigger="<c-j>"
let g:UltiSnipsJumpBackwardTrigger="<c-k>"
```

In the above setting, we use <kbd>Tab</kbd> to trigger snippet expansion,
which means that after entering the snippet keyword for a particular snippet,
using `<Tab>` will expand the keyword to the full snippet template. If you have used <kbd>Tab</kbd> for other
mappings, you can set other snippet trigger, e.g., <kbd>Ctrl</kbd> + <kbd>J</kbd>:

```vim
let g:UltiSnipsExpandTrigger="<C-J>"
```

For example, if we type `link` and then press `<Tab>`, it will be expanded to
the following text:

```
[Text](http://www.url.com)
```

Your cursor will be placed at `Text`, with `Text` being in selection mode and
waiting for you to enter the real link text. The setting
`UltiSnipsJumpForwardTrigger` and `UltiSnipsJumpBackTrigger` set up the
shortcut key to go to next and previous text area of a snippet, making the
snippet editing more convenient. Take the above inline link snippet for an
example. Suppose we want to add an inline link to Neovim official website (the
address is https://neovim.io/), we can follow the steps below:

1. Enter `link` and press `<Tab>` to get the link template.
2. Start typing `Neovim` and `Text` will be replaced by `Neovim`.
3. Press `<Ctrl-F>`. `www.url.com` will be selected.
4. Start typing `https://neovim.io/`.

There are many more snippets you can use. Some of the commonly used snippet
keywords are listed below:

+ Header:
    + Level 1: `sec`
    + Level 2: `ssec`
    + Level 3: `sssec`
    + Level 4: `par`
    + Level 5: `spar`
+ Links:
    + Inline link: `link`
    + Reference link: `refl`
    + Image link: `img`
+ Code:
    + Code block: `cbl`
    + Inline code: `ilc`
+ Text rendering:
    + Italic text: `*`
    + Bold text: `**`
    + Italic and bold text: `***`
+ General:
    + date: `date`
    + time: `time`
    + date and exact time: `datetime`
    + date in ISO format: `diso`

Often times, you will find you want to more snippets to cover your use cases.
For example, I have set up a snippet `meta` which can generate the Markdown
front matter fore me, see [here](https://jdhao.github.io/2019/12/22/ultisnips_avoid_content_update/)
for more details.

For how to set up Ultisnips and writing your own snippets, see [this
post](https://jdhao.github.io/2019/04/17/neovim_snippet_s1/).

## Distraction-free writing

When we are writing Markdown files, we may wish to focus on the task and
temporarily disable the distraction elements in Neovim. The plugin
[goyo](https://github.com/junegunn/goyo.vim) is designed specifically for this
purpose. We can install it via vim-plug:

```
Plug 'junegunn/goyo.vim'
```

After installing this plugin, we can use `Goyo` command to toggle between the
normal mode and distraction-free mode.

The author also suggests installing
[limelight](https://github.com/junegunn/limelight.vim), which will dim all
lines except the current line when turned on.

First, install it with vim-plug:

```
Plug 'junegunn/limelight.vim'
```

After install, we can use `Limelight` to activate it. To deactivate, use
`Limelight!`.

We can also integrate goyo and limelight together with the following setting:

```vim
autocmd! User GoyoEnter Limelight
autocmd! User GoyoLeave Limelight!
```

when you turn on/off goyo, limelight will also be turn on/off.

# Markdown syntax highlighting

Code highlighting is also important, which helps us to see the structure of the
source code more clearly. The first plugin we should install is
[vim-markdown](https://github.com/plasticboy/vim-markdown). We can install it
via vim-plug:

```vim
" tabular plugin is used to format tables
Plug 'godlygeek/tabular'
" JSON front matter highlight plugin
Plug 'elzr/vim-json'
Plug 'plasticboy/vim-markdown'
```

Vim-markdown provides a lot of functionalities such as folding,
[conceal](http://vimdoc.sourceforge.net/htmldoc/syntax.html#conceal) etc. Here,
we mainly use its syntax highlighting feature. Below are some settings for
vim-markdown:

```vim
" disable header folding
let g:vim_markdown_folding_disabled = 1

" do not use conceal feature, the implementation is not so good
let g:vim_markdown_conceal = 0

" disable math tex conceal feature
let g:tex_conceal = ""
let g:vim_markdown_math = 1

" support front matter of various format
let g:vim_markdown_frontmatter = 1  " for YAML format
let g:vim_markdown_toml_frontmatter = 1  " for TOML format
let g:vim_markdown_json_frontmatter = 1  " for JSON format
```

This plugin also provides a few shortcuts and commands:

+ Shortcut:
    + `]]`: go to next header
    + `[[`: go to previous header
+ Command:
    + `Toc`: create a vertical window of table of contents
    + `TableFormat`: format the table under current cursor

For more settings about these plugins, please consult their documentations.

Another good Markdown syntax plugin I recommend is
[vim-pandoc-syntax](https://github.com/vim-pandoc/vim-pandoc-syntax). It
provides more syntax highlight and better conceal features. First, install it
via vim-plug:

```vim
Plug 'vim-pandoc/vim-pandoc-syntax'
```

It is designed to work with
[vim-pandoc](https://github.com/vim-pandoc/vim-pandoc). To use it as a
standalone plugin, we should add the following settings in our Neovim
configuration file:

```vim
augroup pandoc_syntax
    au! BufNewFile,BufFilePre,BufRead *.md set filetype=markdown.pandoc
augroup END
```

After all this settings, your Markdown file will be like what is shown in the
following image:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190115233114.png" width="600">
</p>

# Markdown previewing

There are several Markdown previewer for Neovim on GitHub. I have tried
[previm](https://github.com/previm/previm) and
[markdown-preview.nvim](https://github.com/iamcco/markdown-preview.nvim), both
of which have cross-platform support for Nvim (Windows, Linux and macOS are
supported). After some trail, I think markdown-preview is superior in terms of
speed, functionality provided and rendering result. It is easy to install with
vim-plug:

```vim
" if you don't have node and yarn, use pre build
Plug 'iamcco/markdown-preview.nvim', { 'do': { -> mkdp#util#install() }}
```

Here is my setting for this plugin:

```vim
" do not close the preview tab when switching to other buffers
let g:mkdp_auto_close = 0
```

To start previewing a markdown file, use `MarkdowPreview` command. Your default
browser will be opened automatically. You can also add a custom key mapping for
this command:

```vim
nnoremap <M-m> :MarkdownPreview<CR>
```

For other custom settings, see [its
documentation](https://github.com/iamcco/markdown-preview.nvim#install--usage).

# Summary #

In this post, I introduced the plugins and settings I use for Markdown editing,
syntax highlighting and previewing. The end result is the smooth writing
experience I get.

[^1]: UltiSnips is the snippet engine, while vim-snippets provides the actual snippet.
