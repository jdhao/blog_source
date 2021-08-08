---
title: "Building Zsh from Source and Configuring It on CentOS"
date: 2018-10-13 18:29:00 +0800
tags: [shell, Zsh]
categories: [Linux]
---

**Update**: this is my first post on Zsh install and configuration. For a list
of useful plugins I use to boost my productivity, see [this post](https://jdhao.github.io/2019/02/19/zsh_advanced_configuration/).

[Zsh](http://www.zsh.org/) is a powerful shell with better auto-completion and
richer features. There are also various plugins to enrich your experience of
using Zsh. In this post, I will give a brief summary on how to install and set
up zsh.

<!--more-->

# Install

## Build and install ncurses

The installation of zsh requires [ncurses](https://invisible-island.net/ncurses/announce.html).
On some systems, the ncurses development package is missing. If you do not have
root privilege, you need to install ncurses from source:

```bash
wget ftp://ftp.gnu.org/gnu/ncurses/ncurses-6.1.tar.gz
tar xf ncurses-6.1.tar.gz
cd ncurses-6.1
./configure --prefix=$HOME/local CXXFLAGS="-fPIC" CFLAGS="-fPIC"
make -j && make install
```

The `CXXFLAGS="-fPIC" CFLAGS="-fPIC"` flags are important. Without them, you
will encounter errors when you try to build Zsh. See more information [here](https://unix.stackexchange.com/q/123597/221410).

## Build and install Zsh

Latest code of zsh can be found [here](https://sourceforge.net/projects/zsh/).
It is easy to install:

```bash
ZSH_SRC_NAME=$HOME/packages/zsh.tar.xz
ZSH_PACK_DIR=$HOME/packages/zsh
ZSH_LINK="https://sourceforge.net/projects/zsh/files/latest/download"

if [[ ! -d "$ZSH_PACK_DIR" ]]; then
    echo "Creating zsh directory under packages directory"
    mkdir -p "$ZSH_PACK_DIR"
fi

if [[ ! -f $ZSH_SRC_NAME ]]; then
    curl -Lo "$ZSH_SRC_NAME" "$ZSH_LINK"
fi

tar xJvf "$ZSH_SRC_NAME" -C "$ZSH_PACK_DIR" --strip-components 1
cd "$ZSH_PACK_DIR"

./configure --prefix="$HOME/local" \
    CPPFLAGS="-I$HOME/local/include" \
    LDFLAGS="-L$HOME/local/lib"
make -j && make install
```

Since I do not have root privilege, I can not use [`chsh`](https://linux.die.net/man/1/chsh)
to change the default logging shell. In order to use Zsh as the default shell,
we can add the following settings to `.bash_profile`,

```bash
export PATH=$HOME/local/bin:$PATH
export SHELL=`which zsh`
[ -f "$SHELL" ] && exec "$SHELL" -l
```

Then source `.bash_prfile` to make the changes take effect. With this setting,
zsh will be activated each time you log into the system.

# Install oh-my-zsh

[Oh-my-zsh](https://ohmyz.sh/) is framework for Zsh with rich features. To
install, run the below command:

```
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

oh-my-zsh ships with a bunch of plugins to enhance your experience of using
zsh. You can enable these plugins in the `.zshrc` file. For example,

```
plugins=(
  git pip vi-mode
)
```

The above setting will enable git, pip and vi-mode plugin. The complete list of
plugins are listed [here](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins).

For other plugins that are not shipped by default. You have to install them
yourself. You may want to try [zsh-autosuggestion](https://github.com/zsh-users/zsh-autosuggestions).
It can give you suggestions when you type command, based on your command
history.

First, we need to install this plugin:

```
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

In `.zshrc`, add autosuggestions to plugins:

```
# different plugins should be separated by space
plugins=(zsh-autosuggestions)
```

Do not forget to source `.zshrc` to make the change take effect.

When you type a command, you will get suggestions. You can use `->`(right arrow
key) or `End` key to accept a suggestion. If the suggestion is not what you
want, it will disappear as you continue typing.

## Install zsh-syntax-highlighting

This plugin will highlight your command in command line to enhance your
experience. Use the following command to install this plugin:

```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

Add it to plugins:

```
plugins=( [plugins...] zsh-syntax-highlighting)
```

Then `source ~/.zshrc`.

# Install themes

## Powerlevel9k

You can use various themes in Zsh. For example, [powerlevel9k](https://github.com/bhilburn/powerlevel9k#installation).
Install guide is [here](https://github.com/bhilburn/powerlevel9k/wiki/Install-Instructions#option-2-install-for-oh-my-zsh)

```
git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k
```

And in `.zshrc`, use

```
ZSH_THEME="powerlevel9k/powerlevel9k"
```

### Install a powerline font

powerlevel9k works well if you install a [powerline font](https://github.com/powerline/fonts).

### Customization

To change the default prompt style, put the following after
`ZSHTHEME="powerlevel9k/powerlevel9k"`:

```
POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(dir vcs)
POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(status root_indicator background_jobs)
```

You can customize more settings following the theme documentation [here](https://github.com/bhilburn/powerlevel9k#prompt-customization).

## Spaceship

[Spaceship](https://github.com/denysdovhan/spaceship-prompt) is a simple and
elegant theme. If you want simplicity, you may like it.

```
git clone https://github.com/denysdovhan/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt"
ln -s "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme"
```

Set `ZSH_THEME="spaceship"` in your `.zshrc` and source your zsh config to use
this theme.

# how to change prompt

In order to show current directory relative to `$HOME`, use the following setting
in `.zshrc`:

```
export PS1="[%~]$ "
```

More prompt parameters can be found [here](http://zsh.sourceforge.net/Doc/Release/Prompt-Expansion.html).

Finally, `source ~/.bash_profile` to change the shell to zsh.

# Conclusion

In this post, I gave a summary on how to install zsh and make it your default
shell. I also introduced how to install a theme and plugin with the popular
framework oh-my-zsh. You can read the zsh and oh-my-zsh documentation for more
customizations.

You can find my complete zsh configuration [here](https://github.com/jdhao/dotfiles/blob/master/.zshrc).

# References

+ Use zsh as default shell without root right: https://unix.stackexchange.com/a/136424/221410
+ Change command prompt: https://stackoverflow.com/q/25090295/6064933
