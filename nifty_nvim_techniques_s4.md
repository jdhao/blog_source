---
title: "Nifty Nvim Techniques That Make My Life Easier -- Series 4"
date: 2019-09-17 23:04:55+0800
tags: [Vim]
categories: [Nvim]
---

This is the 4th post of my post series on nifty Nvim/Vim techniques that will make my editing experience easier.

<details>
<summary><font size="3" color="red">Click here to check other posts in this series.</font></summary>

+ Series 11: https://jdhao.github.io/2021/11/22/nifty_nvim_techniques_s11/
+ Series 10: https://jdhao.github.io/2021/06/17/nifty_nvim_techniques_s10/
+ Series 9: https://jdhao.github.io/2021/01/07/nifty_nvim_techniques_s9/
+ Series 8: https://jdhao.github.io/2020/11/11/nifty_nvim_techniques_s8/
+ Series 7: https://jdhao.github.io/2020/09/22/nifty_nvim_techniques_s7/
+ Series 6: https://jdhao.github.io/2019/12/21/nifty_nvim_techniques_s6/
+ Series 5: https://jdhao.github.io/2019/11/11/nifty_nvim_techniques_s5/
+ Series 3: https://jdhao.github.io/2019/05/14/nifty_nvim_techniques_s3/
+ Series 2: https://jdhao.github.io/2019/04/17/nifty_nvim_techniques_s2/
+ Series 1: https://jdhao.github.io/2019/03/28/nifty_nvim_techniques_s1/
</details>

<!--more-->

# Check if a string contains a pattern?

There are two simple ways:

+ Use `match(str, pattern)`
+ Use `=~#` or `=~?`

For `match()` function, Vim assumes that `magic` (see `:h /magic`) option is
set for patterns. If `pattern` is found in `str`, it will return the index
where the `pattern` starts. Otherwise, it will return -1.

You can also use regex match via `=~#` and `=~?` (see `:h expr4`). `=~#`
matches cases during matching, while `=~?` ignores cases. During matching, it
is always assumed that `magic` option is set.

## References

+ [How to check if a string matches a pattern in vim](https://stackoverflow.com/q/55366871/6064933)

# When should I capitalize the function names?

For custom global functions, i.e., functions without the `s:` (see
`local-function`), the function name must start with an uppercase letter. But
for script local functions and auto-loaded functions (see `:h autoload`), you
do not need to start the actual function name with uppercase letter.


## References

+ [Do VimScript functions have to start with a capital letter and if so, why?](https://vi.stackexchange.com/a/2664/15292)
+ [vim functions with script scope](https://stackoverflow.com/q/6164795/6064933)

# How to represent `ALT` key in mapping? `M` or `A` ？

According to documentation (`:h key-notation`), `<M-...>` and `<A-...>` are the
same. Both can be used to refer to <kbd>Alt</kbd> key.

# How to check the actual key press that Nvim receives when I press a key?

Sometimes, due to various reasons, when you press some key, what Nvim receives
is not that key press. To check the key that Nvim actually receives, press
`Ctrl-V` in insert mode and then press the key you want to check.

## References

+ https://superuser.com/a/235520/736190

# How to repeat some character N times？

If you are in normal mode, use `<NUM>a<Chars><ESC>` to input `<NUM>` repeated
`<Chars>`.

## References

+ [Repeating characters in VIM insert mode](https://stackoverflow.com/q/5054128/6064933)

# How do I execute a normal mode command in insert mode?

`Ctrl-O` is used to leave insert mode, execute one normal mode
command and go back to insert mode. For example, if you want to see your
`runtimepath` value, you can first press `ctrl-o` and then use `:echo
&runtimepath` to see the option's value.

# Wrap selection with quotes or other characters

Install [vim-surround](https://github.com/tpope/vim-surround). Go to visual
block mode (press `v` in normal mode), select text you want to wrap, press `S`,
and press the wrapping characters such `'` or `"` or `{`.

If you want to indent the code and as well as wrap it, then you need to go to
visual line mode (press `V` in normal mode)

## References

+ https://superuser.com/questions/875095/adding-parenthesis-around-highlighted-text-in-vim
+ https://github.com/tpope/vim-surround/blob/master/doc/surround.txt

# Rename multiple occurrences of a variable

First, search the variable you want to rename. Then press `cgn` to change it.
Go back to normal mode, press `.` (dot), the next match will be replaced with
the new name. If you want to skip some match, press `n`.

It is not as powerful as the Sublime Text multiple cursor feature, but should
suffice for refactoring your code most of the time.

## Reference

+ [How to selectively change a hightlighted string?](https://vi.stackexchange.com/q/5761/15292)

# Search Unicode characters using its code point

For example, if we would like to search `a` (Unicode code point is `u+0041`) in
Neovim, the correct format is `/\%u0041`, see `:h /character-classes` for more
info.

## References

+ [regex unicode character in vim](https://stackoverflow.com/q/3016965/6064933)
