---
title: "Faster Directory Navigation with z.lua"
date: 2019-02-14 18:35:05+0800
tags: [Cmder, shell, Bash, Zsh, Windows]
categories: [tools]
---

If you work with terminals extensively, you may be tired of typing `cd` to go
to directories, especially when directory names are very long.
[z.lua](https://github.com/skywind3000/z.lua) is such a tool to make your life
easier by jumping to the desired directory *smartly*.

<!--more-->

# Install

## Linux (bash)

If you are using bash shell, you need to download the plugin manually. Down and
[z.lua](https://github.com/skywind3000/z.lua/blob/master/z.lua) file and add
the following setting to your `.bashrc`.

```bash
# change /path/to/z.lua to the actual path of z.lua file
eval "$(lua /path/to/z.lua --init bash enhanced once)"
```

## Linux (zsh)

If you are using [zsh](https://en.wikipedia.org/wiki/Z_shell), you can install
this plugin with [antigen](http://antigen.sharats.me/):

```bash
antigen bundle skywind3000/z.lua
```

## Windows (Cmder)

z.lua requires that the [lua]() binary is in your PATH. The easiest way to
install lua binary is to install [LuaDist](http://luadist.org/). Just extract
it to somewhere on your system and add the path the `lua.exe` to your system
PATH.

Then, copy file `z.lua` and `z.cmd` to `$CMDERROOT/vendor` folder and add
`$CMDERROOT/vendor` to your system PATH.

Make sure to reboot your computer after you have updated the system PATH to
make the change take effect.

# How to use

First, you need to `cd` to a few directories after installing z.lua to build
the cd history. After that, to go a directory containing keyword `foo`, use the
following command:

```bash
z foo
```

## Command completion

z.lua supports command completion. To start completion, press `<Tab>` after the
keyword, for example,

```
z foo<Tab>
```


## Interactive selection

Sometimes, several directories may contain the same keywords. You can use `z`
with `-i` option to start interactive path selection:

```bash
z -i foo
```

All directories containing `foo` will be listed with its index, you can then
type a index to go to a specific directory.

## Jump backward to parent folder

To jump backward to parent directories, use `z -b` command. Suppose that you
are in directory `~/github/foo/bar/util`, to go to `foo` directory, simply use
the following command:

```bash
z -b foo
```

## other settings

Other useful settings are:

```bash
export _ZL_MATCH_MODE=1  # enhanced matching mode
export _ZL_ECHO=1  # display the path after cd
export _ZL_ADD_ONCE=1  # only add path if the $PWD is changed
```

We can also create alias for commonly-used command:

```bash
# alias for z.lua
alias zi="z -i"  # interactive selection
alias zb="z -b"  # jump backward
```

# References

+ [Install z.lua under zsh](https://github.com/skywind3000/z.lua/issues/20)
+ [Discussion on HN](https://news.ycombinator.com/item?id=19077891)
