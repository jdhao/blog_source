---
title: "Flake8 Config in Pyls for Code Linting."
date: 2020-11-05 00:27:36+0800
tags: [LSP, pyls]
categories: [Nvim]
---

<font color="red">Update 2021-07-15: Note that pyls has been deprecated. There
is new fork of it called [pylsp](https://github.com/python-lsp/python-lsp-server), which is maintained by the
community.</font>

In my [previous post](https://jdhao.github.io/2020/11/04/replace_deoplete_jedi_for_LSP/), I have go over the basic setup to make vim-lsp work.
However, I haven't touch on an important part of writing code: linting. It
turns out that configure it correctly is harder than I thought.

<!--more-->

I found the documentation of [pyls](https://github.com/palantir/python-language-server) to be vague and incomplete. It took me quite
a few hours in order to understand how to configure flake8 correctly for pyls.

Pyls is a combination of different packages to form a unified workable language
server. Flake8 is like a driver package for pycodestyle (former known as pep8
and got [renamed](https://github.com/PyCQA/pycodestyle/issues/466)) and
pyflakes. In simple words, it uses [pyflakes](https://github.com/PyCQA/pyflakes)
and [pycodestyle](https://github.com/PyCQA/pycodestyle) and a few other
packages to check your code for possible style issues and syntax errors.

By default, pyls disables flake8, and use pycodestyle and pyflakes for code
checking directly. I do not want to configure two packages, I just want to
configure flake8 for simplicity's sake.

From [here](https://github.com/palantir/python-language-server/pull/656) and [the actual code](https://github.com/palantir/python-language-server/blob/develop/pyls/plugins/flake8_lint.py#L16),
we can see that flake8 is indeed disabled. Also, if we use flake8, it makes
sense to disable pycodestyle and pyflakes, otherwise we will get duplicated
diagnostic messages.

```vim
if executable('pyls')
    " pip install python-language-server
    au User lsp_setup call lsp#register_server({
          \ 'name': 'pyls',
          \ 'cmd': {server_info->['pyls']},
          \ 'allowlist': ['python'],
          \ 'workspace_config': {
          \    'pyls':
          \        {'configurationSources': ['flake8'],
          \         'plugins': {'flake8': {'enabled': v:true},
          \                     'pyflakes': {'enabled': v:false},
          \                     'pycodestyle': {'enabled': v:false},
          \                    }
          \         }
          \ }})
endif
```

This is the correct config for pyls to use flake8 for linting, period.

## config location

According to [flake8 documentation](https://flake8.pycqa.org/en/3.8.4/user/configuration.html), the location of flake8
config varies based on systems, on Linux and Mac, it is `~/.config/flake8` ,
and for Windows, it is `$HOME\.flake8` (`$HOME` is like `C:\\Users\sigmavirus24`).
The content should be in INI format:

```ini
[flake8]
max-line-length = 100
max-complexity = 30
ignore =
    # missing whitespace around arithmetic operator
    E226,
    # line break before/after binary operator
    W503,
    W504,
    # expected 1 blank line, found 0
    E301,E302,
```

To suppress a single warning, it is also handy to add `# noqa: F841`-like
(change the code to the actual code you want to use) comment string to suppress
it.

# References

+ https://github.com/palantir/python-language-server/issues/190
+ https://github.com/palantir/python-language-server/pull/656
