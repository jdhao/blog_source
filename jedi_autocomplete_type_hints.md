---
title: "Why Doesn't Jedi Autocompletion Work for Some Methods"
date: 2019-05-13 20:36:10+0800
tags: [Nvim, Jedi, Python]
categories: [Note]
---

Currently I am writing all my Python code using Neovim and [a couple of
plugins](https://jdhao.github.io/2018/12/24/centos_nvim_install_use_guide_en/#plugin-install-and-settings)
to provide auto-completion, linting, etc.

<!--more-->

For code auto-completion, I use [deoplete](https://github.com/Shougo/deoplete.nvim)
as my completion engine and [deoplete-jedi](https://github.com/deoplete-plugins/deoplete-jedi)
as the completion source for Python. They work great most of the time. However,
occasionally I can not get any auto-completion for some object instances. For
example, auto-completion for object instances returned by the [`Image.open()`](https://pillow.readthedocs.io/en/stable/reference/Image.html#PIL.Image.open)
method in the package [Pillow](https://github.com/python-pillow/Pillow) does
not work. I decide to take a look.

At first, I think it is an issue with deoplete or deoplete-jedi. However, I
find that auto-completion for Image instances returned by other methods, e.g.,
[`Image.new()`](https://pillow.readthedocs.io/en/stable/reference/Image.html#PIL.Image.new),
works as expected. If this is an issue with deoplete or deoplete-jedi, then
auto-completion should not work anymore. Maybe it is due to the static type
checker [Jedi](https://github.com/davidhalter/jedi) that deoplete-jedi relies
on?

To find out the real reason, I use the following script to check if Jedi can
provide completion for Image instances returned by `Image.open()` and
`Image.new()` method[^1]:

```
import jedi

source1 = '''
from PIL import Image
im = Image.new('test.jpg', (128, 128))
im.
'''

script1 = jedi.Script(source1, 4, len('im.'), 'example1.py')
print(script1.completions())

source2 = '''
from PIL import Image
im = Image.open('test.jpg')
im.
'''
script2 = jedi.Script(source2, 4, len('im.'), 'example2.py')
print(script2.completions())
```

As expected, the first `print()` shows a list of completion results, while the
second `print()` shows an empty list.

So this is actually an issue with Jedi. I filed [an
issue](https://github.com/davidhalter/jedi/issues/1322) on the Jedi GitHub
repo. After a while, I got a reply from the maintainer of this project. The
root cause is that Jedi can not infer the type returned by `Image.open()`
method since it is too dynamic to infer. The author suggested that I may use
[type
hints](https://stackoverflow.com/questions/32557920/what-are-type-hints-in-python-3-5)
to help Jedi infer the types of the return objects. I tried two ways based on
the [type hinting
documentation](https://github.com/davidhalter/jedi/blob/master/docs/docs/features.rst#type-hinting)
of Jedi.

The first way is to use function annotations. I find the source file of
`Image.open()` on my system and change the its definition from

```
def open(fp, mode="r") :
```

to

```
def open(fp, mode="r") -> Image:
```

Now the auto-completion works for instance returned by `Image.open()` method.
While this method works, it is cumbersome to use since it requires changing the
source files of various packages.

The second way is to use type hints in inline comments. But the auto-completion
does not work anymore. My test script is:

```
import jedi

source = '''
from PIL import Image
im = Image.open('test.png')  # type: Image
im.
'''

script = jedi.Script(source, 4, len('im.'), 'example.py')
print(script.completions())
```

The print function shows an empty list.

So for now, you may stick to function annotations if you really want type hints
to work for Jedi.

# References

+ [Type hints cheat sheet](https://mypy.readthedocs.io/en/latest/cheat_sheet_py3.html)
+ [Type hinting in Pycharm 5](https://blog.jetbrains.com/pycharm/2015/11/python-3-5-type-hinting-in-pycharm-5/)

[^1]: The script is adapted from the tutorial code of Jedi from [here](https://jedi.readthedocs.io/en/latest/docs/api.html#api-documentation)
