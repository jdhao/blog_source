---
title: "Customize Tabline in Emacs"
date: 2021-09-30T00:32:50+08:00
draft: false
tags: []
categories: [Emacs]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202109300037148.jpg" width="400">
</p>

A short note on how to set up a more beautiful tabline for Emacs.

<!--more-->

In Emacs, to show the tabline in the top of the frame, add the following config:

```lisp
(global-tab-line-mode t)
```

The default tabline is ugly, it also shows the button to create or delete a
tab. To disable those button, add the following config:

```lisp
(setq tab-line-new-button-show nil)  ;; do not show add-new button
(setq tab-line-close-button-show nil)  ;; do not show close button
```

To change the separator between tabs, set the variable `tab-line-separator`:

```lisp
(setq tab-line-separator "")  ;; set it to empty
```

To further customize the look of tabs, we can employ the [powerline](https://github.com/milkypostman/powerline) package:

```lisp
(defvar my/tab-height 22)
(defvar my/tab-left (powerline-wave-right 'tab-line nil my/tab-height))
(defvar my/tab-right (powerline-wave-left nil 'tab-line my/tab-height))

(defun my/tab-line-tab-name-buffer (buffer &optional _buffers)
  (powerline-render (list my/tab-left
                          (format "%s" (buffer-name buffer))
                          my/tab-right)))
(setq tab-line-tab-name-function #'my/tab-line-tab-name-buffer)

;; tab color settings
(set-face-attribute 'tab-line nil ;; background behind tabs
      :background "gray40"
      :foreground "gray60" :distant-foreground "gray50"
      :height 1.0 :box nil)
(set-face-attribute 'tab-line-tab nil ;; active tab in another window
      :inherit 'tab-line
      :foreground "gray70" :background "gray90" :box nil)
(set-face-attribute 'tab-line-tab-current nil ;; active tab in current window
      :background "#b34cb3" :foreground "white" :box nil)
(set-face-attribute 'tab-line-tab-inactive nil ;; inactive tab
      :background "gray60" :foreground "black" :box nil)
(set-face-attribute 'tab-line-highlight nil ;; mouseover
      :background "white" :foreground 'unspecified)
```

The produced tab is shown in the title image. If you want to change the style
of tab from wave to vertical bar, you can use `powerline-bar-right` and
`powerline-bar-left` instead.

# References

+ https://amitp.blogspot.com/2020/06/emacs-prettier-tab-line.html
+ https://andreyorst.gitlab.io/posts/2020-05-10-making-emacs-tabs-look-like-in-atom/
