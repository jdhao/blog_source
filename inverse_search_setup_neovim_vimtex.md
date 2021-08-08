---
title: "Set up inverse search for Neovim and Vimtex"
date: 2021-02-20T22:31:34+08:00
draft: false
tags: [LaTeX]
categories: [Nvim]
---

Since [`--remote` option hasn't been restored by Neovim](https://github.com/neovim/neovim/issues/1750), it is actually not very
straightforward to set up inverse search for PDF files when we are compiling
LaTeX files with the help of Vimtex.

I spend some time figuring how to do it on both Windows and macOS and succeed.
I thought it would be helpful for people looking for this issue.

<!--more-->

# First step

Although Neovim removed the `--remote` option, but it has a complete [RPC api](https://neovim.io/doc/user/api.html) by
which client can talk to it and control its behavior. When Neovim starts, it
always start a server[^1]. The [neovim-remote](https://github.com/mhinz/neovim-remote) project utilizes the RPC api of neovim to
mimic the `--remote` behavior and its friends. First, we need to install this package:

```
pip install neovim-remote
```

It comes with an executable named `nvr`. To connect to a running Neovim
instance and run some command, we can run the following command:

```
nvr --servername server_address -c "echo 'hello'"
```

`--servername` option specify a valid Neovim server address (i.e., the value `v:
servername`), `-c` option means to run a command in the specified Neovim
instance.

# Second step

Since each time when Neovim starts, the server address will change, it is
cumbersome to copy this address when we when to control the Neovim instance. We
can write the server address to temp file and read the server address from that
file when we run the nvr command.

Add the following setting to nvim config:

```vim
function! SetServerName()
  if has('win32')
    let nvim_server_file = $TEMP . "/curnvimserver.txt"
  else
    let nvim_server_file = "/tmp/curnvimserver.txt"
  endif
  let cmd = printf("echo %s > %s", v:servername, nvim_server_file)
  call system(cmd)
endfunction

augroup vimtex_common
    autocmd!
    autocmd FileType tex call SetServerName()
augroup END
```

When we open a LaTeX source file, the server name will be written to
`curnvimserver.txt` so that we can later read it easily.

# Third step

Now we need to set up the corresponding PDF viewer to do inverse search. The
setup differs based on the system and PDF viewer.

## Skim on macOS

Open Skim PDF viewer, open the `Preferences` menu and go to `Sync` page. Set
the part `PDF-Tex Sync support`, use the following settings:

+ Preset: `Custom`
+ Command: `nvr`
+ Arguments: `` --servername `cat /tmp/curnvimserver.txt` +"%line" "%file" ``

To start inverse search, press Shift and Command key, then click the text you
want to inv-search.

## Sumatra PDF on Windows

Open Sumatra PDF, go to `Settings --> Options`, in the bottom part, there is a
section *Set inverse search command-line*, put the following command there:

```
cmd /c for /F %i in ('type C:\Users\ADMINI~1\AppData\Local\Temp\curnvimserver.txt') do nvr --servername %i -c "normal! zzzv" +"%l" "%f"
```

+ `%f`: it means the tex source file path corresponding to the current pdf.
+ `%l`: it means the line in the original tex file.

I know the above command is complicated and looks super weird, but it's the
only way that works out of the numerous other ways that I have tried.  Note
that `cmd /c` is needed, without which Sumatra PDF will complain that the
inverse search command is wrong:

> Can not start inverse search command. Please check the command line in the settings.

Double click somewhere in the PDF file, your cursor in nvim/nvim-qt should go
to the corresponding line in the file buffer.

I have also post this setup on Vimtex repo, see [this issue](https://github.com/lervag/vimtex/issues/1964).

# Conclusion

In this post, I give a summary on how to set up inverse search for Neovim on
different platforms. After all this setup, inverse search should work out of
box without any manual labor.

# Refs

+ echo the content of file to command line on Windows
    + https://superuser.com/q/162713/736190
    + https://superuser.com/q/256651/736190
+ Batch/cmd equivalent of Bash backtick
    + https://stackoverflow.com/q/2768608/6064933
    + https://superuser.com/q/289344/736190

[^1]: You can find the name of the server by running command `:echo v:servername` inside Neovim.
