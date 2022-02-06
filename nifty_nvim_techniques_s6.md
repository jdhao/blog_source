---
title: "Nifty Nvim Techniques That Make My Life Easier -- Series 6"
date: 2019-12-21 23:18:00+0800
tags: [Vim]
categories: [Nvim]
---

This is the 6th post of my post series on nifty Nvim/Vim techniques that will make my editing experience easier.

<details>
<summary><font size="3" color="red">Click here to check other posts in this series.</font></summary>

+ Series 11: https://jdhao.github.io/2021/11/22/nifty_nvim_techniques_s11/
+ Series 10: https://jdhao.github.io/2021/06/17/nifty_nvim_techniques_s10/
+ Series 9: https://jdhao.github.io/2021/01/07/nifty_nvim_techniques_s9/
+ Series 8: https://jdhao.github.io/2020/11/11/nifty_nvim_techniques_s8/
+ Series 7: https://jdhao.github.io/2020/09/22/nifty_nvim_techniques_s7/
+ Series 5: https://jdhao.github.io/2019/11/11/nifty_nvim_techniques_s5/
+ Series 4: https://jdhao.github.io/2019/09/17/nifty_nvim_techniques_s4/
+ Series 3: https://jdhao.github.io/2019/05/14/nifty_nvim_techniques_s3/
+ Series 2: https://jdhao.github.io/2019/04/17/nifty_nvim_techniques_s2/
+ Series 1: https://jdhao.github.io/2019/03/28/nifty_nvim_techniques_s1/
</details>

<!--more-->

# How do we get the script names when we get the `<SNR>` number?

When we use a script local function (see `:h script-local`) in mapping with the
following form

```vim
nnoremap <leader><space> :call <SID>some_function()<cr>
```

Vim will replace <SID> with <SNR> followed by a number and an Underscore, like
this: `<SNR>12_some_function()`. The number is the order the script
containing the function is sourced by Vim. To find the script, use the command
`:scriptnames`, which will print the path of different scripts in the order of
being sourced by Vim.

The Vim plugin [scriptease](https://github.com/tpope/vim-scriptease) provides a
command `:Scriptnames` which will show all the sourced script and their index
in a quickfix window for better inspection.

### References

+ [Get a script's SNR.](https://stackoverflow.com/a/58741653/6064933)

# Open a file and go to specific line

When you run a source file, if something goes wrong, you may see a stack trace
of error lines. You may want to jump to the specific lines in that file. If you
are in command line, you can use `nvim +{NUM} test.py` to open file `test.py `
and go the line `NUM`, for example:

```
# open test.py and go to 10th line
nvim +10 test.py
```

If you are already in Neovim and want to open a file and go to a particular
line, you can use `:e` or `:edit` command:

```
:e +10 some_file.py
```

### References

+ Nvim doc: `:h :edit` and `:h +cmd`.

# Repeat previous command line command

If you have used a command, how do you repeat that command without typing
it again in the command line? You can just press `@:` (see `:h @:`).

### References

+ http://tilvim.com/2014/01/29/repeat-cmdline.html
+ [How do I repeat any command in Vim, like “C-x z” in emacs?](https://stackoverflow.com/a/4789842/6064933)
+ [The dot “.” isn't repeating last command](https://superuser.com/q/938243/736190)

# Show character count for selected region

When we select some characters in visual mode, we want to know exactly how many
characters are selected. This can be achieved by pressing `g` followed by
`Ctrl-G`. The info about selected texts will be print on the command line:

> Selected 1 of 42 lines: 1 of 306 Words; 6 of 2015 characters; 18 of 2027 bytes

### References

+ https://superuser.com/questions/289264/count-highlighted-string-length-in-vim

# Open a file from pipe output in nvim

When the output of a terminal pipe is a file name and you follow it with `nvim`
command, nvim will interpret the filename as text and put the texts in an
unnamed buffer.

If you want to open the corresponding file instead, you may use `xargs`:

```
find ~ -type f -name "init.vim" -print |xargs nvim
```

Or you can use the bash `$` expansion like the following:

```
nvim $(find ~ -type f -name "init.vim" -print)
```

### References

+ [To open files in Grep's output to Vim's -o -mode](https://stackoverflow.com/q/1275241/6064933)
+ [How to a open a file in vim using pipe](https://superuser.com/q/769390/736190)

# How to confirm a wildmenu completion and go to next level?

When we set the option `wildmenu` (`set wildmenu`), we can cycle between the
completion items in the command line using <kbd>Tab</kbd>. For example, when we
use `:edit` command to open files, we can use <kbd>Tab</kbd> to select which
file to edit. But how do we confirm what we want to go to a subdirectory to do
further completions?

According to the documentation of `wildmenu`, we can press the <kbd>↓</kbd> key
to confirm a selection or go to the subdirectory if current selection is a
directory. BTW, you can press <kbd>↑</kbd> key to go to parent directory.
Pressing <kbd>←</kbd> and <kbd>→</kbd> will cycle backward and forward the
completion items, just like what `Ctrl-P` and `Ctrl-N` does.

### References

+ [Vim command line mode - how to confirm selection?](https://stackoverflow.com/q/23401574/6064933)
+ [Confirm selection among options](https://vi.stackexchange.com/q/15115/15292)
