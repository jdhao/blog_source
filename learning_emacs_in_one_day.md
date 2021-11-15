---
title: "Learning Emacs as A Nvimmer in One Day"
date: 2021-09-12T21:40:13+08:00
draft: false
tags: []
categories: [Emacs]
---

I have been using [Neovim](https://github.com/neovim/neovim) for over three
years and I have been constantly hearing the greatness of its rival editor
[Emacs](https://www.gnu.org/s/emacs/). So today I am gonna to try it and see if
it is truly great.

**Edit: My final Emacs conf is [here](https://github.com/jdhao/emacs-conf).**

<!--more-->

# Install

Check here: https://www.gnu.org/software/emacs/download.html

For Windows, go to [this mirror site](http://ftpmirror.gnu.org/emacs/windows),
and download emacs-27.2-x86_64.zip and extract it.

For macOS, use homebrew to install [Emacs-plus](https://github.com/d12frosted/homebrew-emacs-plus)[^2]:

```basha
brew tap d12frosted/emacs-plus
brew install emacs-plus 
```

# How to Run?

+ Windows: Go to extracted folder, run `bin/runemacs.exe`.
+ macOS: Run `emacs` from command line or search `Emacs.app` and run it.

Note to run emacs in maximized window, add following config to emacs config file:

```lisp
(add-to-list 'initial-frame-alist '(fullscreen . maximized))
```

Ref:

+ Run in maximized window: https://emacs.stackexchange.com/a/3017/23435

# Config file

Emacs uses`~/.emacs.d/init.el` as its startup file
(`~/.config/emacs/init.el` is also supported). Its configuration is
written via a dialect of [Lisp](https://en.wikipedia.org/wiki/Lisp_(programming_language))
language called [Emacs Lisp](https://www.gnu.org/software/emacs/manual/html_node/eintr/), or [Elisp](https://en.wikipedia.org/wiki/Emacs_Lisp) in short.

To check config file path, run `C-H v user-init-file RET` (`RET` is return key).

Reload config: note that it is not possible to entirely reload config, the best
way is to restart your emacs. Open `init.el`, and run `m-x eval-buffer`.

Ref:

+ https://www.gnu.org/software/emacs/manual/html_node/emacs/Find-Init.html
+ https://www.gnu.org/software/emacs/manual/html_node/emacs/Init-File.html
+ Show conf file path: https://stackoverflow.com/a/192097/6064933
+ Reload config without quit: https://stackoverflow.com/a/167719/6064933

# Install packages

I am not a purist guy which only uses the built-in features of and editor and
refuses to add plugins or packages.

Package is to Emacs what plugin is to Vim.

## The built-in way

To install packages, run `M-x package-install`, we are prompted to type the
package name we want to install. Type the package name, and the package shall
be installed.

Sometimes, due to bad network connection, emacs hangs forever when trying to
connect to its package repo, and we see the following message:

> Contacting host: elpa.gnu.org:443

In this case, we can [change the package repo URL](https://emacs.stackexchange.com/a/41769/23435)
(add it to `init.el`)

```lisp
;; this is just an example
(setq package-archives '(("gnu"   . "http://mirrors.tuna.tsinghua.edu.cn/elpa/gnu/")
                         ("melpa" . "http://mirrors.tuna.tsinghua.edu.cn/elpa/melpa/")))
```

One disadvantage of using built-in package management is that it can not
install a package automatically if its hasn't been installed. We need to write
[a few lines of code](https://emacs.stackexchange.com/a/28933/23435) in config file.

## Using 3rd party package manager

Aside from the built-in way to install packages, we can also use 3rd
party package managers, for example, [straight.el](https://github.com/raxod502/straight.el).

To install straight.el, just put the following script to `init.el`:

```lisp
(defvar bootstrap-version)
(let ((bootstrap-file
       (expand-file-name "straight/repos/straight.el/bootstrap.el" user-emacs-directory))
      (bootstrap-version 5))
  (unless (file-exists-p bootstrap-file)
    (with-current-buffer
        (url-retrieve-synchronously
         "https://raw.githubusercontent.com/raxod502/straight.el/develop/install.el"
         'silent 'inhibit-cookies)
      (goto-char (point-max))
      (eval-print-last-sexp)))
  (load bootstrap-file nil 'nomessage))
```

To install a package, add the following statement to init.el:

```lisp
(straight-use-package 'some-package)
;; for example, to install company-mode, use the following
(straight-use-package 'company)
```

Straight.el will install a package if it has not been installed yet.

# Color, theme, modeline

We can surly customize the look of Emacs via handcraft, e.g., change background
color and foreground color:

```lisp
(add-to-list 'default-frame-alist '(foreground-color . "#E0DFDB"))
(add-to-list 'default-frame-alist '(background-color . "#102372"))
```

More advanced way to customize color is to use or install a color theme. Emacs
comes with several built-in themes.

To find good themes, [MELPA](https://github.com/melpa/melpa) [is a good place to start](https://melpa.org/#/?q=theme&sort=downloads&asc=false).
A good theme package is [doom-themes](https://github.com/hlissner/emacs-doom-themes). It comes with a lot of themes.

To load a theme, type `A-x load-theme`, and then type a theme name, for example,
`doom-one`, or we can add to config:

```lisp
(load-theme 'doom-gruvbox t nil)
```

To show currently enabled themes, run `c-h v` and type `custom-enabled-themes`
to check.

# File editing

+ Delete a line: `C-S-Backspace`.
+ Go to line beginning and end: `C-a` and `c-e`.
+ Yank selected text and paste: `M-w` and `c-y`.

## Evil mode

I find the editing experience in Emacs highly inferior to that of Vim. So We
can use [emacs-evil](https://github.com/emacs-evil/evil):

```lisp
(straight-use-package 'evil)

;; enable evil mode
(evil-mode 1)
```

There is in fact a built-in [viper-mode](https://www.gnu.org/software/emacs/manual/html_mono/viper.html) for Emacs, but [it is
not complete as compared to evil](https://www.reddit.com/r/emacs/comments/9jp61o/difference_between_vipermode_evil_and_spacemacs/).

Ref:

+ Difference between viper, vimpulse, evil for emacs: https://stackoverflow.com/q/14291260/6064933
+ delete a line: https://stackoverflow.com/a/3958568/6064933
+ Emacs evil guide: https://github.com/noctuid/evil-guide

# Window and buffer

+ open a horizontal split: `c-x 2`
+ open a vertical split: `c-x 3`
+ close a buffer: `c-x k`
+ close a window: `c-x 0`
+ open a file: `c-x c-f`
+ create a new file: same as `c-x c-f`
+ save a file: `c-x c-s`
+ quit emacs: `c-x c-c`
+ Open a buffer: `c-x b`
+ To show config file path, run `M-:`, then type `(buffer-file-name)` to show config path.

Ref:

+ close buffer: https://stackoverflow.com/q/207510/6064933
+ close a windows: https://stackoverflow.com/q/17961449/6064933
+ show file path: https://stackoverflow.com/a/17415557/6064933

# Help system

`c-h ?` gives an overview of the help system.

+ Check the value of a variable: `c-h v <variable-name>`
+ Check function doc: `c-h f`
+ Check which command a key binds to: `c-h c`

Ref:

+ https://www.gnu.org/software/emacs/manual/html_node/eintr/See-variable-current-value.html
+ https://nyk.ma/posts/emacs-intro/

# Tab completion

Add the following to your emacs conf:

```list
(setq completion-cycle-threshold 1)
```

Ref:

+ https://emacs.stackexchange.com/a/17118/23435

# UI

## disable toolbar, menu bar, scrollbar

Toolbar, menu bar and scrollbar are ugly and take precious space. Let's
disable them all together:

```lisp
(menu-bar-mode -1)
(toggle-scroll-bar -1)
(tool-bar-mode -1)
```

Ref:

+ http://kb.mit.edu/confluence/display/istcontrib/Disabling+the+Emacs+menubar%2C+toolbar%2C+or+scrollbar

## Show line number and relative number

Like what vim does:

```lisp
(global-display-line-numbers-mode)
(setq display-line-numbers-type 'relative)
```

Ref:

+ https://stackoverflow.com/q/6874516/6064933
+ https://emacs.stackexchange.com/a/280/23435
+ https://emacs.stackexchange.com/q/45604/23435

## Modeline

What they call statusline in Vim, we call it modeline in Emacs. IMO,
they are very similar concepts.  We can use [doom-modeline](https://github.com/seagle0128/doom-modeline) to help
use customize the modeline.

First, we need to install some special icon that doom-modeline
uses. Run `M-x all-the-icons-install-fonts` to install the icon
fonts. Then install doom-modeline:

```lisp
(straight-use-package 'doom-modeline)
```

Ref:

+ https://stackoverflow.com/a/11419691/6064933
+ check current themes: https://superuser.com/a/320289/736190

## Tabline

For Emacs 27, add the following setting to `init.el`:

```lisp
(global-tab-line-mode t)
```

## show trailing white space

Add following config:

```lisp
(setq-default show-trailing-whitespace t)
```

Ref:

https://stackoverflow.com/a/34589105/6064933

# Auto-completion

We can use package [company](http://company-mode.github.io/) to enable
auto-completion for functions and options etc. Add the following
setting to `init.el`:

```
;; enable company mode in all buffers
(add-hook 'after-init-hook 'global-company-mode)

;; use tab and s-tab to cycle forward and backward completion items
(add-hook 'after-init-hook 'company-tng-mode)
```

The `company-tng-mode` is used to make `Tab` and `SHIFT-Tab` to move
forward and backward in the auto-completion menu, for more details see
[issue here](https://github.com/company-mode/company-mode/issues/526)
and [PR here](https://github.com/company-mode/company-mode/pull/706).

# fuzzy search

If you have used Vim, you may be familiar with fzf.vim or LeaderF or
telescope.nvim, depending which Vim distribution you are using. In
Emacs, we can use [ivy](https://oremacs.com/swiper/).

Here is a minimal config:

```
(straight-use-package 'ivy)
(straight-use-package 'counsel)

;; enable ivy mode by default
(ivy-mode 1)
```

Then ivy will be automatically enabled when you open file/buffer or
run commands. Note that the default fuzzy search behavior of ivy is
different from that of LeaderF or fzf. For example, to search
`package-install` in ivy, you can type `pac ins`, but not `pacins`,
the space between is important.

# Parentheses: highlight and insert

To highlight matching parentheses, add the following config:

```lisp
;; show matching parentheses
(show-paren-mode t)

;; the style for matching parentheses
(set-face-background 'show-paren-match "#ff0000")
(set-face-attribute 'show-paren-match nil
            :weight 'bold :underline t :overline nil :slant 'normal)
```

To insert matching parentheses automatically, install package [smartparens](https://github.com/Fuco1/smartparens):

```lisp
(straight-use-package 'smartparens)

;; enable smartparens mode
(smartparens-global-mode t)
```

To change, delete, add matching pairs easily, there is also
[evil-surround](https://github.com/emacs-evil/evil-surround), mimicking
the behavior of vim-surround by Tpope.

Ref:

+ show matching parentheses
    + https://www.emacswiki.org/emacs/ShowParenMode
    + https://emacs.stackexchange.com/q/47795/23435

# Issues

## custom-set-faces is added automatically to `init.el`

After installing packages, `init.el` is automatically updated and
include `custom-set-faces`.  We can use the following setting to
disable this behavior:

```lisp
(setq custom-file (locate-user-emacs-file "custom.el"))
(load custom-file)
```

Ref:

+ https://stackoverflow.com/q/5052088/6064933

## Change backup directory

By default, Emacs saves a backup file in the same directory as the current file,
which pollutes the current working directory. We can move the backup directory
to a centralized directory:

```lisp
;; change backup settings
(setq backup-directory-alist '(("." . "~/.cache/emacs-backups")))
```

# Conclusion

After learning and trying Emacs for one day, I would say that Emacs is truly a
great GUI editor which has some really cool features. For example, in Emacs,
you can set different font style (size, bold, italic, font family etc.) to
different texts in the same buffer, which [org-mode](https://orgmode.org/) used
for beautiful type-setting inside Emacs[^1]. You can also get the vscode-like
experience via [customize groups](https://www.gnu.org/software/emacs/manual/html_node/emacs/Customization-Groups.html).

However, when it comes to editting text and navigation, I think Vim/Nvim is
still the king, the proof of which is apparent since we have got [evil-mode](https://github.com/emacs-evil/evil)
in Emacs.

# References

+ https://github.com/emacs-tw/emacs-101-beginner-survival-guide
+ awesome emacs: https://github.com/emacs-tw/awesome-emacs
+ Emacs 101: https://github.com/emacs-tw/emacs-101-beginner-survival-guide
+ Emacs for the rest of us: http://iqss.github.io/IQSS.emacs/

[^1]: Sadly, I do not think any Nvim GUI can do this or come even close to org-mode.
[^2]: For macOS, there is [emacs-mac](https://github.com/d12frosted/homebrew-emacs-plus).
