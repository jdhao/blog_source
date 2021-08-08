---
title: "Deoplete Failed to Load at Startup after Updating Python neovim Package"
date: 2018-11-19 18:18:00 +08:00
tags: [Nvim, Windows, CentOS]
categories: [Note]
---

Today when I use `:checkhealth` to check if there are anything wrong with my
Neovim and related packages. I got an info that [Python `neovim`
package](https://github.com/neovim/pynvim) is not up to date. So I tried to
update neovim with `pip install --upgrade neovim`. This is the start of a
disaster which wastes me several hours.

<!--more-->

After the upgrade, when I open Neovim, it complains:

> UltiSnips requires py >= 2.7 or py3

I add the following setting in my `init.vim`:

```
let g:python3_host_prog='/home/jdhao/tools/anaconda3/bin/python'
```

The above warning message disappears. Each time I open nvim, I see the error
message:

> [deoplete] deoplete failed to load. Try the :UpdateRemotePlugins command and
> restart Neovim. See also :checkhealth.

I follow the guide and use `:UpdateRemotePlugins` command. Then I got another
error message:

```
function remote#host#UpdateRemotePlugins[6]..<SNR>36_RegistrationCommands[15]..remote#host#Require[10]..provider#pythonx#Require[13]..provider#Poll, line 4
Vim(if):ch 3 was closed by the client
function remote#host#UpdateRemotePlugins[6]..<SNR>36_RegistrationCommands[15]..remote#host#Require[10]..provider#pythonx#Require[13]..provider#Poll, line 14
Failed to load python3 host. You can try to see what happened by starting nvim with $NVIM_PYTHON_LOG_FILE set and opening the generated log file. Also, the host stderr is available in messages.
remote/host: generated rplugin manifest: /home/jdhao/.local/share/nvim/rplugin.vim
```

I may also get the error message:

```
function remote#host#UpdateRemotePlugins[6]..<SNR>36_RegistrationCommands[15]..remote#host#Require[10]..provider#pythonx#Require[13]..provider#Poll, line 4
Vim(if):ch 3 was closed by the client
Traceback (most recent call last):
  File "<string>", line 1, in <module>
ModuleNotFoundError: No module named 'neovim'
function remote#host#UpdateRemotePlugins[6]..<SNR>36_RegistrationCommands[15]..remote#host#Require[10]..provider#pythonx#Require[13]..provider#Poll, line 14
Failed to load python3 host. You can try to see what happened by starting nvim with $NVIM_PYTHON_LOG_FILE set and opening the generated log file. Also, the host stderr is available in messages.
remote/host: generated rplugin manifest: /home/jdhao/.local/share/nvim/rplugin.vim
```

In fact, `neovim` is already installed and `conda list|grep neovim` shows:

> neovim                    0.3.1                     <pip>


I tried to unistall neovim and reinstall:

```
pip uninstall neovim
pip install neovim
# or pip install --no-cache-dir neovim
```

and the error was not gone. Installing the neovim package under user directory
also does not work:

```bash
# does not work
pip install --user neovim
```

Why is neovim package not found but pip reports that neovim is installed?  When
I go to `$ANACONDA_ROOT/Lib/site-packages`, `ls neovim*` only shows the neovim
egg/wheel info directory. There is acutually no package installed. That is the
reason why neovim pacakge is not found.

Finally, I decided to do a complete uninstall and reinstall neovim. By
*complete uninstall*, I mean to remove all dependency packages installed along
with neovim. These dependency packages are:

```
pynvim
msgpack
greenlet
```

After uninstalling these packages and reinstall neovim with `pip install
--no-cache-dir neovim`, the error finally disappeared:

```
pip uninstall -y neovim pynvim msgpack greenlet
pip install neovim
```

Now, `ls|grep neovim` command in Anaconda Python site package directory shows:

> 205:neovim/
> 206:neovim-0.3.1.dist-info/

So the neovim Python client is successfully installed. Now open neovim and use
issue `UpdateRemotePlugins` command, you will see:

```
remote/host: python3 host registered plugins ['deoplete']
remote/host: generated rplugin manifest: /home/jdhao/.local/share/nvim/rplugin.vim
```

Restart Neovim and the error message will disappear. You will be able to use
autocompletion again.

# References
+ https://github.com/Shougo/deoplete.nvim/issues/550
+ https://github.com/neovim/neovim/issues/8085
+ https://github.com/neovim/pynvim/issues/380
