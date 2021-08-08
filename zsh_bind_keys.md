---
title: "Binding Keys in Zsh"
date: 2019-06-13T22:55:06+08:00
tags: [Zsh]
categories: [Linux]
---

In this post, I want to share how to use `bindkeys` command to solve a few
issues when using Zsh.

<!--more-->

# Can not use HOME and END keys normally

After using Zsh, I found that some terminals can not understand <kbd>Home</kbd>
and <kbd>End</kbd> keys correctly, i.e., I can not go to the beginning or end of
a line by pressing <kbd>Home</kbd> or <kbd>End</kbd> key.

This is because the key codes that terminal recognizes when you press certain
keys can not be recognized by Zsh. You can manually find which key code is sent
when you press a key. You can use `showkey -a` to print the key codes. Here is
what it shows when I press <kbd>Home</kbd> and <kbd>End</kbd> keys.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190613235841.png" width="400">
</p>

After we know the key codes for these two keys, we can bind the codes to the
operation we want to perform, that is `begin-of-line` and `end-of-line`.  Add
the following settings to your `.zshrc`:

```zsh
bindkey '^[[H' beginning-of-line
bindkey '^[[F' end-of-line
```

# Bind key to run a custom command

One reader of [this post](https://jdhao.github.io/2018/11/05/fzf_install_use/)
asked a question on how to use shortcut keys to run the following command:

```bash
nvim $(fzf)
```

I searched the web and found a solution. Suppose that we want to bind
<kbd>Ctrl</kbd> + <kbd>O</kbd> to the above command, we can add the following
setting to `.zshrc`:

```zsh
bindkey -s '^o' 'nvim $(fzf)^M'
# you may also use the following one
# bindkey -s '^o' 'nvim $(fzf)\n'
```

In the above setting, `-s` option is used to translate the input string to
output string so that when you press the shortcut, it is replaced with the
command you want to run. `^M` or `\n` is used to represent the <kbd>Enter</kbd>
key so that the command is run automatically.

## References

+ Home and End not recognized
    + https://stackoverflow.com/questions/8638012/fix-key-settings-home-end-insert-delete-in-zshrc-when-running-zsh-in-terminat
    + https://github.com/robbyrussell/oh-my-zsh/issues/3061
+ Bind keys to a command
    + http://zsh.sourceforge.net/Guide/zshguide04.html
    + http://zsh.sourceforge.net/Doc/Release/Zsh-Line-Editor.html#Zle-Builtins
    + http://zsh.sourceforge.net/Intro/intro_11.html
    + https://github.com/tomsquest/dotfiles/blob/master/zsh/bindkey.zsh
    + https://unix.stackexchange.com/questions/285208/how-to-remove-a-zsh-keybinding-if-i-dont-know-what-it-does
    + https://unix.stackexchange.com/questions/79897/how-can-i-use-bindkey-to-run-a-script
