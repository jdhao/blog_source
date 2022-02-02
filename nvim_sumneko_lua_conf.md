---
title: "Setting up sumneko lua for Nvim-lsp"
date: 2021-08-12T00:14:12+08:00
draft: false
tags: [LSP]
categories: [Nvim]
---
<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202202020047792.jpg" width="600">
</p>

In this post, I will share how to set up [sumneko lua](https://github.com/sumneko/lua-language-server) for [nvim-lspconfig](https://github.com/neovim/nvim-lspconfig).

<!--more-->

# Install sumneko lua

First, we need to install its dependency packages.

## ninja

On Linux, install ninja from its binary release:

```bash
wget https://hub.fastgit.org/ninja-build/ninja/releases/download/v1.10.2/ninja-linux.zip

mkdir -p $HOME/local/bin
unzip ninja-linux.zip -d $HOME/local/bin
```

On macOS, install ninja via homebrew:

```
brew install ninja
```

## C++ compiler

We need to install a cpp compile that support c++ 17, gcc-7.3 is fine. Clang
12.0 is also fine.

## build

Build lua-language-server:

```bash
mkdir -p $HOME/tools/ && cd $HOME/tools
git clone --depth=1 https://hub.fastgit.org/sumneko/lua-language-server

cd lua-language-server
# if the cloning speed is too slow, edit .gitmodules and replace github.com
# with hub.fastgit.org, which should be faster than github.
git submodule update --init --recursive

# build on Linux
cd 3rd/luamake
compile/install.sh
cd ../..
./3rd/luamake/luamake rebuild
```

After that, we should add the path to lua-language-server to `PATH`:

```bash
# For macOS
export PATH="$HOME/tools/lua-language-server/bin/macOS:$PATH"

# For Linux
export PATH="$HOME/tools/lua-language-server/bin/Linux:$PATH"
```

# Config for nvim-lspconfig

Following the [instructions on the nvim-lspconfig repo](https://github.com/neovim/nvim-lspconfig/blob/master/CONFIG.md#sumneko_lua), it is
pretty straight forward to config sumneko-lua for Nvim. Here is a working config for me:

```lua
local sumneko_binary_path = vim.fn.exepath('lua-language-server')
local sumneko_root_path = vim.fn.fnamemodify(sumneko_binary_path, ':h:h:h')

local runtime_path = vim.split(package.path, ';')
table.insert(runtime_path, "lua/?.lua")
table.insert(runtime_path, "lua/?/init.lua")

require'lspconfig'.sumneko_lua.setup {
    cmd = {sumneko_binary_path, "-E", sumneko_root_path .. "/main.lua"};
    settings = {
        Lua = {
        runtime = {
            -- Tell the language server which version of Lua you're using (most likely LuaJIT in the case of Neovim)
            version = 'LuaJIT',
            -- Setup your lua path
            path = runtime_path,
        },
        diagnostics = {
            -- Get the language server to recognize the `vim` global
            globals = {'vim'},
        },
        workspace = {
            -- Make the server aware of Neovim runtime files
            library = vim.api.nvim_get_runtime_file("", true),
        },
        -- Do not send telemetry data containing a randomized but unique identifier
        telemetry = {
            enable = false,
        },
        },
    },
}
```

# References

+ https://github.com/sumneko/lua-language-server/wiki/Build-and-Run-(Standalone)
