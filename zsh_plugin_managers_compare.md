---
title: "My Experience with Several Zsh Plugin Managers"
date: 2019-10-08 00:02:01+0800
tags: [Zsh]
categories: [Linux]
---

In this post, I share my experience and opinions on several popular Zsh plugin
managers.

<!--more-->

# zplug

I have written about [zplug](https://github.com/zplug/zplug) in [this post](https://jdhao.github.io/2019/02/19/zsh_advanced_configuration/#plugin-management-with-zplug).
According to [this gist](https://gist.github.com/laggardkernel/4a4c4986ccdcaf47b91e8227f9868ded),
zplug is slow in loading plugins due to its poor design.

## My feeling

It has a lot of features. The speed of installing plugins is acceptable but not
so fast. Other than that, it is enough for daily usage.

# Antibody

[Antibody](https://github.com/getantibody/antibody) is proposed to solve the
performance issues with antigen. First, we need to install antibody:

```bash
curl -sfL git.io/antibody | sh -s - -b $HOME/local/bin
```

The `-b` option specify where you want to install antibody. Antibody will be
installed under `$HOME/local/bin`.

## How to use

After installing antibody, a typical way to install plugins is like the
following:

```zsh
# initialize antibody
source < (antibody init)

antibody bundle zsh-users/zsh-completions
antibody bundle caarlos0/zsh-open-github-pr
# use the oh-my-zsh plugins
antibody bundle robbyrussell/oh-my-zsh path:plugins/aws
antibody bundle zsh-users/zsh-syntax-highlighting
```

Antibody can use plugins from the oh-my-zsh repo with the `path` annotations
shown above.

## My feeling

Antibody is fast in installing the plugins, but it seems that there is no hint
or progress bar informing the user of the progress. It also has less feature
compared to other plugin managers.

Ref:

+ [Supercharge your Terminal with Zsh](https://callstack.com/blog/supercharge-your-terminal-with-zsh/)

# zinit

<font color="blue">Update-2020-07-28: Previously zinit is known as zplugin, but the author has
decided to rename it to zinit. For more details, see
[here](https://github.com/zdharma/zinit/issues/235), [here](https://github.com/zdharma/zinit/issues/290) and [here](https://www.reddit.com/r/zsh/comments/dgw2ss/newname_poll_for_the_zplugin_project/).
</font>

[zinit](https://github.com/zdharma/zinit) is also an advanced plugin manager
with rich features. In fact, I was overwhelmed when I started using it. It is
simple to install, just run the below command:

```zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/zdharma/zinit/master/doc/install.sh)"
```

## How to use

To install normal plugins, zinit works like other plugin managers:

```
zinit load softmoth/zsh-vim-mode
zinit load zsh-users/zsh-autosuggestions
zinit load zdharma/fast-syntax-highlighting
```

zinit also has the so-called [ice modifiers](https://github.com/zdharma/zinit#ice-modifiers),
which are used to modify the behaviour of the plugin installation command
below. For example, to install the [pure](https://github.com/sindresorhus/pure)
theme, we can use the following command:

```zsh
zinit ice pick"async.zsh" src"pure.zsh"
zinit light sindresorhus/pure
```

It means that we will first source `async.zsh` under the repo and then source
`pure.zsh`.

## Install oh-my-zsh plugins

To install oh-my-zsh plugins, we need to use `zinit snippet` command.
Oh-my-zsh plugins start with the `OMZ::` prefix.  Below I show how to install
single-files OMZ plugin and multiple-file OMZ plugins.

```zsh
 # Install a OMZ plugin with multiple files
 zinit ice svn lucid
 zinit snippet OMZ::plugins/pip

 # Install a OMZ plugin with single file
 zinit ice wait lucid
 zinit snippet OMZ::plugins/fzf/fzf.plugin.zsh
```

## My feeling

zinit has so many ice modifiers and so many features that new users may find it
hard to understand all of this. It also has a lot of commands that I do not
understand. In summary, it has steeper learning curves compared to other plugin
managers.

# References

- [zinit wiki](http://zdharma.org/zinit/wiki/INTRODUCTION/).
- [Load oh-my-zsh plugin from a folder](https://www.reddit.com/r/zplugin/comments/ahpddw/how_to_load_an_ohmyzsh_plugin_from_a_folder/).
- [Comparison of different zsh plugin managers](https://gist.github.com/laggardkernel/4a4c4986ccdcaf47b91e8227f9868ded).
