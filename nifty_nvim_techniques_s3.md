---
title: "Nifty Nvim Techniques Which Make My Life Easier -- Series 3"
date: 2019-05-14 22:37:04+0800
tags: [search, Unicode, Vim]
categories: [Nvim]
---

This is the 3rd post of my post series on nifty Nvim/Vim techniques that will make my editing experience easier.

<details>
<summary><font size="3" color="red">Click here to check other posts in this series.</font></summary>

+ Series 11: https://jdhao.github.io/2021/11/22/nifty_nvim_techniques_s11/
+ Series 10: https://jdhao.github.io/2021/06/17/nifty_nvim_techniques_s10/
+ Series 9: https://jdhao.github.io/2021/01/07/nifty_nvim_techniques_s9/
+ Series 8: https://jdhao.github.io/2020/11/11/nifty_nvim_techniques_s8/
+ Series 7: https://jdhao.github.io/2020/09/22/nifty_nvim_techniques_s7/
+ Series 6: https://jdhao.github.io/2019/12/21/nifty_nvim_techniques_s6/
+ Series 5: https://jdhao.github.io/2019/11/11/nifty_nvim_techniques_s5/
+ Series 4: https://jdhao.github.io/2019/09/17/nifty_nvim_techniques_s4/
+ Series 2: https://jdhao.github.io/2019/04/17/nifty_nvim_techniques_s2/
+ Series 1: https://jdhao.github.io/2019/03/28/nifty_nvim_techniques_s1/
</details>

<!--more-->

# How do I use last search pattern in substitute command?

As is often the case, when we want to replace some patterns, we may not be able
to write the correct regular expression in one pass and have to refine it many
times. I often search first to make sure that the search pattern is correct.
Then I manually type the search pattern again in `substitute` command to
replace it with the text I want. This is acceptable when the pattern is simple
to type. It becomes unacceptable once the search patterns are complicated.

To use the last search pattern, you can keep the search part empty in
`substitute` command, e.g., `%s//REPLACE/g`, which will replace all occurrences
of last search pattern with `REPLACE`. Alternatively, you could use `<C-R>/` to
input your last search pattern, that is, first press <kbd>Ctrl</kbd>+
<kbd>R</kbd> and then press <kbd>/</kbd>[^1].

## References

+ [Substitue last search](https://vim.fandom.com/wiki/Substitute_last_search)

[^1]: see `:h c_CTRL-R` for more information.

# How do I write the output of a command or built-in function to a buffer?

A least three ways are possible (take function
[`strftime()`](https://neovim.io/doc/user/eval.html#strftime()) and command
`:scriptnames` (`:h :scriptnames`) for an example).

1. Use `<C-R>=` in insert mode. In insert mode, press
   <kbd>Ctrl</kbd>+<kbd>R</kbd> followed by <kbd>=</kbd>, then type the
   function (e.g., `strftime('%c')`) or use `execute()` to run a command (e.g.,
   `execute('scriptnames')`).  The output will inserted right after the current
   cursor.

2. Use `:put=` in command mode. In command mode, first input `put=`, then input
   the function or `execute()` command.  The output will be put below the
   current cursor line.

3. Use `redir`. We first use `redir` to redirect the output to a Vim register
   and then paste the content of the register to current buffer. For functions,
   use the following

    ```vim
    :redir @a | echo strftime('%c') | redir END<CR>
    ```

    For commands, use:

    ```vim
    :redir @a | silent scriptnames |redir END<CR>
    ```

    This will redirect the output of function `strftime()` or command
    `scriptnames` to register `a`. To paste the content of register `a`, use
    `"ap` in normal mode.

    You can also directly redirect the output to current file:

    ```
    :redir >> % | silent scriptnames|redir END|edit
    ```

    , which may be more convenient sometimes.
4. Use `let` with a register. This is similar to using `redir`, but more
   concise. For function outputs, use:

   ```vim
   :let @a = strftime("%c")<CR>
   ```

   For command outpus, use:

   ```vim
   :let @a = execute("silent scriptnames")<CR>
   ```

   Then you can paste the register content to current buffer with `"ap`

Using `<C-R>=` in insert mode is not good. It will write the output line by
line, thus are very slow if your output has hundreds or thousands of the lines.
The line indentation may also break if you use this method.

## References

+ [How to insert the result of a command into the text in vim?](https://unix.stackexchange.com/q/8101/221410)
+ [Dump the output of internal vim command into buffer](https://vi.stackexchange.com/q/8378/15292)
+ [How to redirect ex command output into current buffer or file?](https://stackoverflow.com/q/2573021/6064933)

# Why can't I search some Unicode characters?

For example, given the following text:

```text
免费	免费 🆓️️
```

if you place the cursor on the emoji and use `ga`, the output is:

```
<🆓> 127379, Hex 0001f193, Octal 370623 < ️> 65039, Hex fe0f, Octal 177017 < ️> 65039, Hex fe0f, Octal 177017
```

It shows that this character is actually made up of three Unicode characters!
This is called [variant form](https://en.wikipedia.org/wiki/Variant_form_(Unicode)) and the Unicode
character `U+FE0F` is called a *variant selector* .  There are other forms of
multi-Unicode-char characters, for example, [combining characters](https://en.wikipedia.org/wiki/Combining_character),
where the character is made up of a base character and one or more decoration
characters. In Vim, the main character is called base character and the other
character that follows is called composing character.

If you use search the base character or the composing character using their
Unicode code points, you will find disappointedly that no pattern are found.

It turns out the way we search the characters are wrong. To search the base
character, you should follow it by `\%C` (in no-magic mode) or `%C` (in magic
mode). For example, to search the above base character in the emoji, use

+ Magic mode: `\v%U1f193%C`
+ No-Magic Mode: `\%U1f193\%C`

To search the composing character, it is more complicated. You should type
`/<Ctrl-V>ufe0f`:

+ `<Ctrl-v>u`: start Unicode input
+ `fe0f`: the code point of the composing character

Then press Enter to search.

## References

+ [Some emojis (e.g. ☁) have two unicode, u'\u2601' and u'\u2601\ufe0f'. What does u'\ufe0f' mean? Is it the same if I delete it?](https://stackoverflow.com/q/38100329/6064933)
+ [Can I search for a Unicode combining character in Vim?](https://vi.stackexchange.com/q/3557/15292)
+ [regex unicode character in vim](https://stackoverflow.com/q/3016965/6064933)

# How do I clear the message on the status line?

If you set the option [`laststatus`](https://neovim.io/doc/user/options.html#'laststatus') to 2,
after searching a word using `*`, you will find that the text is not cleared
automatically. To clear the text , you can press <kbd>Ctrl</kbd>+<kbd>L</kbd> .

## References

+ [How to clear output of function call in VIM?](https://stackoverflow.com/a/55990480/6064933)
+ [How to make vi redraw screen?](https://stackoverflow.com/q/1117725/6064933)

# How do I use a variable when calling shell command inside Neovim?

You can use [`:execute`](https://neovim.io/doc/user/eval.html#:execute), which
will evaluate the expression followed before executing it. Suppose that you
have defined a download URL variable `URL`, in order to download it using
`wget` inside Neovim, you can use the following command:

```
:execute '!wget ' . URL
```

Note the space inside `'!wget '` because we use `.` to concatenate the
arguments behind `execute` to form an expression. Without the space, the
command you are going to run is `!wget{the_content_of_URL}`, which is
apparently wrong.

## References

+ [function to call shell script with arguments](https://vi.stackexchange.com/q/2406/15292)
+ [Using variables in a vim shell command](https://stackoverflow.com/q/6948024/6064933)

# How to remove the trailing newline when using the `system()` function?

When I wanted to assign the output of an external shell command to a Vim
variable using `system()` function, I found that a trailing newline character
was present in the returned string, which breaks a lot of things. For example,

```vim
let python_path = system('which python')
```

the variable `python_path` will contain an annoying newline. To remove this
newline character, you can use various methods:

+ `substitute()`: you can replace the trailing newline with an empty string to remove it:

    ```vim
    substitute(python_path, '\n$', '', 'g')
    ```

+ string indexing: you can also utilize string indexing (see `:h expr-[:]`) if
  you are sure that there is only one newline at the end of the string:

    ```vim
    " remove last byte from the string
    let new_path=python_path[:-2]
    ```

## References

+ [Get rid of null character in vim variable?](https://superuser.com/q/935574/736190)
+ http://vim.1045645.n5.nabble.com/Trimming-newlines-from-calls-to-system-td5720467.html
+ [How to remove ^@ character returned by `system()`?](https://vi.stackexchange.com/q/17704/15292)
+ [How do you “chomp” a string in Vim?](https://vi.stackexchange.com/q/2867/15292)
