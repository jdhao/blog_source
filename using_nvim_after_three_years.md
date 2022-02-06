---
title: "Using Neovim for Three Years"
date: 2021-12-31T14:54:38+08:00
draft: false
tags: []
categories: [Nvim]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202202031428905.jpg" width="800">
</p>


I started using [Neovim](https://neovim.io/) (nvim for short) about three years ago, in Sep. 2018.
At that time, nvim had just released [v0.3.1](https://github.com/neovim/neovim/releases/tag/v0.3.1). Over the years, I have been a staunch nvim user,
constantly learning new knowledge, and sharing tips, tricks and interesting plugins in [my blog posts](https://jdhao.github.io/categories/Nvim/).

<!--more-->

# How I started with Neovim

Back when I was still in university, I had dabbled in Vim a little bit.
I remember that in those days, most posts on the Internet recommended a plugin manager called [vundle](https://github.com/VundleVim/Vundle.vim).
As for auto-completion, [ycm](https://github.com/ycm-core/YouCompleteMe) was the most popular plugin at the time.
However, it is difficult to install and configure, and I never succeeded in installing it.
I copied some basic config from various posts and did not really know what they mean.
Vim script seemed weird and hard to understand. So my first trial of Vim didn't really take off.
I learned the basic motions and know how to save and quit a file, but no fancy and advanced features such as text objects.
I was still using [PyCharm](https://www.jetbrains.com/pycharm/) and [Sublime Text](https://www.sublimetext.com/) for serious project development.

After I graduated and started working, since we need to run the code on a remote server with no GUI support,
I was forced to make choices:

1. Develop on the server without GUI, which implies using a TUI editor. Vim is apparently the choice.
2. Develop on my local PC and sync the code to remote via Git or other tools.

I find choice one more elegant, since I do not need to set up dev environment on my local machine,
and I can run my code directly on the server (running the code requires GPU and there is no GPU on my local machine).
At the time, I came to know the Neovim project. I choose Nvim because it is easily installable.
It provides [binary releases](https://github.com/neovim/neovim/releases), so I do not need to compile from source myself.
I also like its development model more than Vim's. Nvim uses GitHub and other modern dev tools for better collaboration,
while Vim is still using [google groups](https://groups.google.com/g/vim_dev)[^6] for dev communications.

Finally, I was on the Neovim boat and started my journey.

# My learning story

The initial learning curve of nvim was indeed quite steep.
I needed to constantly check the documentation or search on the Internet, then tweak the config to do what I want.
Sometimes, a small setting may waste a lot of time, since I needed to understand what it does and how it works.
I also spent a lot time tweaking the settings of various plugins to fit my need.

My way of learning Nvim is to add the features and plugins I need incrementally.
At the start, I only added the settings I wanted most, like autocompletion, linting, file explorer etc.
I searched intensively on the Internet for the state of the art plugins for a particular need.
I will try several similar plugins, and only keep the one that best suits me.
The most important point to remember is that never rush to master Vim in a few days.
Vim has so many hidden features and settings, and it is impossible to master it quickly.

After about three or four months of rapid learning process, I was at ease using Nvim for my development work.
I published [a post](https://jdhao.github.io/2018/12/24/centos_nvim_install_use_guide_en/) on how to configure Nvim for Python development.

After building a usable config, when I want to do something, I always check if there is a better way to do it.
I searched on the Internet and learned new tricks, and perfected those skills through practicing.
Notice that not all tricks on the Internet are good and written by advanced Vim users.
We need to constantly learn new Vim knowledge and update our existing settings.

Another great learning resource is the configurations of other people.
We can find numerous dotfile repos on GitHub and learn from them little tips and tricks that we do not know yet.
I have always advised **against** copying the config of others without knowing what you are doing.
At the very least, we should read the documentation and understand what a particular setting does.
If we copy other's config blindly, it may cause endless pains afterwards when we spend hours debugging misbehaving Nvim.

As I became more proficient in Vim, I started learning more advanced features such as text objects.
I also [created some custom text objects](https://jdhao.github.io/2020/11/15/nvim_text_objects/) for my own use.
I have always wanted to write my own plugin and contribute to the Vim community.
After learning enough about Vim script, I [wrote a plugin called better-escape.vim](https://jdhao.github.io/2020/12/18/my_first_vim_plugin/) in Dec. 2020.
The goal is to help users escape insert mode quicker without lagging caused by timeoutlen option.

This year (2021), I have been learning Lua and migrating some of my config to pure Lua.

# Evolution of my configuration

I didn't create a repo to store my nvim config at first. I just stored it [in a gist](https://gist.github.com/jdhao/d592ba03a8862628f31cba5144ea04c2/2caf723de20e486acfb0a24adc1c4d520b3146d4).
It wasn't until April 2019 that I [created a repo for my nvim config](https://github.com/jdhao/nvim-config/commit/154958e50cc322ed157a545860eb36826dc9a37f).

Initially, all my config is stored in a single file `init.vim`, which in its peak exceeded 2000 lines.
Later, as I learned more about how Vim works ([autoload](https://learnvimscriptthehardway.stevelosh.com/chapters/53.html), [ftplugin](https://learnvimscriptthehardway.stevelosh.com/chapters/42.html#vimftplugin), [after folder](https://vi.stackexchange.com/a/12735/15292) etc.)[^1],
I [started splitting](https://github.com/jdhao/nvim-config/commit/460ec98052e07bbf16d8143d8f242280662582a6) my config into different directories and files for better maintainability.

My config has been all vim script for a long time.
Since nvim is adding more lua support and using lua to implement its [builtin LSP support](https://neovim.io/doc/user/lsp.html),
I have also been updating my config and adding a little lua.
I have [slowly moved](https://github.com/jdhao/nvim-config/commit/50c761119a86cd12676e550e93f615e1fce0c3f9) my completion system to the builtin LSP[^2].
After that, part of my configuration is [moved to `lua/` directory](https://github.com/jdhao/nvim-config/commit/2933d7da625d96907b077e5ad7258bd5e8785dc6)。

With the [release of Nvim v0.5](https://lwn.net/Articles/864712/), there has been a burst of lua-based plugins.
I have also replaced some of the viml plugins to their lua-based counterparts[^7] this year.

# What remains, what has changed, and some new plugins

I am not a Vim purist[^8], so plugins have always been an essential part in using Vim/Nvim.
Since there is no central plugin marketplace like [Emacs MELPA](https://melpa.org/), finding good plugins is not always easy.

That a plugin is popular and has more stars does not mean that it is better or it suits you[^4].
Over the course of three years, I have always tried to find better alternatives for my existing plugins.
Admittedly, trying a new plugin and adapting to it is a time-consuming process,
especially when a plugin does not work out of the box and requires some degree of customization.
However, the efficiency boost brought by a well-designed plugin can be huge.

## What remains

A few plugins still remain:

+ git gutter plugin: I still use [vim-signify](https://github.com/mhinz/vim-signify), but [gitsigns.nvim](https://github.com/lewis6991/gitsigns.nvim) seems more powerful and promising.
+ snippet plugin: I am still using [ultisnips](https://github.com/SirVer/ultisnips) due to its powerful interpolation features.
  There are also many [lua-based snippet engines](https://github.com/rockerBOO/awesome-neovim#snippet) worth trying.
+ git plugins: still use [vim-fugitive](https://github.com/tpope/vim-fugitive). I have also tried [gina](https://github.com/lambdalisue/gina.vim), but find it too complicated.
  There are also lua-based git plugins such as [neogit](https://github.com/TimUntersberger/neogit), which aims to replicate the functionality of [magit](https://github.com/magit/magit).
+ markdown plugins: [vim-markdown](https://github.com/plasticboy/vim-markdown) for editing and [markdown-preview.nvim](https://github.com/iamcco/markdown-preview.nvim) for preview.

## What has changed

I have also updated a large part of my plugins during the process:

- plugin manager: from [vim-plug](https://github.com/junegunn/vim-plug) to [packer.nvim](https://github.com/wbthomason/packer.nvim). I switched to packer.nvim middle this year,
  because it allows all sort of crazy lazy loading and brings package management to a whole new level.
- autocompletion plugins: from [deoplete](https://github.com/Shougo/deoplete.nvim) + [deoplete-jedi](https://github.com/deoplete-plugins/deoplete-jedi) to [vim-lsp](https://github.com/prabirshrestha/vim-lsp) + deoplete to nvim-lsp + [nvim-compe](https://github.com/hrsh7th/nvim-compe) to nvim-lsp + [nvim-cmp](https://github.com/hrsh7th/nvim-cmp). Nvim-cmp is light-weight, well-designed and awesome.
- pair management: from [vim-surround](https://github.com/tpope/vim-surround) to [vim-sandwich](https://github.com/machakann/vim-sandwich).
  Vim-sandwich is well-designed and has better UX than vim-surround, though it is less popular than vim-surround.
- comment plugin: from [nerdcommenter](https://github.com/preservim/nerdcommenter) to [commentary.vim](https://github.com/tpope/vim-commentary). I like commentary's feature of commenting code via motion.
- file explorer: from [nerdtree](https://github.com/preservim/nerdtree) to none. I use fuzzy file finder instead.
  If I need to explore a project, I check it in another tmux pane.
- statusline plugin: from [vim-airline](https://github.com/vim-airline/vim-airline) to [lualine](https://github.com/nvim-lualine/lualine.nvim). Vim-airline is great,
  but it is also bloated with too many 3rd-party plugin integrations. Lualine is simple, faster, and easy to customize.
- change of linting: from [Neomake](https://github.com/neomake/neomake) to [ALE](https://github.com/dense-analysis/ale) to nvim-lsp
- auto-pair plugin: from [auto-pairs](https://github.com/jiangmiao/auto-pairs) to [delimitMate](https://github.com/Raimondi/delimitMate). DelimitMate has saner behaviour for pair insertion than auto-pairs.
- in-buffer jump: from [vim-sneak](https://github.com/justinmk/vim-sneak) to [hop.nvim](https://github.com/phaazon/hop.nvim). Vim-sneak has been a great plugin.
  I changed to hop.nvim because it may support jumping via [pinyin](https://en.wikipedia.org/wiki/Pinyin) ([PR here](https://github.com/phaazon/hop.nvim/pull/133)), which is convenient for Chinese users.
- search item indexing: from [vim-anzu](https://github.com/osyo-manga/vim-anzu) to [nvim-hlslens](https://github.com/kevinhwang91/nvim-hlslens).
  Nvim-hlslens is a lua plugin that provides better UX for search indexing using [virtual text feature](https://jdhao.github.io/2021/09/09/nvim_use_virtual_text/) of Nvim.
- clear search highlight automatically: from [vim-cool](https://github.com/romainl/vim-cool) to nvim-hlslens
- format plugin: from [Neoformat](https://github.com/sbdchd/neoformat) to none (using LSP for formatting).
- multiple cursors: from [vim-multiple-cursors](https://github.com/terryma/vim-multiple-cursors) to none. I now use [`gn`](http://vimcasts.org/episodes/operating-on-search-matches-using-gn/) mostly.
  [vim-visual-multi](https://github.com/mg979/vim-visual-multi) may be a good replacement (also recommended by author of vim-multiple-cursors).
- yank highlight: from [vim-highlightedyank](https://github.com/machakann/vim-highlightedyank) to nvim builtin, see [this post](https://jdhao.github.io/2020/05/22/highlight_yank_region_nvim/#neovim-only) for the detail.
- fuzzy finder: from [fzf.vim](https://github.com/junegunn/fzf.vim) to [LeaderF](https://github.com/Yggdroot/LeaderF). LeaderF works fast and reliably across different systems.
- startup screen: from [vim-startify](https://github.com/mhinz/vim-startify) to [alpha-nvim](https://github.com/goolord/alpha-nvim).
- tabline plugin: from vim-airline to [bufferline.nvim](https://github.com/akinsho/bufferline.nvim).
- indent line plugin: from [indentLine](https://github.com/Yggdroot/indentLine) to [indent-blankline.nvim](https://github.com/lukas-reineke/indent-blankline.nvim).
  Indent-blankline uses the virtual text feature to make indent line flawless even on blank lines.
- auto save plugin: from [vim-auto-save](https://github.com/907th/vim-auto-save) to [AutoSave.nvim](https://github.com/Pocco81/AutoSave.nvim).
- showing tags: from [tagbar](https://github.com/preservim/tagbar) to [vista](https://github.com/liuchengxu/vista.vim). Vista is better at displaying symbols.

## New plugins

I have also added some new and interesting plugins:

- [nvim-treesitter](https://github.com/nvim-treesitter/nvim-treesitter): more accurate syntax highlighting, text object and more.
- [nvim-notify](https://github.com/rcarriga/nvim-notify): show notifications via floating windows.
- [unicode.vim](https://github.com/chrisbra/unicode.vim): check, search and insert unicode characters.
- [asyncrun.vim](https://github.com/skywind3000/asyncrun.vim): run code asynchronously in the background and show results in quick fix window.
- [firenvim](https://github.com/glacambre/firenvim): run neovim inside text area in the browser and use it for quicker editing.
- [vim-oscyank](https://github.com/ojroques/vim-oscyank): yank text from remote server directly to the clipboard of your local machine.
- [wilder.nvim](https://github.com/gelguy/wilder.nvim): fancy autocompletion in command line for Nvim.
- [whichkey.nvim](https://github.com/folke/which-key.nvim): show key mappings and register contents etc. automatically for you.

# My thoughts on several topics

## Vim or Neovim?

This is a question that gets asked almost monthly by mainly beginners (e.g., see [this post](https://vi.stackexchange.com/q/34/15292)).
The short and unbiased answer is that it does not really matter for novice.

Since Nvim is a fork of Vim and merges patches from Vim regularly, the basic features are almost the same.
There are some subtle differences (see [vim-differences](https://neovim.io/doc/user/vim_diff.html#vim-differences)), but that is largely irrelevant for beginners.
If you are a beginner, you can pick either one and learn the basics[^5].
Later when you become a proficient user, you can choose a side, or make your config compatible with both Vim and Nvim.

It is a sad truth that Vim and Nvim are going on quite different paths:
Neovim is investing more on Lua, and its Lua plugin system is thriving,
while Vim is [trying hard implement the vim9 script](https://github.com/vim/vim/search?o=asc&q=Vim9&s=committer-date&type=commits), a completely new and incompatible version of viml.
There might be more incompatible changes happening on both sides.
It might be getting harder to port Vim patches to Neovim.
Personally, I doubt whether the amount of effort put into building vim9 script will ever pay off.
Considering that a lot of vim plugins are still trying to support Vim 7.xxx,
I doubt how many of these plugin authors are willing to rewrite their plugins in vim9 script,
especially when their plugins have very large code bases.

Personally, I will stick to Neovim and migrate to all-lua config some day.

## init.vim vs init.lua?

Since nvim has added [support for init.lua](https://github.com/neovim/neovim/commit/767cd8b17b71f78bdd4c2e0dd8d3f4f0f1551381), many users have moved from viml-based to lua-based config completely.
Currently, my config is still a mix of vim script and lua script due to several reasons:

+ Lua support in Nvim is still not complete and mature enough. For example, sometimes we still need to use `vim.cmd()` to run vim command.
  We still need to learn how to use Vim script.
+ Some viml command is more concise to write, and lua tends to be more verbose, e.g., when defining mappings or setting an option.
+ I am still using quite a few vim script plugins, and it makes sense to configure these plugins in vim script.

## Should I use a pre-made config distribution?

There are several Vim/Nvim distributions, which aim at providing IDE-like and out-of-the-box experience.
Some of the most popular distributions are [Spacevim](https://github.com/SpaceVim/SpaceVim), [LunarVim](https://github.com/LunarVim/LunarVim) and [NvChad](https://github.com/NvChad/NvChad).

People ask frequently on r/neovim and r/vim whether they should use a vim/Nvim distribution.
If you are serious about Vim and want to learn how to use it properly and eventually master it,
I think it is better to avoid those distributions and build your own config from scratch:

+ Those distributions may add their own configuration layer and complicate things.
  Users end up learning too much distribution-specific config without learning real Vim stuff.
+ Even if you use those distributions, when you want to customize certain things,
  you still need to learn how Vim/Nvim works under the hood.
  Otherwise, you can only count on other developers to implement the feature for you.
+ When you meet issues with those distributions, you may have a hard time debugging it,
  since they all include various configurations.

However, this does not mean that you can not learn from these distributions, e.g., how they configure,
how they organize their directory structure. You can even *steal* some configs for your own use.

# Concluding remarks

Looking back, I think I have made the right choice to learn Vim/Nvim.
It has been a rewarding jounery for me. I am so used to vim's style editing that Vim becomes second nature.

I am thankful to all the contributors of Vim/Nvim for building and maintaining such a great editor.
I am grateful for the efforts that plugin authors put into in building those great plugins.

[^1]: An excellent article on how to split your config is [from .vimrc to .vim](https://vimways.org/2018/from-vimrc-to-vim/).
[^2]: I used deoplete with deoplete-jedi for over two years. Then I moved to [vim-lsp](https://github.com/prabirshrestha/vim-lsp) + deoplete for several month, before moving to nvim-lsp.
[^4]: One such example is [vim-surround](https://github.com/tpope/vim-surround) vs [vim-sandwich](https://github.com/machakann/vim-sandwich). Vim-sandwich has much lesser stars than vim-surround, but I find it more user-friendly and switched to it in 2020.
[^5]: If you decide to go all Lua and use `init.lua`, then you should definitely use Neovim.
[^6]: I am not saying that Vim's development model is worse, I just like Nvim's development model more.
[^7]: A good resource for finding lua plugins is [awesome neovim](https://github.com/rockerBOO/awesome-neovim). [Neovimcraft](https://neovimcraft.com/) also collects some lua-based neovim plugins and config distributions.
[^8]: Vim purist refers to people who use little or no external plugins.
