---
title: "Set up Inverse Search for LaTeX with VimteX and Neovim"
date: 2021-02-20T22:31:34+08:00
draft: false
tags: [LaTeX]
categories: [Nvim]
---

<details>
<summary><font size="2" color="red">Update log</font></summary>

+ <font color="blue">2021-10-22: Update the script to write server name.</font>
+ <font color="blue">2021-10-10: Fix typo and minor tweak.</font>
</details>

Since [`--remote` option hasn't been restored by Neovim](https://github.com/neovim/neovim/issues/1750), it is actually not very
straightforward to set up inverse search for PDF files when we are editing
large LaTeX files with the help of [VimTeX](https://github.com/lervag/vimtex).

I spend some time figuring out how to do it on both Windows and macOS and
succeed. I thought it would be helpful for people looking for the solution.

<!--more-->

# First step

Although Neovim removed the `--remote` option, it has a complete [RPC api](https://neovim.io/doc/user/api.html) by
which a client can talk to it and controls its behavior. When Neovim starts, it
always starts a server[^1]. The [neovim-remote](https://github.com/mhinz/neovim-remote) project utilizes the RPC api of neovim to
mimic the `--remote` behavior and its friends. First, we need to install this package:

```
pip install neovim-remote
```

It comes with an executable named `nvr`. To connect to a running Neovim
instance and run a command, we can use the following command:

```
nvr --servername server_address -c "echo 'hello'"
```

Option `--servername` specifies a valid Neovim server address (i.e., the value
`v:servername`). Option `-c` means to run a command in the specified Neovim
instance.

# Second step

Each time we start a new neovim instance, the server address changes. It is
cumbersome to copy this address when we want to communicate with this Neovim
instance. We can write the server address to a temporary file and read the
server address from that file when running the `nvr` command.

Add the following setting to nvim config:

```vim
function! s:write_server_name() abort
  let nvim_server_file = (has('win32') ? $TEMP : '/tmp') . '/vimtexserver.txt'
  call writefile([v:servername], nvim_server_file)
endfunction

augroup vimtex_common
  autocmd!
  autocmd FileType tex call s:write_server_name()
augroup END
```

When we open a LaTeX source file, the server name will be written to
`vimtexserver.txt` so that we can later read it easily.

# Third step

Now we need to set up the corresponding PDF viewer to do inverse search. The
setup differs based on your system and PDF viewer.

## Skim on macOS

Open [Skim PDF viewer](https://skim-app.sourceforge.io/), open the
`Preferences` menu (shortcut: `Command+,`) and go to `Sync` page. Set up the
section `PDF-Tex Sync support`, use the following settings:

+ Preset: `Custom`
+ Command: `nvr`
+ Arguments: `` --servername `cat /tmp/vimtexserver.txt` +"%line" "%file" ``

To start inverse search, press <kbd>Shift</kbd> and <kbd>Command</kbd>, then
click the text you want to inv-search.

## Sumatra PDF on Windows

Open [Sumatra PDF](https://www.sumatrapdfreader.org/free-pdf-reader), go to `Settings --> Options`, in the bottom part, there is a
section **Set inverse search command-line**, put the following command there:

```batch
cmd /c for /F %i in ('type C:\Users\ADMINI~1\AppData\Local\Temp\vimtexserver.txt') do nvr --servername %i -c "normal! zzzv" +"%l" "%f"
```

The meaning of `%f` and `%l`:

+ `%f`: tex source file path corresponding to current pdf.
+ `%l`: line in the original tex file.

I know the above command is complicated and looks super weird, but it's the
only way that works out of the numerous other ways I have tried. Note that `cmd
/c` is needed, without which, Sumatra PDF will complain that the inverse search
command is wrong:

> Can not start inverse search command. Please check the command line in the settings.

Double click somewhere in the PDF file, your cursor in nvim/nvim-qt should go
to the corresponding line in the file buffer.

I have also posted this setup on VimteX repo, check [this issue](https://github.com/lervag/vimtex/issues/1964).

# Conclusion

In this post, I give a summary on how to set up inverse search for Neovim on
different platforms. After all these setup, inverse search should work out of
the box without any manual labor.

# References

+ echo the content of a file to command line on Windows
    + https://superuser.com/q/162713/736190
    + https://superuser.com/q/256651/736190
+ Batch/cmd equivalent of Bash backtick
    + https://stackoverflow.com/q/2768608/6064933
    + https://superuser.com/q/289344/736190

[^1]: You can find the name of the server by running command `:echo v:servername` inside Neovim.
