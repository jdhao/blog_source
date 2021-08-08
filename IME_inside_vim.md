---
title: "Vim 实现的输入法体验"
date: 2021-02-26T23:34:36+08:00
draft: false
tags: [Vim]
categories: [Nvim]
---

文接[上回](https://jdhao.github.io/2021/02/25/nvim_ime_mode_auto_switch/)，Vim 虽然贵为编辑器之神，有一个没有解决好的问题就是输入中文，以及中英文的切换问题。最近也尝试了一些试图利用 Vim 提供的功能，在 Vim 中实现输入法的插件。本文做一总结点评。

<!--more-->

# VimIM

VimIM 是一个比较早尝试在 Vim 中实现输入法的插件，可惜在 2012 年就停止更新了。2012 年正是 Vim 的灰暗时期，当时连 job api 都没有出来，Vim 中调用函数只能是 blocking 方式的。

使用方式，首先下载 [VimIM](https://github.com/vim-scripts/VimIM)，可以用插件管理器或者手动下载。

```
Plug 'vim-scripts/VimIM'
```

但是这个仓库只包含代码，不包含数据，从 [这里](https://github.com/vimim/vimim/tree/master/plugin) 可以下载到字典数据，把 `vimim.cjk.txt`， `vimim.gbk.bsddb` 和 `vimim.txt` 放到 VimIM 插件的 plugin 目录下。在我的系统上，VimIM 安装目录在  `~/.local/share/nvim/plugged/VimIM/plugin`

```bash
wget -P  ~/.local/share/nvim/plugged/VimIM/plugin https://raw.fastgit.org/vimim/vimim/master/plugin/vimim.cjk.txt
wget -P  ~/.local/share/nvim/plugged/VimIM/plugin https://raw.fastgit.org/vimim/vimim/master/plugin/vimim.gbk.bsddb
wget -P  ~/.local/share/nvim/plugged/VimIM/plugin https://raw.fastgit.org/vimim/vimim/master/plugin/vimim.txt
```

下面是最小的可运行配置：

```
set runtimepath+=/home/haojiedong/.local/share/nvim/plugged/VimIM
" 关闭云输入
let g:Vimim_cloud=-1
```

打开 Vim，进入 insert 模式，使用 Ctrl + Shift + / 开启中文输入（这个是一个开关，再按一次关闭中文输入），输入英文字符，会出现弹出菜单，按数字可以选择汉字。

体验：输入速度很快，并且匹配很准，没有延迟的感觉。

按 `=` 和 `-` 可以往后、往前翻页，`Tab` 可以在给出的候选项之间切换，按空格可以让选中的词语上屏，按 Enter 输入英文字符。

在 Vim 7.4 中测试可以运行，使用 Neovim 无法工作，不清楚具体是哪里有问题，这个插件好久没更新了，可能是不兼容了。

## 参考

+ https://shenyu.me/2016/01/03/vim-plugin-vimim.html
+ https://www.vim.org/scripts/script.php?script_id=2506
+ https://code.google.com/archive/p/vimim/
+ https://github.com/howiefh/vimfiles/blob/master/vimfiles/doc/1_vimim.cnx


# ZFVimIM

[ZFVimIM](https://github.com/ZSaberLv0/ZFVimIM) 是一个比较新的用 Vim 实现的输入法。

使用如下的配置：

```vim
call plug#begin(expand('~/.local/share/nvim/plugged'))
  Plug 'ZSaberLv0/ZFVimIM'
  Plug 'ZSaberLv0/ZFVimJob'
call plug#end()

" 使用本地 db 的配置
function! s:zfvimim_init() abort
  let db = ZFVimIM_dbInit({'name' : 'jd_db'})
  call ZFVimIM_cloudRegister({
              \   'mode' : 'local',
              \   'dbId' : db['dbId'],
              \   'repoPath' : expand('~/scratch_dir/'),
              \   'dbFile' : 'pinyin.txt',
              \   'dbCountFile' : 'pinyin_count.txt',
              \ })
endfunction

augroup zfvim_conf
  autocmd!
  autocmd VimEnter * call s:zfvimim_init()
augroup END
```

接下来需要下载字典，也就是作者说的 db，从 [这里](https://github.com/ZSaberLv0/ZFVimIM_pinyin_base/tree/master/misc) 下载拼音文件 pinyin.txt 和 count 文件 pinyin_count.txt,放到一个测试的目录，上面的配置中，`repoPath` 对应的就是这个文件存放的目录。

然后使用上述配置打开 Neovim，在插入模式下，使用 `;;` 打开中文输入模式，开始输入即可。由于 pinyin.txt 只包含单个字拼音的数据，所以一点也不智能，输入拼音词组，需要自己一个个去选择数字来组词，所以速度比较慢。可以使用这个插件提供的 `IMAdd` 命令添加个人词组。例如， `:IMAdd 云淡风轻 yundanfengqing`。

加载一个更大的词库，能够缓解输入时需要自己组词的尴尬，可以使用 [这里](https://github.com/ZSaberLv0/ZFVimIM_pinyin_huge/blob/master/misc/pinyin_huge.txt) 提供的 pinyin_huge.txt 和 pinyin_huge_count.txt 测试。

这个插件还支持云输入模式，需要添加下面的插件和配置：

```vim
" 插件地址：https://github.com/ZSaberLv0/ZFVimIM_openapi
Plug 'ZSaberLv0/ZFVimIM_openapi'

let g:ZFVimIM_openapi_enable=1

```
<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20210228150000.jpg" width="800">
</p>

体验：有一些延迟卡顿，但是在 Neovim 下是可以正常使用的，云输入法挺好用的，比较准确（前提是可以正常使用 wget 进行请求）。
