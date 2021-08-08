---
title: "A Complete Guide to Neovim Configuration for Python Development"
date: 2018-12-24T00:14:00+08:00
tags: [Python, Linux, font, pylint]
categories: [Nvim]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/Xnip2020-04-04_21-39-49.jpg" width="800">
</p>

<details>
<summary><font size="3" color="blue">Change log</font></summary>

+ <font color="blue">2021-01-17: Install nvim via tar ball instead of appimage</font>
+ <font color="blue">2020-12-01: Add detailed guide on installing Neovim on Linux</font>
</details>

<!--more-->

TL;DR: My complete nvim configuration is [here](https://github.com/jdhao/nvim-config). Follow the guide there on how to
use it. Most of the config below also applies to Windows and Mac. For how to
configure Neovim on Windows, you may also be interested in [this post](https://jdhao.github.io/2018/11/15/neovim_configuration_windows/).

Vim is a popular code editor on Unix/Linux systems, and it is often compared
with [Emacs](https://www.gnu.org/software/emacs/), another great code editor
among programmers. [The debate about which one is the best editor never
cease](https://en.wikipedia.org/wiki/Editor_war). Vim is powerful, but it has
its own shortcomings as an old editor[^1]. Over the years, Vim's code base has
grown increasing larger, which makes it hard for maintenance and for adding new
features quickly. It has only one core developer, [Bram Moolenaar](https://en.wikipedia.org/wiki/Bram_Moolenaar).
Although Vim has a lot of contributors, nearly all patches are merged by Bram[^2].
[To overcome the shortcomings of Vim](https://groups.google.com/forum/#!searchin/vim_dev/neovim/vim_dev/x0BF9Y0Uby8/Xse9Bvyza0AJ),
preserve its advantages (i.e., compatible with Vim) and make the development of
Vim faster, the [Neovim](https://neovim.io/) project is created.

In this post, I will give a detailed guide on how to install Neovim and
configure it as an IDE-like environment for Python development (in the rest of
this post, I will use Neovim and Nvim interchangeably).

# Install Nvim

Neovim has pre-built binary package for Linux, you can download it from the
Neovim [GitHub release page](https://github.com/neovim/neovim/releases):

```bash
mkdir -p $HOME/tools/ && cd $HOME/tools/
wget https://github.com/neovim/neovim/releases/download/nightly/nvim-linux64.tar.gz
tar zxvf nvim-linux64.tar.gz
```

The executable file is located in `$HOME/tools/nvim-linux/bin/nvim`.

Now, we need to add the bin directory to the system `PATH`. Open `.bash_profile`
and add the following line:

```bash
export PATH=$HOME/tools/nvim-linux64/bin:$PATH
```

Save the file and source it to make the change take effect.

Now, Neovim has been installed on your system successfully. You can open neovim
on terminal with `nvim` command.

# Nvim configuration file

Neovim use a different configuration file from Vim. You need to create a file
named `init.vim` under the directory `$HOME/config/nvim` (if this directory
does not exist, just create one). All configurations can be put into this file.

Most of the configuration options for Neovim are the same with Vim. You can
copy your vim configurations if you have used Vim before.

# Install and use a plugin manager

One of main reasons for the powerfulness of Vim comes from the various plugins
written for it. With these plugins, you can achieve all sorts of crazy things
which are hard to achieve with plain Vim. If you have installed a lot of
plugins manually, you will find it difficult to manage them. Like Vim, Neovim
does not have a builtin plugin manager. We need to install one ourselves.

There are two plugin managers in wide use among Nvim users. One is
[dein](https://github.com/Shougo/dein.vim) and the other is [vim-plug](https://github.com/junegunn/vim-plug).
Vim-plug has a larger user base and seems more popular. Finally, I decided to
give vim-plug a try.

## Install vim-plug

1. Install vim-plug itself:

```bash
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

After installing vim-plug, you may need to restart Nvim.

2. Edit `init.vim` and add the configuration for vim-plug. I show an example
   configuration below (modified from the [vim-plug GitHub
   page](https://github.com/junegunn/vim-plug#usage)):

```vim
call plug#begin('~/.local/share/nvim/plugged')

Plug 'davidhalter/jedi-vim'

call plug#end()
```

Note that all the plugin installation command **must** be put inside the two
`call` command above. I will not repeat that later.

## How to use vim-plug

You can execute following command in Nvim command mode:

+ Install plugins：`:PlugInstall`
+ Update plugins：`:PlugUpdate`
+ Remove plugins：`:PlugClean` (First, comment the plugin install command in
`init.vim`. Open Nvim and use `:PlugClean` to uninstall plugins)
+ Check the plugin status：`:PlugStatus`
+ Upgrade vim-plug itself：`:PlugUpgrade`

### Disable a plugin only temporarily

If you want to disable certain plugin for a while but do not want to delete it,
you can try two methods:

1. Comment out the plugin and reopen Nvim.

2. According to discussions
   [here](https://github.com/junegunn/vim-plug/issues/369), to disable plugin
   `foo/bar`, you can use the following settings:

```vim
Plug 'foo/bar', { 'on': [] }
```

# Plugin install and settings

## Auto-completion plugin deoplete

[deoplete](https://github.com/Shougo/deoplete.nvim) is an auto-completion
plugin designed for Neovim:

> Deoplete is the abbreviation of "dark powered neo-completion". It provides an
> extensible and asynchronous completion framework for neovim/Vim8.

Strictly speaking, it is an auto-completion engine. In order to enable
auto-completion for a certain programming language, you need to install the
corresponding
[source](https://github.com/Shougo/deoplete.nvim/wiki/Completion-Sources).

Deoplete is easy to install. Use the following settings inside the vim-plug
calls:

```vim
Plug 'Shougo/deoplete.nvim', { 'do': ':UpdateRemotePlugins' }
```

The minimum setting for deoplete is as follows:

```vim
let g:deoplete#enable_at_startup = 1
```

For more options and their meanings, check `:h deoplete-options` inside Nvim.

### Install plugin deoplete-jedi

As I have said, deoplete supports auto-completion for various languages via
different sources. In order to do auto-completion for Python, we need to
install deoplete-jedi. In the following, I list the steps to install
[deoplete-jedi](https://github.com/deoplete-plugins/deoplete-jedi).

Step 1: Install dependency packages

First we need to install [pynvim](https://github.com/neovim/pynvim)，

```bash
pip install pynvim
```

We also need to install [jedi](https://github.com/davidhalter/jedi). If you
happen to use the latest version of [Anaconda](https://www.anaconda.com/download/), jedi is already installed. Otherwise, you can
install jedi via pip:

```bash
pip install jedi
```

Step 2: Install and set up deoplete-jedi

First, we need to install deoplete-jedi using vim-plug:

```
Plug 'zchee/deoplete-jedi'
```

After that, if nothing wrong happens, you can use auto-completion now! Open a
Python source file with Nvim and start editing, you will see the
auto-completion pop-up menu like what is shown in the following image:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-9-20/62954414.jpg" width="600">
</p>

### How to use deoplete

1. How to navigate between the items in the auto-completion list?

According to discussions
[here](https://stackoverflow.com/questions/3842543/vim-auto-complete-navigation),
you can navigate through the auto-completion list with `<Ctrl-N>` and
`<Ctrl-P>`.

2. How to automatically close the method preview window?

When we navigate through the auto-completion list, a small window will appear
on top of current window and gives hints about the parameters of current
selected method. But this window will not disappear after auto-completion is
done. According to discussions [here](https://github.com/Shougo/deoplete.nvim/issues/115), we can use the
following setting to close the preview window automatically:

```vim
autocmd InsertLeave,CompleteDone * if pumvisible() == 0 | pclose | endif
```

3. How can I open the preview window below the current window?

By default, preview windows will open on top of current window. You can change
this behaviour by adding `set splitbelow` in the configuration file.  For more
info, see [here](https://github.com/Shougo/deoplete.nvim/issues/416).

4. How can I navigate through the auto-completion list with <kbd>Tab</kbd>?

Add the following setting in the Nvim configuration file:

```
inoremap <expr><tab> pumvisible() ? "\<c-n>" : "\<tab>"
```

## Status bar plugin: vim-airline

The default status bar of Nvim is simple and can not provide much useful
information like Sublime Text or other modern code editors. However, the status
bar can be customized to achieve wonderful effects. An excellent plugin is
called [vim-airline](https://github.com/vim-airline/vim-airline), which can
achieve the same functionalities as Sublime Text status bar. It is easy to
install:

```
Plug 'vim-airline/vim-airline'
```

Use `:PlugInstall` to install this plugin and restart Nvim. You should be able
to see the new status bar with more information displayed.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181223164220.png" width="800">
</p>

### Switch vim-airlinetheme

Vim-airline provides a lot of themes for you to customize your status bar.
Check [here](https://github.com/vim-airline/vim-airline/wiki/Screenshots) to
see what different theme looks like. Vim-airline has put the various themes in
another plugin called
[vim-airlinetheme](https://github.com/vim-airline/vim-airline-themes). To
change the theme, we need to install this plugin:

```
Plug 'vim-airline/vim-airline-themes'
```

Then, in the Nvim configuration file, add the following settings:

```
let g:airline_theme='<theme>' " <theme> is a valid theme name
```

## Automatic quote and bracket completion

When input quote and bracket, we often wish to input them as pairs. Nvim does
not support this feature. [Auto-pairs](https://github.com/jiangmiao/auto-pairs)
can achieve this and much more. Simply install it:

```
Plug 'jiangmiao/auto-pairs'
```

### How to use

Let's take manipulating double quotes as an example. In insert model, press `"`,
it will automatically become two double quotes. At the same time, the cursor is
placed between the two double quotes, waiting for input. When you finish
inputing text between the quotes, you may want to place the cursor after the
right quote and continue inserting text. Here comes the magic part: just press
`"` one more time, and the cursor will be placed right behind the right quote.

## comment plugin

Different programming languages have different specifications for comment. It
would be great to change the comment style for the current file based on its
file type. [nerdcommenter](https://github.com/scrooloose/nerdcommenter) is one
such plugin. Install it with vim-plug:

```vim
Plug 'scrooloose/nerdcommenter'
```

To comment out a single line, use `<leader>cc` ([leader](https://stackoverflow.com/q/1764263/6064933)
is a prefix key in Nvim, the default leader key is backslash ` \ `); to
uncomment a line, use `<leader>cu`. To comment or uncomment several lines, add
a repeating number to corresponding command, just like what you do in plain
Vim. For more usages, check [nerdcommenter's documentation](https://github.com/scrooloose/nerdcommenter#default-mappings).

## code auto-format plugin

[Neoformat](https://github.com/sbdchd/neoformat) can be used to format your
source code. Install it with vim-plug:

```
Plug 'sbdchd/neoformat'
```

For Python code, you need to install a Python code formatter to work together
with neoformat. We will choose [yapf](https://github.com/google/yapf#installation). First, we install
this formatter with pip:

```
pip install yapf
```

Then, you can format Python code with `yapf` formatter using neoformat.

### How to format code?

In command mode, input `Neoformat`, neoformat will auto-format your source
code.

If neoformat can not detect your file type or you haven't installed any
formatter. You can add the following command to your Nvim configuration to let
neoformat do some basic format for the file:

```
" Enable alignment
let g:neoformat_basic_format_align = 1

" Enable tab to space conversion
let g:neoformat_basic_format_retab = 1

" Enable trimmming of trailing whitespace
let g:neoformat_basic_format_trim = 1
```

There is another format plugin
[vim-autoformat](https://github.com/Chiel92/vim-autoformat) which you can try.

## Code jump (go-to) plugin

We often need to jump to the definition of class and method to check their
implementation details. We can use
[jedi-vim](https://github.com/davidhalter/jedi-vim) to achieve this. Actually,
jedi-vim can also do code auto-completion. Since we have installed deoplete and
deoplete-jedi, we can disable code completion and only use the code jumpt
function of jedi-vim. Install jedi-vim with vim-plug:

```
Plug 'davidhalter/jedi-vim'
```

Use the following simple configuration:

```vim
" disable autocompletion, because we use deoplete for completion
let g:jedi#completions_enabled = 0

" open the go-to function in split, not another buffer
let g:jedi#use_splits_not_buffers = "right"
```

### How to use jedi-vim

Move the cursor to the class or method you want to check, then press the
various supported shortcut provided by jedi-vim:

+ `<leader>d`: go to definition
+ `K`: check documentation of class or method
+ `<leader>n`: show the usage of a name in current file
+ `<leader>r`: rename a name

## File managing and exploration plugin

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181223190958.png" width="600">
</p>

Those who have been working with GUI code editors are familiar with the sidebar
on the left side of the code editor, which provides an overview of files and
folders in current project. How do we achieve similar functions in Nvim? You
can use [nerdtree](https://github.com/scrooloose/nerdtree). First, install it using vim-plug:

```vim
Plug 'scrooloose/nerdtree'
```

### How to use nerdtree?

1. How to open file navigation window?

In command mode, input `:NERDTree` to open the directory your current file
resides.

2. How do I open a file in the file explorer?

Place the cursor in the file you want to open and press `o` to open this file
in the right window.

3. How do I switch between file window and nerdtree file navigation window?

This is the same as [how you switch windows in Vim](https://superuser.com/q/280500/736190).
Press `ctrl` and press `w` two times quickly to switch window. You can also
press `<Ctrl-w>` and then press `L`.

4. How do I exit or close file explorer window?

Press `q` while your cursor is this window? Or use `NERDTreeClose` command in
command mode.

For more customization, refer to [this post](https://jdhao.github.io/2018/09/10/nerdtree_usage/).

## Code checker plugin

[Neomake](https://github.com/neomake/neomake) is a tool for code syntax check
and build automation for Nvim. Install it with vim-plug:

```
Plug 'neomake/neomake'
```

Neomake's syntax check for various languages relies on various linters. After
installation, you need to install linters according to your programming
languages. The linter for different languages are listed
[here](https://github.com/neomake/neomake/wiki/Makers).

For Python, we can use [pylint](https://www.pylint.org/) as linter. Install
pylint with pip or conda:

```
conda install pylint
# pip install pylint
```

After installing pylint, we need to set it in `init.vim` as the Python code
checker:

```
let g:neomake_python_enabled_makers = ['pylint']
```

### Code checking

In command mode, use `Neomake` command to start syntax check. We can also
enable [automatic code check](https://github.com/neomake/neomake#setup). Just
add the following setting in Nvim configuration file:

```
call neomake#configure#automake('nrwi', 500)
```

Pylint checks extensively on your code, which makes it a bit slow. Another code
linter you can try is [flake8](https://github.com/PyCQA/flake8), which is
faster.

## Multiple cursor editing plugin

If you have used [Sublime Text](https://www.sublimetext.com/) before, you must
be impressed by Sublime Text's ability to edit multiple position in the code
simultaneously. This function is great when you want to refactor your code. You
can achieve similar function with the help of
[vim-multiple-cursors](https://github.com/terryma/vim-multiple-cursors). Install it with:

```vim
Plug 'terryma/vim-multiple-cursors'
```

### How to use

In normal mode, move the cursor to a variable you want to rename, press
`<Ctrl-N>` and this variable will be highlighted. Continue press `<Ctrl-N>`,
the next occurrence of this variable will be highlighted. If you want to skip a
certain occurrence (for example, a string may contain a sub-string which is the
same as the variable), just press `<Ctrl-X>` after this position has been
highlighted. Continue selecting the occurrence of this variable until all its
occurrence has been selected.

Now, press `c` (in Nvim, `c` means to *change* ) and enter insert mode. Input a
new name and save.

But, I should say that this plugin is not perfect. When you have a lot of
occurrences of the variable in the file, the refactor of variable name is slow.

There are some discussions about Neovim's native support for multiple cursors.
But it seems that this function is still not finished as of today. You can find
relevant discussions in the following links:

+ https://github.com/neovim/neovim/issues/3845
+ https://github.com/neovim/neovim/issues/211
+ https://github.com/neovim/neovim/issues/7257
+ https://github.com/onivim/oni/issues/184

## Highlight your yank area

In Nvim, if you yank (i.e., copy) a block of text, you do not get a visual hint
about exactly what you have copied. Unless you are very familiar with Nvim, you
may have copied less or more than what you want to copy, which is annoying.
With the help of [vim-highlightedyank](https://github.com/machakann/vim-highlightedyank),
you can now highlight the text region which you have yanked. Handy little
feature, isn't it?

You can install it with vim-plug:

```
Plug 'machakann/vim-highlightedyank'
```

Most of the time, you do not need any further settings after installing this
plugin. But, for some colorschemes, you will not be able to see the highlight
colors. To fix this issue, add the following setting to Nvim configuration:

```
hi HighlightedyankRegion cterm=reverse gui=reverse
```

If you feel that the highlight duration time is too short, you can increase the
highlight duration (measured in ms):

```
" set highlight duration time to 1000 ms, i.e., 1 second
let g:highlightedyank_highlight_duration = 1000
```

## Code folding plugin

For large code base, it is helpful to fold some part to get a better idea of
the structure of the code.
[SimplyFold](https://github.com/tmhedberg/SimpylFold) is nice plugin for code
folding. Install it with vim-plug:

```
Plug 'tmhedberg/SimpylFold'
```

You do not need further settings. It works out of the box.

### simple usage

There are some shortcuts to manipulate code folding:

+ `zo`： Open fold in current cursor position
+ `zO`： Open fold and sub-fold in current cursor position recursively
+ `zc`： Close the fold in current cursor position
+ `zC`： Close the fold and sub-fold in current cursor position recursively

# Theme install and configuration

## Before we begin

Neovim has several builtin colorschemes or themes. You may not like them and
want better themes. It is easy to install a theme. But it is little tricky to
make the theme display correctly as what the theme homepage shows. A lot of
themes will not display well if you use an SSH client which has poor support
for true colors. For Windows system, after trying several SSH clients, I find
that [ZOC](https://www.emtec.com/zoc/index.html) works best. For Mac, you can
use the open source tool [iTerm2](https://www.iterm2.com/). To find more SSH
clients which support true colors, visit [this
page](https://gist.github.com/XVilka/8346728).

## Install themes

[Gruvbox](https://github.com/morhetz/gruvbox) is a popular Vim colorscheme.
Install it with vim-plug:

```
Plug 'morhetz/gruvbox'
```

Then use `:PlugInstall` to install it just like you install other plugins.

In Nvim configuration file, activate this theme with the following setting:

```
colorscheme gruvbox
```

It has two modes, i.e., `dark` and `light`, which can be switched using the
following settings:

```
set background=dark " use dark mode
" set background=light " uncomment to use light mode
```

Restart Nvim and you will activate the new theme. It looks like the following
on my Mac:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/Xnip2020-04-04_21-55-23.jpg" width="800">
</p>

## Other themes

There are other themes you may want to try:

+ [one dark](https://github.com/joshdick/onedark.vim)
+ [monokai](https://github.com/sickill/vim-monokai)
+ [jellybeans](https://github.com/nanotech/jellybeans.vim)
+ [NeoSolarized](https://github.com/icymind/NeoSolarized)

# How do I change the font?

Unlike Sublime Text, you can not set up which font to use in Nvim's
configuration. Instead, Nvim will use the font you choose for your terminal
emulator. So you need to set up your favorite font in the terminal. Some good
programming fonts are:

+ [Hack](https://github.com/source-foundry/Hack)
+ [FiraCode](https://github.com/tonsky/FiraCode)
+ [Source Code Pro](https://github.com/adobe-fonts/source-code-pro)

Check [nerd fonts](https://www.nerdfonts.com/) for more programming fonts.

# How to find more interesting plugins?

There is a website called [vimawesome](https://vimawesome.com/), which collects
information about various Vim plugins. You can find helpful plugins on this
website.

Since most Vim plugins are published on GitHub, you can also search with `vim`
and other keywords on GitHub to find relevant plugins. For example, search `vim
colorschem` or `vim theme` to find themes designed for Vim.

# Builtin terminal

Neovim has support for [terminal emulator](https://neovim.io/doc/user/nvim_terminal_emulator.html)
so that you can directly run a shell inside Neovim. It is handy for temporary
shell operations.

To open the terminal, use `:terminal` or `:vnew term://bash` or `new
term://bash`. After entering the terminal, you are in normal mode. Press `i` to
start typing terminal command.

To exit insert mode in terminal emulator, press
[`<Ctrl+\><Ctrl+N>`](https://vi.stackexchange.com/questions/4919/exit-from-terminal-mode-in-neovim).

To exit terminal emulator and close the window, use `exit`.

# The difference between Nvim and Vim

## Support for bracketed paste mode

Neovim has support for [bracketed paste
mode](https://cirw.in/blog/bracketed-paste). The `paste` option in Vim is
obsolete. Use `:h paste` and you will the following documentation:

> This option is obsolete; bracketed-paste-mode is built-in.

## Neovim is all `nocompatible`

In Nvim normal mode, use `:h comatible`, and you will see that:

> Nvim is always "nocompatible"

For more differences between Nvim and Vim, see [here](https://neovim.io/doc/user/vim_diff.html).

# Trouble shooting

In this part, I introduce some issues found when using Nvim and how to fix
these issue.

## Nvim colorscheme does not look right inside Tmux

If your terminal supports true color, first install the latest version of tmux[^3],
add the following config to `~/.tmux.conf` (extracted from [here](https://github.com/tmux/tmux/issues/699))

```tmux
set -g default-terminal "screen-256color"
set -ga terminal-overrides ",xterm-256color*:Tc"
```

Then the color should be fine. Otherwise, in the Nvim config file `init.vim`,
use the following setting instead:

```
set notermguicolors
```

Also, there are some discussions [here](https://stackoverflow.com/q/10158508/6064933).

## How do I get a log file when starting Neovim

Use `nvim -VNUMnvim.log`. `NUM` is used to indicate verbose level, for example:

```
nvim -V10nvim.log
```

The above command creates a log file `nvim.log` with verbose level 10.

More reference [here](https://stackoverflow.com/q/3025615/6064933). See also
`nvim --help`.

OK, that is end of this long post. You can find my complete Nvim configurations
[here](https://github.com/jdhao/nvim-config). Happy Viming!

[^1]: Vim was first released in 1991. Its predecessor is Vi, born in 1978.
[^2]: Developers would send a patch to Bram. Bram will review it and then merge it on GitHub if he thinks the patch is ok.
[^3]: Check [here](https://jdhao.github.io/2018/10/16/tmux_build_without_root_priviledge/) on how to build the latest version of tmux from source.
