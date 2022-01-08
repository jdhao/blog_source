---
title: "Migration from Nvim 0.5.1 to Nvim 0.6.0"
date: 2021-12-01T22:25:30+08:00
draft: false
tags: [LSP]
categories: [Nvim]
---

About five months ago, the biggest update of Nvim comes with its [v0.5.0
release](https://github.com/neovim/neovim/releases/tag/v0.5.0). With v0.5, we finally get the official LSP support in Nvim core and
better Lua support, among other features and bug fixes[^1].

TL;DR: My LSP-related config can be found [here](https://github.com/jdhao/nvim-config/blob/master/lua/config/lsp.lua).

<!--more-->

This Tuesday, the Nvim [v0.6.0 version](https://github.com/neovim/neovim/releases/tag/v0.6.0) is released. During the five
months, the LSP module has been revamped and enhanced, though with some breaking changes.

Below are some the changes we need or can make based on this release.

# Diagnostics and LSP changes

Initially, diagnostic module is part of `vim.lsp` module. In order to support
external plugins such as [null-ls.nvim](https://github.com/jose-elias-alvarez/null-ls.nvim), the nvim team has refactor the
diagnostic module to its module `vim.diagnostic`. So we need to change our config accordingly.

1. `vim.lsp.diagnostic.show_line_diagnostics()` has been changed to
   `vim.diagnostic.open_float()`. Previously, there is no easy to show
   diagnostic source unless with some hack, you can now show source in
   diagnostics in `open_float()` easily:

    ```lua
    vim.diagnostic.open_float(nil, {
        source = 'always'
    })
    ```

2. `vim.lsp.diagnostic.goto_prev()` and `vim.lsp.diagnostic.goto_next()` has
   been renamed to `vim.diagnostic.goto_prev()` and `vim.diagnostic.goto_next()`
   respectively.

3. `vim.lsp.diagnostic.set_loclist()` and `vim.lsp.diagnostic.set_qflist()`
   has been renamed to `vim.diagnostic.setloclist()` and
   `vim.diagnostic.setqflist()` instead.

4. Diagnostics signs has been renamed, for example (old --> new):

    + `LspDiagnosticsSignError` --> `DiagnosticSignError` (`Lsp` is removed, `Diagnostics` is changed to singular from `Diagnostic`)
    + `LspDiagnosticsSignWarning` --> `DiagnosticSignWarn`
    + `LspDiagnosticsSignInformation` --> `DiagnosticSignInfo`
    + `LspDiagnosticsSignHint` --> `DiagnosticSignHint`

    Also, related highlight has been renamed too:

    + `DiagnosticsDefaultError` --> `DiagnosticSignError`
    + `DiagnosticsDefaultWarning` --> `DiagnosticSignWarn`
    + `DiagnosticsDefaultInformation` --> `DiagnosticSignInfo`
    + `DiagnosticsDefaultHint` --> `DiagnosticSignHint`

    Now, we can use the following lua snippet to change diagnostic signs:

    ```lua
    vim.fn.sign_define("DiagnosticSignError", { text = "✗", texthl = "DiagnosticSignError" })
    vim.fn.sign_define("DiagnosticSignWarn", { text = "!", texthl = "DiagnosticSignWarn" })
    vim.fn.sign_define("DiagnosticSignInformation", { text = "", texthl = "DiagnosticSignInfo" })
    vim.fn.sign_define("DiagnosticSignHint", { text = "", texthl = "DiagnosticSignHint" })
    ```

For more details on diagnostic change, see [this commit](https://github.com/neovim/neovim/pull/15585/commits/a5bbb932f9094098bd656d3f6be3c58344576709).

# Changes to the default

There are also changes to options and mappings that you might be interested.

Option default value changes:
 + `backupdir` can now be created automatically and double backslash is used, see [this commit](https://github.com/neovim/neovim/commit/460019366e58e1bcd42959f76494e38bd895e762).
 + option `inccommand` is set to `nosplit`
 + `set nojoinspaces` by default

Mapping changes:
 + `<C-L>` now defaults to `nohlsearch` and `diffupdate`, see [this commit](https://github.com/neovim/neovim/commit/0aa8128aaa9e6f714372f7ea7299f4115de0a962).
 + In normal mode, `Y` is mapped to `y$`, see [this commit](https://github.com/neovim/neovim/commit/5a111c1b02bbfbc2b42df11d7205153be0893dff), no need for `nnoremap Y y$` anymore.

[^1]: The nvim 0.5 release note is [here](https://github.com/neovim/neovim/commit/a5ac2f45ff84a688a09479f357a9909d5b914294).
