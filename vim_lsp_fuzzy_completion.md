---
title: "Set up Fuzzy Completion for Vim-lsp"
date: 2020-11-16T22:37:02+08:00
draft: false
tags: [Jedi, pyls, LSP]
categories: [Nvim]
---

<font color="red">Update 2021-07-15: Note that pyls has been deprecated. There
is new fork of it called [pylsp](https://github.com/python-lsp/python-lsp-server), which is maintained by the
community.</font>

In my [older post](https://jdhao.github.io/2020/11/04/replace_deoplete_jedi_for_LSP/), I have shared how to set up
auto-completion for vim-lsp with the help of deoplete. One annoyance is that I
can not use fuzzy matching for auto-completion: the completion items in the
pop-up completion menu seems to be selected base on prefix matching.

<!--more-->

After consulting the deoplete documentation, I add the following config:

```vim
call deoplete#custom#source('_', 'matchers', ['matcher_full_fuzzy'])
call deoplete#custom#option({'camel_case': v:true,})
```

Interestingly, the above config works for [ccls](https://github.com/MaskRay/ccls),
i.e., fuzzy matching now works for cpp files.
For example, `cv::imread("test.jpg", cv::imcolor)` can show `cv::IMREAD_COLOR`
as a completion candidate.

However, for [pyls](https://github.com/palantir/python-language-server), the above config does not work. Under the hood, pyls uses [jedi](https://github.com/davidhalter/jedi) for code completion feature (see [here](https://github.com/palantir/python-language-server/blob/develop/pyls/plugins/jedi_completion.py#L60)).

In newer version of Jedi, it actually supports fuzzy code completion in [`complete()`](https://jedi.readthedocs.io/en/latest/docs/api.html#jedi.Script.complete) method. Pyls [turns off this feature by default](https://github.com/palantir/python-language-server/blob/develop/pyls/plugins/jedi_completion.py#L59). So to enable fuzzy matching, we need to config it manually. Based on example config [here](https://github.com/palantir/python-language-server/blob/develop/vscode-client/package.json#L68), I have figured out the correct config for [vim-lsp](https://github.com/prabirshrestha/vim-lsp):

```vim
if executable('pyls')
 " pip install python-language-server
  augroup pyls_setup
    autocmd!
    autocmd User lsp_setup call lsp#register_server({
          \ 'name': 'pyls',
          \ 'cmd': {server_info->['pyls']},
          \ 'allowlist': ['python'],
          \ 'workspace_config': {
          \    'pyls':
          \        {'configurationSources': ['flake8'],
          \         'plugins': {'flake8': {'enabled': v:true},
          \                     'pyflakes': {'enabled': v:false},
          \                     'pycodestyle': {'enabled': v:false},
          \                     'jedi_completion': {'fuzzy': v:true},
          \                    }
          \        }
          \ }})
  augroup END
endif
```

Note that you also need to install the latest version of jedi[^1]:

```bash
pip install -U jedi
```

Now everything should work as expected.

# References

+ https://github.com/lighttiger2505/deoplete-vim-lsp/issues/23

[^1]: The jedi version tested is 0.17.2
