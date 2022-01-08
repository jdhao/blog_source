---
title: "A Complete Guide on Writing LaTeX with Vimtex in Neovim"
date: 2019-03-26 22:54:43+0800
tags: [LaTeX, PDF, text-object]
categories: [Nvim]
---

<details>
<summary><font size="2" color="red">Update log</font></summary>

+ <font color="blue">2021-02-20: Add inverse search settings on Windows and macOS</font>
</details>

Previously, I have written [a post](https://jdhao.github.io/2018/03/10/sublime-text-latextools-setup/)
on how to write and compile LaTeX source code in Sublime Text. Now that I am
familiar with Neovim, I want to do everything related to text editing inside
Neovim. In this post, I would like to share how to configure Neovim for LaTeX
editing and previewing.

My complete config for vimtex can be found [here](https://github.com/jdhao/nvim-config/blob/master/core/plugins.vim) (search vimtex)
on my nvim-config repo .

<!--more-->

# Pre-requisite

Before we begin, you should make sure that you have installed the following
software and plugins.

+ TeX Distribution: [TeX Live](https://www.tug.org/texlive/) is preferred since it is bundled with the possible many tools you will ever need for writing LaTeX.
+ Auto-complete plugin: Since I use Neovim, so I recommend using [deoplete](https://github.com/Shougo/deoplete.nvim).
+ Snippet Engine and snippet plugin: I recommend [Ultisnips](https://github.com/SirVer/ultisnips) along with the many snippets provided by plugin [vim-snippets](https://github.com/honza/vim-snippets).

Then we need to install the LaTeX plugin for Neovim.
[Vimtex](https://github.com/lervag/vimtex) is a great choice. To install it, we
can use [vim-plug](https://github.com/junegunn/vim-plug):

```vim
Plug 'lervag/vimtex'
```

# Faster LaTeX source file editing

## Auto-completion

In writing LaTeX source code, auto-completion is crucial for fast editing and
improves our efficiency dramatically. Vimtex supports completion for citations,
labels (for figure, equation, table, section, etc.), commands, environments,
packages and document classes and more. For more documentation on completion,
see `:h vimtex-completion`.

Vimtex can work with several completion engines such as
[deoplete](https://github.com/Shougo/deoplete.nvim),
[YouCompleteMe](https://github.com/Valloric/YouCompleteMe),
[ncm2](https://github.com/ncm2/ncm2). Since I use deoplete for completion, I
will just introduce how to configure it for deoplete[^1]. To make vimtex work
with deoplete, add the following setting to your `init.vim`:

```vim
" This is new style
call deoplete#custom#var('omni', 'input_patterns', {
      \ 'tex': g:vimtex#re#deoplete
      \})
```

After that, you should be able to use auto-completion.

## How to quickly insert an environment?

Vimtex supports autocompletion through autocompletion engines such as deoplete.
After configuring vimtex to use deoplete, you should be able to use the snippet
provided by Ultisnips[^2].

Ultisnips is a snippet engine, which uses
[vim-snippets](https://github.com/honza/vim-snippets) to provide the actual
snippet for various file types. To insert an `figure` environment, type `fig`
in insert mode and press <kbd>Tab</kbd>.

To insert an `enumerate` environment, type `enum` in insert mode and press
<kbd>Tab</kbd>. The complete list of snippets provided by vim-snippets can be
found [here](https://github.com/honza/vim-snippets/blob/master/UltiSnips/tex.snippets).

## How to close an environment?

After typing `\begin{ENV}`, how do we automatically close it with `\end{ENV}`?
With vimtex, you can press `]]` in insert mode to auto-close an environment.

## Useful mappings and shortcuts

Vimtex provides the `e` text object to facilitate
[vim-surround](https://github.com/tpope/vim-surround) like features for
manipulating an environment quickly:

+ `cse`: change an environment
+ `dse`: delete an environment
+ `tse`：toggle stared environment and no-star environment, e.g., change from
  `equation*` to `equation` and vice versa.

You can also use the standard Vim motion with this object. For example, `vie`
will select the **inside** an environment, and `vae` will select the entire
environment (including the begin and end commands).

There are other shortcuts provided:

+ `]]`: close an environment (**only work in insert mode**, in normal mode, it
  will bring cursor to next subsection instead). After you have typed
  `begin{ENV}`, use `]]` to auto-close it with `\end{ENV}`

For more mappings, see `:h vimtex-default-mappings`.

## Jumping between references and the definition

It would be nice if we can jump to the definition of a label with the native
Vim shortcut [`Ctrl-]`](<http://vimdoc.sourceforge.net/htmldoc/tagsrch.html#CTRL-]>). You have to do several things.

First, you need to install a ctags executable. See [here](https://github.com/jdhao/nvim-config/blob/master/README.md#ctags) on how
to install ctags.

Second, you need to install a plugin to manage tags file for you.
[vim-gutentags](https://github.com/ludovicchabant/vim-gutentags) is a good
option. It will update the tags file automatically once the source file has
been changed. To make gutentags work, you need to tell it where your project
root is. The easiest way is to [make your project a git repository](https://kbroman.org/github_tutorial/pages/init.html).

Open the TeX source file again, the tags file should be created automatically.
If the cursor is in `\ref{some_label}`, you can press `Ctrl + ]` to go the
position where `some_label` is defined. BTW, to jump back, you can use
`Ctrl-T`.

## Useful command

+ `:VimtexInfo`: show all relevant info about current LaTeX project.
+ `:VimtexTocOpen`: show table of contents window
+ `:VimtexTocToggle`: toggle table of contents window
+ `:VimtexCompile`: Compile the current LaTeX source file and opens the viewer after successful compilation.
+ `:VimtexStop`: Stop compilation for the current project.
+ `:VimtexClean`: clean auxiliary files generated in compliation process.

# Other settings

## PDF previewing

Vimtex supports several PDF viewer based on the platform. On Windows,
[SumatraPDF](https://www.sumatrapdfreader.org/free-pdf-reader.html) is a good
choice. Below is the settings needed for SumatraPDF to work as the default
viewer for the compiled PDF files.

```
" settings for sumatraPDF
let g:vimtex_view_general_viewer = 'SumatraPDF'
let g:vimtex_view_general_options
    \ = '-reuse-instance -forward-search @tex @line @pdf'
let g:vimtex_view_general_options_latexmk = '-reuse-instance'
```

After the above settings, if you use `:VimtexCompile` command, the compiled PDF
will be opened in sumatraPDF automatically. If it does not show up, you can
also use `:VimtexView` to open the PDF manually.

On Mac, you can install [Skim](https://sourceforge.net/projects/skim-app/) and use it as a PDF viewer. My current settings
for previewing PDF on Mac is:

<details>
<summary><font color="red">Click to see the code.</font></summary>

```vim
" let g:vimtex_view_method = "skim"
let g:vimtex_view_general_viewer = '/Applications/Skim.app/Contents/SharedSupport/displayline'
let g:vimtex_view_general_options = '-r @line @pdf @tex'

augroup vimtex_mac
    autocmd!
    autocmd User VimtexEventCompileSuccess call UpdateSkim()
augroup END

function! UpdateSkim() abort
    let l:out = b:vimtex.out()
    let l:src_file_path = expand('%:p')
    let l:cmd = [g:vimtex_view_general_viewer, '-r']

    if !empty(system('pgrep Skim'))
    call extend(l:cmd, ['-g'])
    endif

    call jobstart(l:cmd + [line('.'), l:out, l:src_file_path])
endfunction
```
</details>

## Inverse search

<font color="red">Note the below settings are not updated anymore, see [this post](https://jdhao.github.io/2021/02/20/inverse_search_setup_neovim_vimtex/)
for a better and up-to-date config without much manual labor.</font>

First, we need to install [neovim-remote](https://github.com/mhinz/neovim-remote):

```
pip install -U neovim-remote
```

Second, we start nvim with a listen address so that nvim can listen for RPC
calls.

```
nvim --listen localhost:12345 test.tex
```

The port number does not matter as long as it is not taken by other
applications. If you use nvim-qt, you can use the following command instead:

```
nvim-qt -- --listen localhost:12345 test.tex
```

Now the settings will differ for the PDF viewer you use on different platforms.

### Inverse search for Sumatra PDF on Windows

Open Sumatra PDF, go to `Settings --> Options`, in the bottom part, there is a
section *Set inverse search command-line*, put the following command there:

```
nvr --servername localhost:12345 +"%l" "%f"
```

+ `%f`: it means the tex source file path corresponding to the current pdf.
+ `%l`: it means the line in the original tex file.

Double click somewhere in the PDF file, your cursor in nvim/nvim-qt should go
to the corresponding line in the file buffer.

### Inverse search for Skim on macOS

Open Skim PDF viewer, open the `Preferences` menu and go to `Sync` page. Set
the part `PDF-Tex Sync support`, use the following settings:

+ Preset: `Custom`
+ Command: `nvr`
+ Arguments: `--servername localhost:12345 +"%line" "%file"`

Based on [discussion here](https://github.com/lervag/vimtex/issues/1576#issuecomment-586754119), we can also
start nvim without the `--listen` option (`nvim test.tex`). When we open a tex
file in nvim, we can write the nvim server address to a file and read it when
running the `nvr` command in Skim.

Inside the Nvim config, add the following setting:

```
augroup vimtex_mac
  autocmd!
  autocmd FileType tex call SetServerName()
augroup END

function! SetServerName()
  call system('echo ' . v:servername . ' > /tmp/curvimserver')
endfunction
```

For skim, we change the `Arguments` in `Sync` page to the following:

+ Arguments: ``--servername `cat /tmp/curvimserver` +"%line" "%file" ``

To start inverse search, press Shift and Command key, then click the text you
want to inv-search.

## Linting and syntax checking

I use [neomake](https://github.com/neomake/neomake) for syntax checking. For LaTeX source file, neomake provides
[several makers](https://github.com/neomake/neomake/wiki/Makers):

+ [lacheck](https://ctan.org/pkg/lacheck?lang=en)
+ [chktex](https://ctan.org/pkg/chktex?lang=en)
+ [Proselint](https://github.com/amperser/proselint)

Among the makers, lacheck and chktex are installed if you have installed Tex
Live. Proselint can be installed via pip:

```
pip install proselint
```

## Setting up table of contents

Vimtex can show a nice little table of contents window on the left side if you
use command `VimtexTocOpen`. By default, the lables in each section is also
shown, which makes the TOC clutter with texts. We can customize TOC with
following settings:

```vim
" TOC settings
let g:vimtex_toc_config = {
      \ 'name' : 'TOC',
      \ 'layers' : ['content', 'todo', 'include'],
      \ 'resize' : 1,
      \ 'split_width' : 50,
      \ 'todo_sorted' : 0,
      \ 'show_help' : 1,
      \ 'show_numbers' : 1,
      \ 'mode' : 2,
      \}
```

For detailed meaning of the keys in the above dictionary, refer to `:h
g:vimtex_toc_config`.

## TeX directives

If you are compiling a LaTeX source file written in non-English character, you
may want to use the TeX directive to specify the compiling program. Vimtex
supports two TeX directives: `TeX root` and `TeX program`. `TeX program`
directive is useful when we want to compile the LaTeX source code with
`xelatex` instead of `pdflatex`. In order to compile your LaTeX source file
with `xelatex`, add the following directive to the first line of the source
code:

```
%!TeX program = xelatex
```

# References

+ https://yufanlu.net/2018/09/03/neovim-latex/
+ [vimtex: Create environment with shortcut](https://github.com/lervag/vimtex/issues/1036)
+ [Auto-close environment in LaTeX](https://tex.stackexchange.com/a/266242/114857)
+ [Setting up PDF viewer for Mac](https://gist.github.com/skulumani/7ea00478c63193a832a6d3f2e661a536).
+ Set up inverse search
    + https://www.sumatrapdfreader.org/docs/Use-Sumatra-as-a-pre-viewer-for-LaTeX-editors.html
    + https://forum.sumatrapdfreader.org/t/can-not-use-the-inverse-search-with-sumatrapdf-and-sublime/1786
    + https://tex.stackexchange.com/q/460971/114857
    + https://github.com/mhinz/neovim-remote/issues/132
    + https://github.com/lervag/vimtex/issues/262
    + https://github.com/lervag/vimtex/issues/877
    + https://chiyosuke.blogspot.com/2019/04/neovimvimtextexlive2018tex.html
    + [How do I jump from line in Skim to the same line in my text editor?](https://tex.stackexchange.com/q/129335/114857)
+ Go to label
    + https://github.com/lervag/vimtex/issues/348
    + https://github.com/lervag/vimtex/issues/853
+ [TeX directives provided by different programs](https://tex.stackexchange.com/q/78101/114857).

[^1]: For configuring vimtex to work with other completion engines, please refer to `:h vimtex-complete-auto`.
[^2]: See [this post](https://jdhao.github.io/2019/04/17/neovim_snippet_s1/) on how to configure Ultisnips for Neovim.
