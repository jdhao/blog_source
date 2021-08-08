---
title: "Linux 下 Neovim 配置 Python 开发环境指南"
date: 2018-09-05T20:34:00+08:00
tags: [Python, Linux, font, 字体, pylint]
categories: [Nvim]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/Xnip2020-04-04_21-39-49.jpg" width="800">
</p>

我的完整 Neovim 配置可以点[这里](https://github.com/jdhao/nvim-config)查看。

Vim 是一款主要流行于 \*nix 系统上的强大的编辑器，另外一款可以与之媲美的编辑器是 Emacs，这两款编辑器广泛流行于程序员群体，[关于谁是编辑器之王的争论经久不息](https://en.wikipedia.org/wiki/Editor_war)。Vim 的功能虽然强大，但是作为一款「古老」的编辑器[^1]，也不是没有缺点：随着 Vim 代码量的增加，维护变得日益困难，不利于快速增加新功能；另外，它的主要开发者目前只有一个人，[Bram Moolenaar](https://en.wikipedia.org/wiki/Bram_Moolenaar)，也不符合当今开源社区多人协作的习惯。为了克服 Vim 的这些缺点，保留 Vim 的优点（最大程度兼容 Vim），让 Vim 的开发能有更快的迭代速度，[Neovim](https://neovim.io/) 项目诞生了。本文介绍如何安装 Neovim 并配置 Python 开发环境(在以下叙述中，Neovim 和 Nvim 含义相同，不再加以区分)[^3]。

<!--more-->

# 安装 Nvim

Neovim 有针对 Linux 系统的统一的 [appimage](https://appimage.org/) 格式的可执行文件，可以直接从 Neovim [GitHub release 页面](https://github.com/neovim/neovim/releases)下载。下载以后，先赋予文件可执行权限，

```bash
chmod u+x nvim.appimage
```

为了方便使用，可以在 Neovim 的安装目录下建立软链接，用 `nvim` 来作为该可执行文件的外部名称：

```bash
ln -s nvim.appimage nvim
```

接下来，我们需要将 Neovim 的安装目录加入到系统的 `$PATH` 变量，编辑`.bash_profile` 文件，把 Neovim 的安装目录（假设为 `$HOME/tools/nvim`）加入到`$PATH`变量:

```bash
export PATH=$HOME/tools/nvim:$PATH
```

保存文件，然后 `source .bash_profile`，使更改生效。

至此，Neovim 安装完成。这样设置以后，在命令行使用 `nvim` 命令即可打开 Neovim。

# Nvim 的配置文件

Neovim 使用的配置文件和 Vim 不同，需要在 `~/.config/nvim/` 目录下创建文件 `init.vim` ，该文件就是 Neovim 的配置文件，Neovim 所有的配置都可以放入其中。

由于 Neovim 是基于 Vim 开发的，所以 Neovim 和 Vim 的绝大多数配置都是相同的，如果之前使用过 Vim，可以把之前的配置的大部分拷贝过来使用。关于 Neovim 和 Vim 的不同，可以参见[这里](https://neovim.io/doc/user/vim_diff.html)。

# 使用插件管理器 vim-plug

Vim 之所以强大，一个很重要的原因是存在很多插件，在 Vim 漫长的发展过程中，无数开发者为 Vim 贡献了插件，这些插件可以实现各种各样的功能。如果安装插件很多，插件管理成为一个麻烦的问题。Neovim 和 Vim 一样，并没有自带插件管理器，我们需要自己安装插件管理器。经过搜索和比较，发现有两款比较有名的插件管理器在 Nvim 用户中流行，分别是 [dein](https://github.com/Shougo/dein.vim) 和 [vim-plug](https://github.com/junegunn/vim-plug). Vim-plug 的 user base 更大，最后我决定安装 vim-plug，以下为 vim-plug 安装以及简单的使用说明。

## vim-plug 安装

1. 安装 vim-plug 这个插件本身，运行以下命令安装：

```
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

安装 vim-plug 以后，可能需要退出 Nvim 重新进入。

2. 编辑 `init.vim` 文件，在该文件中加入 vim-plug 配置部分，以下为一个示例配置（改编自 vim-plug GitHub 主页，见[这里](https://github.com/junegunn/vim-plug#usage)）：

```bash
call plug#begin('~/.local/share/nvim/plugged')

call plug#end()
```

所有其它插件的安装都要放在两个 `call` 命令之间，下面不再赘述。

## vim-plug 使用

以下命令均在 Nvim 命令模式下使用

+ 安装插件：`:PlugInstall`
+ 更新插件：`:PlugUpdate`
+ 删除插件：`:PlugClean` （首先在 `init.vim` 中，注释掉该插件，然后打开 Nvim，使用 `:PlugClean` 命令清除该插件）
+ 查看插件状态：`:PlugStatus`
+ 升级 vim-plug：`:PlugUpgrade`

### 暂时禁用某个插件

如果想暂时禁用某个插件，但是不删除它，有两种方式。

1. 在 inti.vim 中注释掉该插件，重新打开 Nvim 即可
2. 参考[这里](https://github.com/junegunn/vim-plug/issues/369)给出的解决方法，如果要禁用插件 `foo/bar`，使用如下设置

```bash
Plug 'foo/bar', { 'on': [] }
```

# 常用插件安装与配置

## 自动补全插件 deoplete

[deoplete](https://github.com/Shougo/deoplete.nvim) 是一款和 YouCompleteMe 类似的自动补全插件，官方对它的描述为：

> Deoplete is the abbreviation of "dark powered neo-completion". It provides an
> extensible and asynchronous completion framework for neovim/Vim8.

准确来说，它其实是一款自动补全的引擎，具体对某种编程语言的自动补全支持，要安装对应的[source](https://github.com/Shougo/deoplete.nvim/wiki/Completion-Sources) 才能真正工作.

deoplete 安装相对简单，在 vim-plug 的设置里加入以下配置：

```bash
Plug 'Shougo/deoplete.nvim', { 'do': ':UpdateRemotePlugins' }
```

关于 deoplete 的配置，官方并未在 GitHub 主页多说，只给出了一个配置选项：

```bash
let g:deoplete#enable_at_startup = 1
```

更多选项以及对应的含义，可以在 Nvim 中输入 `:h deoplete-options` 查看。

如前面所说，deoplete 对不同编程语言的支持需要安装不同的 source，下面给出 Python语言的 source 配置，供参考。

### 安装 deoplete-jedi

Deoplete-jedi 是和 deoplete 配合使用的 Python source。安装以及配置步骤如下：

1. 需要安装的其他库

首先，我们需要安装 [pynvim](https://github.com/neovim/pynvim)，

```
pip install pynvim
```

接着，安装 [jedi](https://github.com/davidhalter/jedi). 如果你安装了最新的 Anaconda Python 发行版，jedi 已经安装了，否则，采用 pip 安装即可，

```
pip install jedi
```

2. 安装与配置 deoplete-jedi 插件

编辑 Nvim 的配置文件 `init.vim`，在 vim-plugin 部分，加入以下命令：

```bash
Plug 'zchee/deoplete-jedi'
```

然后安装 deoplete-jedi，如果没有问题，现在你已经可以使用自动补全了！使用 Nvim打开一个 Python 源文件，试着编辑一下，你会看到类似下图的自动补全菜单：

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-9-20/62954414.jpg" width="800">
</p>

### Deoplete 的使用

1. 如何在自动补全给出的函数或者类的列表中上下移动选择？

参考[这里](https://stackoverflow.com/questions/3842543/vim-auto-complete-navigation)的答案，可以使用 `Ctrl + N` 以及 `Ctrl + P` 在列表项目中进行切换。

2. 函数方法 Preview 的窗口如何自动关闭？

在自动补全给出的列表中移动的时候，Nvim 的上半部分会出现一个很小的窗口，提示当前方法的参数，但是该窗口在自动补全完成后并不能自动消失，参考[这里](https://github.com/Shougo/deoplete.nvim/issues/115)，可以使用下面的设置使得窗口自动消失：

```
autocmd InsertLeave,CompleteDone * if pumvisible() == 0 | pclose | endif
```

3. Preview 窗口的可以设为在当前窗口下面打开吗？

默认 preview 窗口在上面打开，可以通过 `set splitbelow` 使新建立的窗口位于当前窗口下面，参见[这里](https://github.com/Shougo/deoplete.nvim/issues/416)。

4. 如何设定为使用 `Tab` 键在自动补全的列表跳转？

在 Nvim 的配置中，加入如下设置即可：

```
inoremap <expr><tab> pumvisible() ? "\<c-n>" : "\<tab>"
```

## 状态栏插件 vim-airline

原生的 Nvim 状态栏比较简单，不像 Sublime Text 那样，状态栏可以显示很多信息。有一款不错的插件 [vim-airline](https://github.com/vim-airline/vim-airline) 可以实现类似 Sublime Text 的功能。在 vim-plugin 安装区加入以下命令：

```
Plug 'vim-airline/vim-airline'
```

然后运行 `PlugInstall` 安装该插件，重启 Nvim 可以看到更新后的状态栏。

### 切换 vim-airline 主题

vim-airline 提供了很多主题来个性化状态栏，不同主题的样子可以参见[这里](https://github.com/vim-airline/vim-airline/wiki/Screenshots)。更改vim-airline 主题方式很简单，首先安装插件[vim-airlinetheme](https://github.com/vim-airline/vim-airline-themes):

```
Plug 'vim-airline/vim-airline-themes'
```

然后，在 Nvim 配置文件中，加入以下设置，

```
let g:airline_theme='<theme>' " <theme> 代表某个主题的名称
```

## 自动引号&括号补全

输入引号，括号时，经常希望能够自动输入一对，原始的 Nvim 不支持这样的功能，可以使用 [auto-paris](https://github.com/jiangmiao/auto-pairs) 插件帮助我们实现这样的功能。插件安装：

```
Plug 'jiangmiao/auto-pairs'
```

### 使用方法

以引号输入为例，说明如何使用这个插件。按下 `"`，会自动变成双引号`""`，此时光标位于双引号的中间，等待插入文本，文本插入结束以后，通常我们希望把光标置于右边引号的后面继续输入，此时，再按一次 `"`，光标就会跳转到右边引号的后面，等待我们继续输入文本。

## 注释插件

不同编程语言中注释的表示方法不尽相同，如果能够根据当前编辑文件对应的编程语言自动选择合适的注释符号，将是极好的。[nerdcommenter](https://github.com/scrooloose/nerdcommenter) 就是这样一款插件。安装插件：

```
Plug 'scrooloose/nerdcommenter'
```

注释单行，使用 `<leader>cc`，其中 [leader](https://stackoverflow.com/q/1764263/6064933) 是在 Nvim 中设置的前导按键（Nvim 中默认为 `/`）；如果要反注释，使用 `<leader>cu`。更多具体使用方法可以参考[插件的文档](https://github.com/scrooloose/nerdcommenter#default-mappings)。

## 代码自动 format 插件

[neoformat](https://github.com/sbdchd/neoformat) 可以对源代码自动 format。安装
：

```
Plug 'sbdchd/neoformat'
```

对于 Python 代码，neoformat 需要配合 Python 的自动格式化工具（称为 `formatter`）来使用，这里以 [yapf](https://github.com/google/yapf#installation) 为例说明，首先安装该格式化工具：

```
pip install yapf
```

然后，neoformat 在格式化 Python 代码的时候就可以使用 `yapf` formatter 了。

如何格式化代码？命令模式下，输入 `:Neoformat`，该插件会对源代码进行自动 format。还有一款 [vim-autoformat](https://github.com/Chiel92/vim-autoformat)，也可以尝试一下。

如果 neoformat 没有检测到文件类型，或者没有插件，也可以设置以下命令，让 neoformat 对文件做简单的 format：

```
" Enable alignment
let g:neoformat_basic_format_align = 1

" Enable tab to spaces conversion
let g:neoformat_basic_format_retab = 1

" Enable trimmming of trailing whitespace
let g:neoformat_basic_format_trim = 1
```

## 代码跳转(go-to)插件

我们经常需要跳转到类，方法等的定义，查看实现细节，可以使用 [jedi-vim](https://github.com/davidhalter/jedi-vim) 实现，实际上，jedi-vim 还可以进行 Python 代码的自动补全，但是由于我们已经安装了 deoplete 和 deoplete-jedi进行自动补全，所以将 jedi-vim 的自动补全功能关闭，只使用代码跳转功能。

安装：

```
Plug 'davidhalter/jedi-vim'
```

jedi-vim 配置如下，

```
" disable autocompletion, cause we use deoplete for completion
let g:jedi#completions_enabled = 0

" open the go-to function in split, not another buffer
let g:jedi#use_splits_not_buffers = "right"
```

### 使用

+ `<leader>d`: go to definition
+ `K`: check documentation of class or method
+ `<leader>n`: show the usage of a name in current file
+ `<leader>r`: rename a name


## 文件管理器 nerdtree

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181223190958.png" width="800">
</p>

使用过 GUI 代码编辑器的人都知道，编辑器左边一般是项目文件浏览窗口，在 Nvim 中如何实现这一功能呢？可以安装 [nerdtree](https://github.com/scrooloose/nerdtree) 来实现。安装：

```
Plug 'scrooloose/nerdtree'
```

### 简单使用

1. 如何打开 nerdtree 文件浏览窗口？

直接在命令模式输入`:NERDTree` 即可打开当前编辑文件所在的目录。

2. 如何在文件窗口与 file explorer 窗口切换？

按住 `Ctrl`, 双击 `w` 可以在两个窗口之间切换。

3. 如何打开 file explorer 中某个文件？

把光标移动到该文件，然后按 `o`，即可在右边窗口打开该文件。

4. 如何退出 file explorer 窗口？

在该窗口直接按 `q` 即可退出。

更多个性化设置，可以参考[这篇](https://jdhao.github.io/2018/09/10/nerdtree_usage/)。

## 代码检查工具

[Neomake](https://github.com/neomake/neomake) 是为 Nvim 做的一款代码语法检查和自动化 make 工具。安装：

```
Plug 'neomake/neomake'
```

Neomake 对不同编程语言的检查依赖各种 linter，安装 Neomake 以后，针对要使用的编程语言，我们还需要安装对应的 linter。各种编程语言支持的 linter 列表见[这里](https://github.com/neomake/neomake/wiki/Makers)。

对于 Python，我们可以使用 [pylint](https://www.pylint.org/) 作为 linter。直接使用 pip 或 conda 安装 pylint：

```
conda install pylint
```

安装 pylint 以后，在 init.vim 中指定使用它作为 Python 代码检查器

```
let g:neomake_python_enabled_makers = ['pylint']
```

### 代码检查

命令模式下，使用 `:Neomake` 命令检查代码正确性。或者也可以[设置自动检查](https://github.com/neomake/neomake#setup)，在 Nvim 配置文件中，加入以下设置：

```
call neomake#configure#automake('nrwi', 500)
```

## 多点编辑插件 vim-multiple-cursors

使用过 [Sublime Text](https://www.sublimetext.com/) 的人应该都对 Sublime Text中多点编辑功能爱不释手，这个功能对于代码重构非常实用，如何在 Nvim 中使用类似的功能呢？可以借助于 [vim-multiple-cursors](https://github.com/terryma/vim-multiple-cursors)。安装：

```
Plug 'terryma/vim-multiple-cursors'
```

### 简单使用

命令模式下，首先把光标移动到要重命名的变量处，然后开始按 `Ctrl + N`，可以看到变量被高亮，继续按 `Ctrl + N`，变量下一个出现的地方被高亮显示，如果要跳过某个位置该变量的出现（例如，字符串中也可能包含与该变量名相同的子字符串），在该处被高亮以后，再按 `Ctrl + X` 取消高亮即可，不断选中变量的出现位置，直到所有想要选中的位置均选中完毕。

此时，按下 `c`（`c` 在 Nvim 中代表 *change* ）,进入编辑模式，输入变量新的名称，保存即可。更多使用方法，请参考该插件的文档。

### 参考

+ https://github.com/neovim/neovim/issues/3845
+ https://github.com/neovim/neovim/issues/211
+ https://github.com/neovim/neovim/issues/7257
+ https://github.com/onivim/oni/issues/184

## 高亮显示复制区域

Nvim 中，使用 `y` 复制文本以后，不会提示复制了哪些文本，除非使用者非常熟悉 Nvim按键，否则可能会多复制或少复制部分字符。[vim-highlightedyank](https://github.com/machakann/vim-highlightedyank) 这款插件可以在复制（yank）文本以后高亮提示哪些文本被复制了，非常实用。安装：

```
Plug 'machakann/vim-highlightedyank'
```

通常情况下，安装插件以后不需要做任何设置即可使用，但是对于某些主题，高亮的颜色可能看不清楚，可以在 Nvim 设置中加入以下命令：

```
hi HighlightedyankRegion cterm=reverse gui=reverse
```

如果觉得高亮显示的时间太短，可以设置增加高亮显示的时间（单位为毫秒）：

```
let g:highlightedyank_highlight_duration = 1000 " 高亮持续时间为 1000 毫秒
```

## 代码折叠插件

对于很长的代码，折叠代码有助于理清代码整体结构。[SimplyFold](https://github.com/tmhedberg/SimpylFold) 是一款不错代码折叠插件。安装：

```
Plug 'tmhedberg/SimpylFold'
```

安装以后不用进行设置，开箱即用。

## 简单使用

+ `zo`： 打开光标处的 fold
+ `zO`： 递归打开光标处所有 fold
+ `zc`： 关闭光标处 fold
+ `zC`： 关闭光标处所有 fold

# 主题安装与配置

## Before we begin

Neovim 自带了几款主题（colorscheme），你可能不喜欢，想要安装个性化主题。安装主题相对比较简单，但是，某些 SSH 程序对颜色的支持似乎有问题，导致主题安装以后，并不能很好工作。经过试用多款 Windows 系统下的 SSH 软件，最终发现[ZOC](https://www.emtec.com/zoc/index.html) 最好用，当然[Cygwin](https://www.cygwin.com/) 也是不错的选择。更多的 SSH 软件，可以参考[这里](https://gist.github.com/XVilka/8346728#here-are-terminals-discussions)给出的支持 true color 的 SSH 客户端。

## 主题安装介绍

[gruvbox](https://github.com/morhetz/gruvbox) 是一款 Vim 主题。在 vim-plug 安装插件部分，加入以下指令：

```
Plug 'morhetz/gruvbox'
```

运行 `:PlugInstall` 安装该主题。

在 Nvim 配置文件中加入

```
colorscheme gruvbox
```

即可启用该主题，主题有两个模式，分别为 `dark` 和 `light`，可以使用下面的命令进行切换:

```
set background=dark " 或者 set background=light
```

重启 Nvim，你将会看到主题的变化。在我的 Mac 上，主题显示如下：

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/Xnip2020-04-04_21-55-23.jpg" width="800">
</p>

# 如何找到更多有用的插件？

有个叫做 [vimawesome](https://vimawesome.com/) 的网站收集了很多不同类型 vim 插件的信息，可以在这个网站寻找合适的插件来尝试。另外，很多 Vim 插件都发布在GitHub 上，可以直接在 GitHub 上搜索 vim 和相关的关键词查找插件，例如，搜索 "vim colorscheme" 或 "vim theme"可以查找为 vim 设计的主题。

# 更改显示字体

Nvim 会自动使用 ssh 客户端设置的字体，所以在客户端设置字体即可。一些比较不错的编程字体有：

+ [FiraCode](https://github.com/tonsky/FiraCode)
+ [Source Code Pro](https://github.com/adobe-fonts/source-code-pro)
+ [Hack](https://github.com/source-foundry/Hack)

# 内置 terminal

Neovim 原生支持 terminal，[内置了 terminal emulator](https://neovim.io/doc/user/nvim_terminal_emulator.html)，可以直接在 Neovim 内运行 shell，对于一些临时的 shell 操作非常方便。打开的方式为在命令模式下输入 `:terminal` 或者输入 `vnew term://bash`(表示在水平方向再打开一个bash shell) 或者 `new term://bash`（在垂直方向打开一个 bash shell）。

进入 terminal 后，默认是命令状态，不能输入文字，按 `i` 开始输入命令。如何退出terminal window 的插入状态？按[`<Ctrl+\><Ctrl+N>`](https://vi.stackexchange.com/questions/4919/exit-from-terminal-mode-in-neovim)。

要退出 terminal 窗口，输入 `exit` 即可，此时 terminal 的窗口将关闭。

# Neovim 与 Vim 的不同

## 支持 bracketed paste mode

Neovim 官方文档指出 Neovim 支持 [bracketed paste mode](https://cirw.in/blog/bracketed-paste)，因此 Vim 中的 `paste` 选项已经过时了，在 Neovim 中使用 `:h paste`，可以看到如下的说明：

> This option is obsolete; bracketed-paste-mode is built-in.

## Neovim is all `nocompatible`

在 Neovim 界面，使用 `:h compatible`，给出的信息可以看到，

> Nvim is always "nocompatible"

# 一些小问题

## 在 Tmux 中，Nvim 的主题颜色显示有问题

首先，如果你的终端模拟器支持真彩色，那么需要先安装最新版本的 tmux[^2]。然后把如下配置加入到 tmux 配置文件 (`~/.tmux.conf`) 中 (参考 [这里](https://github.com/tmux/tmux/issues/699))：

```tmux
set -g default-terminal "screen-256color"
set -ga terminal-overrides ",xterm-256color*:Tc"
```

然后颜色显示应该会正常。如果你使用的终端不支持真彩色，那么就在 Neovim 的配置中加入下面的设置：

```vim
set notermguicolors
```

更多的讨论参考[这里](https://stackoverflow.com/q/10158508/6064933).

## 如何记录 Neovim 启动时候的 log

参考[这里](https://stackoverflow.com/q/3025615/6064933)。使用 `nvim -VNUMnvim.log` 来启动 Neovim。 `NUM` 用来指定 log 的 verbosity 等级，例如 `nvim -V10nvim.log` 将会生成一个 verbosity 等级为 10 的 log 文件。 另外在命令行运行 `nvim --help` 也有相关帮助信息.

至此，Neovim 就算配置完成了，终于可以愉快码代码了。

[^1]: Vim 最初于 1991 年发布，它的前身 Vi 诞生于 1978 年。
[^2]: 参考[这里](https://jdhao.github.io/2018/10/16/tmux_build_without_root_priviledge/)，安装 Tmux 最新版。
[^3]: 关于如何在 Windows 上配置 Neovim，参考[这里](https://jdhao.github.io/2018/11/15/neovim_configuration_windows/).
