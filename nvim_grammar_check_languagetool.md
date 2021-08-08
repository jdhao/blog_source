---
title: "Grammar Check in Neovim with LanguageTool"
date: 2020-09-20T22:47:19+08:00
tags: [writing]
categories: [Nvim]
---

As a Neovim user who writes frequently, I want to use Neovim to check my
grammar before posting it in my blog. In this post, I will compare several
plugins used for grammar checking. Under the hood, they all use the opensource
tool [languagetool](https://languagetool.org/).

<!--more-->

I will use macOS as example here. First, we need to install languagetool using
homebrew:

```
brew install languagetool
```

It will also install open-jdk for you, but the path is not set by default. We
have to set the open-jdk path manually:

```bash
export PATH="/usr/local/opt/openjdk/bin:$PATH"
```

Now I introduce several similar plugins that use languagetool for grammar
checking.

# LanguageTool.nvim

[LanguageTool.nvim](https://github.com/vigoux/LanguageTool.nvim) is neovim
plugin that support asynchronous grammar checking. Install LanguageTool.nvim
using vim-plug:

```
Plug 'vigoux/LanguageTool.nvim'
```

The only setting to make it work is:

```vim
let g:languagetool_server_jar="/usr/local/opt/languagetool/libexec/languagetool-server.jar"
```

Then open a file, run the following commands:

+ `:LanguageToolSetup`: set up the languagetool server
+ `:LanguageToolCheck`: check the current file
+ `:LanguageToolSummary`: open a buffer and list all the grammar errors found in the file.

It seems that languagetool.nvim is fork of
[vim-LanguageTool](https://github.com/dpelle/vim-LanguageTool), but the author
intends to rewrite it to use more neovim-specific features.

## Pros and cons

+ Pros: asynchronous grammar check. Has error highlighting.
+ Cons: [very slow](https://github.com/vigoux/LanguageTool.nvim/issues/6).

# vim-grammarous

[vim-grammarous](https://github.com/rhysd/vim-grammarous) is a more polished plugin.


Install:

```vim
Plug 'rhysd/vim-grammarous'
```

Run command: `:GrammarousCheck` to check grammar. It is asynchrous and
non-blocking. When it finishes, it will notify the user in the statusline how
many errros have been found.

It has options for you to configure the rules and categories for languagetool.
See my config [here](https://github.com/jdhao/nvim-config/commit/c146a50967f6dae2e411fb00b342545e2ece3700).
The explanation of categories and rules can be found
[here](https://community.languagetool.org/rule/list?offset=0&max=10&lang=en&filter=&categoryFilter=&_action_list=Filter).

## Pros and cons

+ Pros: Asynchronous and relatively fast. Various configs for you to tweak. Can
  specifiy the rules and categories to use for languagetool. Has error
  highlighting.
+ Cons: not found yet.

# vim-languagetool

Another plugin: https://github.com/dpelle/vim-LanguageTool

Install with vim-plug:

```
Plug 'dpelle/vim-LanguageTool'
```

Only config you need:

```vim
let g:languagetool_jar="/usr/local/opt/languagetool/libexec/languagetool-commandline.jar"
```

Run command: `:LanguageToolCheck` to check grammar.
## Pros and cons

+ Pros: Has error highlighting.
+ Cons: It is not asynchronous, so it will hang until the results are produced.

# vim-langtool

Another similar plugin is
[vim-langtool](https://github.com/Konfekt/vim-langtool). Install:

```
Plug 'Konfekt/vim-langtool'
```

Simple config:

```
let g:langtool_jar = '/usr/local/opt/languagetool/libexec/languagetool-commandline.jar'
```

Command to run: `:LangTool`.

## Pros and cons

+ Pros: It will populate quickfix list with errors.
+ Cons: It is not asynchronous. No error highlighting.

# Ale

[Ale](https://github.com/dense-analysis/ale) also support languagetool, add the
following config:

```vim
let g:ale_linters = {
    \ 'python': ['pylint'],
    \ 'vim': ['vint'],
    \ 'cpp': ['clang'],
    \ 'c': ['clang'],
    \ 'markdown': ['languagetool']
\}
```

Then Ale will populate errors in the quickfix list.

## Pros and cons

+ Pros: It will populate quickfix list with errors.
+ Cons: No custom config available.

# Common issues

A common issue with languagetool is that it is intended for plain text, while
filetypes like Markdown or LaTeX contains a lot of formatting commands or
structure. So using languagetool directly on these filetypes produces a lot of
false positives, as discussed [here](https://github.com/rhysd/vim-grammarous/issues/40).
However, it is not easy to fix this problem.

# Conclusion

Right now, it seems vim-grammarous the best plugin we can use for grammar
checking.
