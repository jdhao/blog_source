---
title: "Setting up Yasnippet for Emacs"
date: 2021-10-06T15:15:05+08:00
tags: [snippet]
categories: [Emacs]
---

Unlike Vim/Neovim, where there are snippet engines like [ultisnips](https://github.com/SirVer/ultisnips) and [other plugins](https://github.com/rockerBOO/awesome-neovim#snippet). In Emacs, the de facto snippet engine is [yasnippet](https://github.com/joaotavora/yasnippet).

<!--more-->

# Installation and setup

Like [vim-snippets]() for ultisnips, [yasnippet-snippets]() provides the actual snippets for yasnippet.

Yasnippet and yasnippet-snippets can be easily installed via [straight.el](https://github.com/raxod502/straight.el):

```emacs-lisp
(straight-use-package 'yasnippet)
(straight-use-package 'yasnippet-snippets)

(yas-global-mode t) ;; activate yasnippet
```

# Check available snippets

To check the available snippets for the current mode, run command `yas-visit-snippet-file` and choose snippet to view. You can also run command `yas-describe-tables`, which will show a list of snippets and their trigger words in a separate buffer.

![yasnippet snippet table](https://blog-resource-1257868508.file.myqcloud.com/202110062312844.jpg)

## The trigger shortcut

By default, the shortcut to trigger the expansion of a snippet is <kdb>Tab</kbd>. It can be easily changed, e.g., to change the shortcut to `Meta-z`, add the following config to `init.el`:

```emacs-lisp
(define-key yas-minor-mode-map (kbd "M-z") 'yas-expand)
```

## Jump forward and backward

To jump forward and backward through the tabstop fields/positions, the default is to use <kdb>Tab</kbd> and <kdb>Shift+Tab</kbd>. You can customize via the following config:

```emacs-lisp
;; use Meta-j and Meta-k to jump between fields
(define-key yas-keymap (kbd "M-j") 'yas-next-field-or-maybe-expand)
(define-key yas-keymap (kbd "M-k") 'yas-prev-field)
```

# Create custom snippets

The snippets are usually organized into different modes, which is kinda like filetypes in Vim/Neovim. For custom snippets, we can put under the directory `~/.emacs.d/snippets`.

## Snippet for a specific mode

To create a snippet for `markdown-mode`, create a directory `markdown-mode` under `~/.emacs.d/snippets` and create a new file there (the file name does not really matter)[^1]. To create a snippet for markdown front matter like what is done [here](https://jdhao.github.io/2019/12/22/ultisnips_avoid_content_update/) using ultisnips, use the following snippet:

```snippet
# -*- mode: snippet -*-
# name: meta data
# contributor: jdhao <jdhao@hotmail.com>
# key: meta
# --
---
title: "${1:TITLE}"
date: `(format-time-string "%Y-%m-%dT%H:%M:%S%:z")`
tags: [${2:TAG}]
categories: [${3:CATEGORY}]
---
$0
```

The `key` field is the trigger word for this snippet, i.e., you type `meta` and press the trigger key to expand the snippet. The string inside the two backtick will be interpreted by the Emacs lisp interpreter and the result will be inserted there.

## General Snippets (fundamental-mode snippets)

To write a snippet applicable to different modes, we can put it under the directory named `fundamental-mode`.


Check [here](https://github.com/joaotavora/yasnippet/blob/master/doc/snippet-development.org) on how to write a snippet for yasnippet.

# Override an existing snippet

To override an existing snippet provided by yas-snippets, create a new snippet for the same mode with the same name. For example, to override [link](https://github.com/AndreaCrotti/yasnippet-snippets/blob/master/snippets/markdown-mode/link), use the following snippet instead:

```emacs-lisp
# -*- mode: snippet -*-
# name: Link
# contributor: jdhao <jdhao@hotmail.com>
# key: link
# --
[${1:TEXT}](${2:URL})
```

The `name` field must be the same with the existing snippet you want to override.

# References

+ [how to change key binding for yas-expand](https://emacs.stackexchange.com/q/66352/23435)
+ https://www.youtube.com/watch?v=YDuqSwyZvlY

[^1]: You can also run command `yas-new-snippet` to create a new snippet.
