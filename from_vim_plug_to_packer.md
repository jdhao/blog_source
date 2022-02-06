---
title: "Migrating from vim-plug to Packer.nvim"
date: 2021-07-11T23:05:22+08:00
draft: false
tags: []
categories: [Nvim]
---

<details>
<summary><font size="3" color="red">Update log</font></summary>

+ <font color="blue">2022-02-05: add new autocmd for auto-running command `PackerCompile`.</font>
</details>

Over the past two years, I have been using [vim-plug](https://github.com/junegunn/vim-plug) as my Nvim plugin manager.
Vim-plug is powerful, fast and amazing, especially considering that all its features are contained in a single vim script.
It uses the old way to manage plugins, i.e., it will manage the loading of plugins directly.

<!--more-->

# Packages in Vim

In Vim/Nvim, we also have another way to manage plugins, that is, by using `packages`.
It is a new way of organizing plugins. We can split plugins into two types, `opt` plugins and `start` plugins.
If we put plugins in `opt` directory under a package, the plugins will not be loaded during initialization.
If we want to use plugin `foo` later, we use `:packadd foo` to load it.
For plugins under `start` directory, they will be loaded[^1] by Vim automatically after initialization.

For `opt` plugin `foo`, Vim will look for `pack/xxx/opt/foo` under paths in the option `packpath`.
For `start` plugins, Vim will look for them under `pack/xxx/start/` directory under paths in `packpath`.

# Now enter packer.nvim!

Actually, there are several plugin managers that use the package feature to manage plugins,
for example, [minpac](https://github.com/k-takata/minpac) and [vim-packager](https://github.com/kristijanhusak/vim-packager), which are written in pure vim script.

With the release of the long overdue [Nvim 0.5.0](https://github.com/neovim/neovim/releases/tag/v0.5.0) and increasing importance of Lua in configuring and developing Nvim.
I would like to try a plugin manager written in Lua, which also supports packages.
After some survey, I decide to give [packer.nvim](https://github.com/wbthomason/packer.nvim) a try,
which is by far the most powerful and feature-rich plugin manager written in Lua[^2].

I use the following script to install packer.nvim in my computer:

```lua
vim.g.package_home = fn.stdpath("data") .. "/site/pack/packer/"
local packer_install_dir = vim.g.package_home .. "/opt/packer.nvim"

local plug_url_format = ""
if vim.g.is_linux then
  plug_url_format = "https://hub.fastgit.org/%s"
else
  plug_url_format = "https://github.com/%s"
end

local packer_repo = string.format(plug_url_format, "wbthomason/packer.nvim")
local install_cmd = string.format("10split |term git clone --depth=1 %s %s", packer_repo, packer_install_dir)

-- Auto-install packer in case it hasn't been installed.
if fn.glob(packer_install_dir) == "" then
  vim.api.nvim_echo({ { "Installing packer.nvim", "Type" } }, true, {})
  vim.cmd(install_cmd)
end

-- Load packer.nvim
vim.cmd("packadd packer.nvim")

-- the plugin install follows from here
-- ....
```

I put it under `~/.config/nvim/lua/plugins.lua` and in my `init.vim`, I just require it: `lua require('plugins.lua')`.
With the above script, packer will be automatically installed if it hasn't.

# Some issues I met during transition

## Issues for lazy-loaded plugins

Packer's lazy loading of plugins based on some conditions is really cool, but is also error-prone for newbies.
All lazy-loaded plugins are put under `opt` directory.
They are only added to runtimepath by packer when the specified conditions are met.
This is the root cause of many issues related to missing function or modules, etc.

For example, if we use [nvim-compe](https://github.com/hrsh7th/nvim-compe) and lazy-load it, but we require its config during Nvim initialization,
we will see errors during nvim startup:

> module 'compe' not found.

One way to fix this is to use `config` key after the plugin has been loaded.
See for example [here](https://github.com/wbthomason/dotfiles/blob/linux/neovim/.config/nvim/lua/plugins.lua#L123).

```lua
use { 'hrsh7th/nvim-compe', event = 'InsertEnter *', config = [[require('config.compe')]] }
```

## Forget to run PackerCompile after changing plugin settings

After changing plugin configuration, we **must** run `:PackerCompile`.
It will generate a file named `packer_compiled.vim` or `packer_compile.lua` under the directory `~/.config/nvim/plugin`.

When something went wrong, always check if you have run `:PackerCompile` and restarted nvim.
90% time, the issue will go.

To fix the issue once and for all, I add the following autocmd:

```vim
augroup packer_auto_compile
  autocmd!
  autocmd BufWritePost */nvim/lua/plugins.lua source <afile> | PackerCompile
augroup END
```

## No way to retry plugins that failed during installation

Unlike vim-plug, when plugin installation fails, there is no re-try mechanism.
We have to manually run `:PackerInstall` to re-install the failed plugins.

## Plugin loading order

When we want to load plugin A after B and they are both lazy-loaded,
there is no need to use event for plugin A, using `after` condition is enough:

```lua
use {'foo/bar', event = 'VimEnter'}
use {'foo/baz', after = 'bar'}
```

Due to the firing of autocmd event, if we use `event` for both `foo/bar` and `foo/baz`,
`foo/baz` may not be loaded after `foo/bar`. See also [this issue](https://github.com/wbthomason/packer.nvim/issues/461).

## Issues related to lazy-loaded remote plugins

For nvim remote plugins, we need to run command `:UpdateRemotePlugins` to make them work properly.
By running this command, a manifest file called `rplugin.vim` will be generated under `~/.local/share/nvim/`,
which records what kinds of commands a plugin provides (also see `:h remote-plugin`).

For example, for remote plugin semshi, my initial setup is:

```lua
use {'numirias/semshi', run = ':UpdateRemotePlugins', ft = 'python'}
```

It does not work well. When we run `:PackerUpdate` (it will run `: UpdateRemotePlugins` every time we update plugins),
semshi may not be in runtimepath. So semshi-related command will be missing in `rplugin.vim`.
Afterwards, when we open a Python file, we see errors that command `Semshi enable` is not available.

The correct way is to run `:UpdateRemotePlugins` after semshi is loaded:

```lua
use {'numirias/semshi', ft = 'python', config = 'vim.cmd [[UpdateRemotePlugins]]'}
```

This way, we insure that semshi will be work properly.

## Specify the plugin url format

By default, when we use `use 'aaa/bbb'`, packer will try to clone `https://github.com/aaa/bbb.git`.
However, due to speed issue accessing github in certain areas, the user may want to use a mirror site of GitHub,
for example, [fastgit](https://fastgit.org/). Fortunately, this feature has been added recently, [under the request of me](https://github.com/wbthomason/packer.nvim/issues/433) 😃.

Now, you can do the following:

```lua
require('packer').startup(
{
  function(use)
    use 'aaa/bbb'
    use 'foo/bar'
  end,
  config = {
    max_jobs = 16,
    git = {
      default_url_format = 'https://hub.fastgit.org/%s'
    }
  }
})
```

All your plugins will be clone from fastgit instead of GitHub.

# Conclusion

Although there are small issues when switching from vim-plug to packer,
it is mainly because I am new to its lazy-loading feature.
Overall, packer.nvim is a great choice if you want to manage plugins in lua.
Considering that packer is a young plugin and the active development happening in its GitHub repo,
I am sure that it will be maturer in short time.

My packer.nvim config can be found [here](https://github.com/jdhao/nvim-config/blob/590baf4ca95f77418dc6beee80e9ad149cd585d4/lua/plugins.lua).

# References

+ https://github.com/wbthomason/dotfiles/blob/linux/neovim/.config/nvim/lua/plugins.lua
+ Plugin not found issue: https://github.com/lervag/vimtex/issues/1785
+ Plugin functions not available in init.vim: https://github.com/wbthomason/packer.nvim/issues/4

[^1]: Here, `loaded` means the plugin directory will be added the runtimepath.
[^2]: If you want to minimal plugin manager in lua, try [paq](https://github.com/savq/paq-nvim).
