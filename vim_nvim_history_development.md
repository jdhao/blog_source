---
title: "Vim 和 Neovim 的前世今生"
date: 2020-01-12T11:48:43+08:00
tags: [Vim, LSP]
categories: [Nvim]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202202021242998.jpg" width="800">
</p>

# 引子

从完全使用 [Neovim](https://neovim.io/) 进行日常[项目开发](https://jdhao.github.io/2018/12/24/centos_nvim_install_use_guide_en/)与[文档写作](https://jdhao.github.io/2019/01/15/markdown_edit_preview_nvim/)到现在，已经过去大约一年半的时间。一年半以前，我对 Vim 的了解还处在非常初级的阶段，甚至还不知道 Neovim，经过长时间的[使用与学习](https://jdhao.github.io/categories/Nvim/)，目前已经达到了熟练使用的程度。当然，Vim 的知识过于庞大，即便我已经使用了一年半的时间，还有很多 Vim/Neovim 的特性或者知识仍然有待了解与发掘。

在使用 Neovim 的过程中，我对 Neovim 和 Vim 的历史以及它们之间的*恩怨情仇*也产生了兴趣，散布在互联网上各处的博客、视频以及论坛讨论让我对过去的历史有了一定了解。

<!--more-->

# Vim 的历史

想要了解 Vim 的历史，一个很好的资料是 Vim 创始人 [Bram Moolenaar](https://en.wikipedia.org/wiki/Bram_Moolenaar) 在 VimConf[^4] 2018 上的专题演讲，他在演讲中全面回顾了 Vim 的发展以及 Vim 即将添加的一些新特性。出于兴趣，我把他的 [原始演讲视频](https://www.youtube.com/watch?v=ES1L2SPgIDI) 从 YouTube 上扒了下来，添加了英文字幕，[上传到了 B 站](https://www.bilibili.com/video/av79242778)。另外一个很好的视频是 Bram 在 Vim 25 周年活动上发表的[演讲](https://www.youtube.com/watch?v=ayc_qpB-93o)。

简单来说，Vim 于 1991 年由 Bram 发布，最初 Vim 模仿了 [Vi 编辑器](https://en.wikipedia.org/wiki/Vi)的特性，后面加以扩展，逐步添加了很多新功能。今年距离 Vim 首次发布已经快 30 年了，Vim 这个强大的编辑器仍然在[不断更新](https://github.com/vim/vim/commits/master)，并且被许多人所使用和讨论，这也从侧面说明了 Vim 的魅力 (Vim 被网友们尊称为[「编辑器之神」](https://www.google.com/search?q=vim+%E7%BC%96%E8%BE%91%E5%99%A8%E4%B9%8B%E7%A5%9E&oq=vim+%E7%BC%96%E8%BE%91%E5%99%A8%E4%B9%8B%E7%A5%9E&aqs=chrome..69i57.8927j0j4&sourceid=chrome&ie=UTF-8))。刚开始，Vim 完全由 Bram 独立开发维护，后来不断有开发者加入 Vim 的开发，并把 Vim 移植到了不同的系统平台上。有一段时间，Bram 甚至辞去了工作，全力投入到 Vim 的开发，靠着网友的捐助维持基本生活。2006 年 Bram 加入了 Google 位于苏黎世的分部，Google 出于对 Bram 的尊重以及对开源文化的认同，同意 Bram 每周可以花 20\% 的工作时间用于和 Vim 相关的工作，谷歌真是一家开明的公司。

# Vim 开发模式与弊端

Vim 最初开发的年代，虽然已经兴起了[开源运动](https://en.wikipedia.org/wiki/Open-source-software_movement)，例如大名鼎鼎的 [GNU](https://en.wikipedia.org/wiki/GNU) 项目，但是开源项目的组织与运行还不像现在这样方便，Git 和 GitHub 要在十几年后才会出现。其他开发者向 Bram 贡献 Vim 源代码的方式是通过邮件，向 Bram 提交 [patch](https://en.wikipedia.org/wiki/Patch_(computing))，如果 Bram 觉得这个 patch 不错，就会把 patch 合并到 Vim 的源代码中。20 多年过去了，开源项目的协作方式由于 [Git](https://git-scm.com/) 和 GitHub 的出现发生了很大变化。很多顶级开源项目都选择使用 GitHub 进行代码的开发和管理：开发者通过 Git 提交 pull request 方式贡献自己的代码，如果项目的维护者觉得代码的质量 OK，就会合并这个请求，将代码并入主线代码。

## 是否可持续发展的隐忧

Vim 代码曾托管在 [Google Code](https://code.google.com/archive/)，随着 Google Code 于 2015 年关闭，[Vim 源代码被迁移到 GitHub](https://www.vim.org/movetogithub.php)。表面上看起来 Vim 的开发模式遵循了开源项目的范式，实际上并不是，因为 Vim 并没有采用 GitHub pull request 的方式。关于 Vim 开发相关的讨论，都在 [vim-dev](https://groups.google.com/forum/#!forum/vim_dev) 进行，开发者仍然要向 Bram 提交 patch，如果 patch 通过，再由 Bram 本人提交到 GitHub 上[^3]，和之前的开发方式并没有本质区别。所以 Vim 虽然看起来也有很多贡献者，但是只有 Bram 才有权力决定什么代码可以合并入 Vim，什么样的代码不行 (Bram 就是 Vim 项目的 [BDFL](https://en.wikipedia.org/wiki/Benevolent_dictator_for_life))。Bram 对于添加新功能非常谨慎，导致之前有很多开发者提交的 patch 都没有被合并，引起了一些开发者对 Vim 开发模式的不满。这也引发了很多人对 Vim 项目未来能否可持续发展的[担忧](https://joshtronic.com/2018/08/12/will-vim-die-with-bram-moolenaar/)，当 Bram 去世以后，Vim 的未来会怎样？甚至 Bram 本人在采访中被问到，如何确保 Vim 项目可持续发展，他的回答是

> Keep me alive.

不知是开玩笑，还是他真正想法如此？也正是因为 Bram 的这种对新功能的「审慎」态度，Vim 7.0 版本于 2006 发布，一直到了[十年之后的](https://github.com/vim/vim/commit/bb76f24af2010943387ce696a7092175b4ecccf2) [2016 年](https://www.linuxbabe.com/vim/install-vim-8-0-debian-ubuntu-linux-mint-fedora-centos-arch-linux)，Vim 才发布了有重大更新的 8.0 版本，增加了开发者期待已久的异步操作等新特性。

# Neovim 的诞生

Neovim 之所以诞生，就与 Vim 这种「怪异」的开发模式有关。Vim 在版本 8.0 之前是没有异步执行功能的，这意味着，如果你运行一个命令，必须等到命令执行完毕，才可以继续操作当前 buffer，命令执行过程，会阻断当前的 UI，用户只能等着。Neovim 项目的发起人 [tarruda](https://github.com/tarruda)[^1] 曾经给 Vim 提了[一个 patch](https://groups.google.com/forum/#!msg/vim_dev/QF7Bzh1YABU/02-YGr7_sCwJ)，为 Vim 增加了 job 功能，可以帮助 Vim 异步执行操作。不过遗憾的是，该 patch 不知为什么没有被 Bram 接受。因此 Tarruda 在 2014 年 fork 了 Vim，发起了 [Neovim 项目](https://github.com/neovim/neovim)，开始改变 Vim 的开发模式，遵循现代开源项目的开发流程，同时对 Vim [杂乱的代码](https://www.reddit.com/r/vim/comments/bc43nv/how_should_i_read_the_source_code_of_vim/)进行了大的清理和重构，去掉了对老旧系统的支持，同时添加了新的特性。

## Neovim 特性

经过几年的发展，Neovim 没有陷入凋亡，目前来看，项目很活跃，开发者众多。Neovim 实现了很多新特性，例如内置 terminal 和异步功能都是 Neovim 率先实现，然后 Vim 才跟进实现的，可能是 Bram 也感受到了来自 Neovim 竞争的压力，不希望自己的项目失去开发者的青睐，加快了开发的步伐。遗憾的是，Vim 重新实现的异步功能，方法命名上选择了与 Neovim 不兼容，在 Neovim 中，发起和停止 job 的方法是 `jobstart()` 和 `jobstop()`，但是 Vim 使用的是 `job_start()` 和 `job_stop()`，这也给[开发者和用户](https://github.com/vim/vim/issues/904)[造成了不必要的麻烦](https://github.com/neovim/neovim/issues/8547)，在使用异步功能的时候，需要面对两套不同的 API[^2]。

在用户层面来看，使用 Neovim 或者 Vim，区别不明显，Neovim 主要改变在软件架构方面，例如 Neovim 增加了 RPC 通信功能，插件可以通过 [messagepack](https://neovim.io/doc/user/api.html#API) 协议与 Neovim 进行通信，控制 Neovim 的行为。这种方式的好处是插件不必使用原生的 Vim script 编写，大部分的功能都可以通过更加强大的编程语言来实现，例如 Python，或者 JavaScript，利用这个特性的最典型的插件如 [deoplete](https://github.com/Shougo/deoplete.nvim) 和 [coc.nvim](https://github.com/neoclide/coc.nvim)，特别是 coc.nvim 插件，利用 JavaScript 实现了非常强大的代码自动补全功能，可以媲美 VS code 提供的代码补全功能。

由于 Neovim 支持 RPC，在此基础上可以有[很多 GUI 的实现](https://github.com/neovim/neovim/wiki/Related-projects#gui)，GUI [通过 RPC 与 Neovim 进行通信](https://neovim.io/doc/user/ui.html#UI)。一些优秀的 GUI 客户端有 [nvim-qt](https://github.com/equalsraf/neovim-qt)，[vimR](https://github.com/qvacua/vimr)，[fvim](https://github.com/yatli/fvim) (19 年刚出来的客户端，还不错)。同时，Neovim 可以充当一个 server，被嵌入到其他编辑器或者程序中，因此你不必使用蹩脚的 Vim 按键模拟插件，可以使用真正的 Vim，例如 [vscode-neovim](https://marketplace.visualstudio.com/items?itemName=asvetliakov.vscode-neovim) 把 Neovim 嵌入到了 VS code 中，[firenvim](https://github.com/glacambre/firenvim) 把 Neovim 嵌入到了网页的 textarea 中，非常酷的插件，firenvim 的使用可以参考我之前的 [博文](https://jdhao.github.io/2020/01/01/firenvim_nvim_inside_browser/)。

Neovim 对 Lua 语言有很好的支持，可以使用 Lua 语言编写插件，设置 Neovim，调用 Neovim 的函数等，例如后面要提到的 Neovim 的 LSP 功能就是用 Lua 语言开发的，更多信息，可以参考 `:h lua`。

# Vim 与 Neovim 最新进展

## Vim 新进展

Bram 在 2018 年做了[一个调查](https://github.com/vim/vim/issues/3573)，希望了解 Vim 插件开发者目前最需要 Vim 实现哪些功能，其中呼声比较高的是 [floating windows](https://github.com/vim/vim/issues/3573#issuecomment-433731148)，也就是在当前窗口的任意位置再建立一个悬浮窗口，用来显示额外的信息。2019 年 12 月，在 [Vim 8.2](https://www.vim.org/vim-8.2-released.php) 版本中，正式加入了 floating window 功能。

同时 Bram 已经在考虑 [Vim 9.0](https://groups.google.com/forum/#!topic/vim_dev/__gARXMigYE) 的开发事宜，目前主要考虑的是加快 vim script 执行的速度，逐渐去掉 vim 的 Python，perl 等 interface，以及新的定义函数的方式等。Bram 已经在 GitHub 上创建了 [vim9 仓库](https://github.com/brammool/vim9)，开始 Vim 9 的开发工作。

## Neovim 新进展

Neovim 团队在 19 年 9 月份，先于 Vim ，在 Neovim [0.4 版本](https://github.com/neovim/neovim/commit/e2cc5fe09d98ce1ccaaa666a835c896805ccc196)中正式推出了 [floating window 特性](https://github.com/neovim/neovim/pull/6619)，马上就有一批插件利用 floating windows 做了一些功能，例如 [leaderf](https://github.com/Yggdroot/LeaderF) 利用 floating window 显示文件模糊查找的窗口，coc.nvim 利用 floating window 显示函数方法的签名等，整体感觉炫酷而实用，给人一种 IDE 的既视感。

另外，Neovim 已经合并了 [Language Server Protocol](https://microsoft.github.io/language-server-protocol/) 的[客户端实现](https://github.com/neovim/neovim/issues/5522)，并且专门[开了一个仓库](https://github.com/neovim/nvim-lspconfig)帮助用户配置 LSP，对于不喜欢使用第三方 LSP 插件的用户，多了一种选择。

Neovim 开发者正在开发的一个功能是 [tree-sitter 集成](https://github.com/neovim/neovim/pull/9219)，目前 Neovim 和 Vim 的语法高亮都是通过正则表达式实现的，效率不高，复杂的正则表达式可能会造成卡顿。[Tree-sitter](https://github.com/tree-sitter/tree-sitter) 是一个用 C 语言实现的源代码解析工具，等该功能合并进 Neovim 以后，能够进一步提高 Neovim 的速度，解决基于正则表达式的语法高亮的卡顿问题。对 Neovim treesitter 感兴趣的用户可以关注 [这个仓库](https://github.com/nvim-treesitter/nvim-treesitter)。

# 参考

+ [Hacker New 对 Vim 迁移到 GitHub 的讨论](https://news.ycombinator.com/item?id=9263193).
+ [The history of Vim](https://www.youtube.com/watch?v=yv6KAzRPHkc).
+ [Vim, an open-source editor.](http://www.free-soft.org/FSM/english/issue01/vim.html)
+ [Vim 各个版本发布信息](https://github.com/vim/vim-history).
+ [Vim 8 version release](https://lwn.net/Articles/713114/).
+ [Hacker News 上对 Vim 8.2 的讨论](https://news.ycombinator.com/item?id=21772223).
+ Tarruda
    + [What happened to tarruda](https://www.reddit.com/r/neovim/comments/85vj86/what_happened_to_tarruda/).
    + [What exactly does tarruda do?](https://www.reddit.com/r/neovim/comments/3knzem/what_exactly_does_tarruda_do/)
+ [Reddit 上对 Neovim LSP 和 tree-sitter 功能的讨论](https://www.reddit.com/r/neovim/comments/b2tc1g/what_i_am_looking_forward_to_builtin_lsp/).
+ [Neovim 0.4 版本的讨论](https://www.reddit.com/r/vim/comments/d4xjuv/neovim_v040_released_multigrid_floating_windows/).
+ [/r/vim 关于 Vim 9.0 的讨论](https://www.reddit.com/r/vim/comments/ebz0cj/vim_9/).
+ [/r/neovim 上关于 Vim 9.0 的讨论](https://www.reddit.com/r/neovim/comments/ebz0p1/vim_9/).
+ [Why Neovim is better than Vim](https://geoff.greer.fm/2015/01/15/why-neovim-is-better-than-vim/).
+ [Bram 对 Neovim 的看法](https://groups.google.com/forum/#!searchin/vim_dev/neovim/vim_dev/x0BF9Y0Uby8/Xse9Bvyza0AJ).
+ https://www.hostingadvice.com/blog/vim-creator-champions-charityware/

[^1]: Tarruda 目前已经不再担任 Neovim 的维护者，目前 Neovim 项目的[负责人](https://gitter.im/neovim/neovim?at=5588a8951c3ba5ef5bff811a)是 [Justin M. Keyes](https://github.com/justinmk).
[^2]: 有开发者专门开发了[一个插件](https://github.com/prabirshrestha/async.vim)，用来抹平 Neovim 和 Vim 异步特性的差异。
[^3]: 所以查看 [Vim 项目的贡献者](https://github.com/vim/vim/graphs/contributors)，就会发现很奇葩的现象：贡献者只有两人，并且几乎所有的 commit 作者都是 Bram。
[^4]: [VimConf](https://vimconf.org/) 是由日本的 Vim 使用与爱好者举办的一年一度的会议，迄今为止已举办了七届。
