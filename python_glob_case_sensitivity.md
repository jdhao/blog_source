---
title: "Unintuitive Behaviour of Case Sensitivity in Python glob"
date: 2019-06-24 17:19:33+0800
tags: []
categories: [Python]
---

Recently, I was bitten by the unintuitive behaviour of `glob.glob()`.
I think it would be beneficial to write down what I have found.

<!--more-->

A little background. I wanted to find all the images under directory `test_img` with extensions `.jpg` or `*.JPG` on my Windows 10 machine.
My initial code was like:

```python
import glob

ext = '.jpg'
im_paths1 = glob.glob('test_img/' + '*' + ext)
im_paths2 = glob.glob('test_img/' + '*' + ext.upper())
```

I expect that `im_paths1` and `im_paths2` contain the paths of all the images ending in `.jpg` and `.JPG` respectively.
But the truth is that `im_paths1` and `im_paths2` are exactly the same:
all images whose names end with either `.jpg` or `.JPG` have been matched, i.e.,
<font color="red">`glob.glob()` is case insensitive on Windows!</font>

I run the same code on Linux and find that `glob.glob()` is case sensitive.

This inconsistent behaviour on different platforms drives me to read the [source code of `glob` module](https://github.com/python/cpython/blob/master/Lib/glob.py).
It seems that the culprit is [`fnmatch.filter()`](https://docs.python.org/3/library/fnmatch.html#fnmatch.filter),
which is used by `glob` to get the matching file paths (relevant code is [here](https://github.com/python/cpython/blob/184f3d4f39056f6fe450d007d3b9b61d811a2a4d/Lib/glob.py#L83)).
`fnmatch.filter()` uses `os.path.normcase()` for the pattern and filenames in non-POSIX systems (relevant code [here](https://github.com/python/cpython/blob/184f3d4f39056f6fe450d007d3b9b61d811a2a4d/Lib/fnmatch.py#L51)).
That is why `glob.glob()` can not distinguish between lower and upper case files on the Windows platform.

This behaviour is a **bad design** in my opinion, which should be notified to the users.

To keep the behaviour of `glob.glob()` consistent across different systems,
I write the following method to find files in a case sensitive manner on Windows:

<details>
<summary><font color="red">Click to check the code.</font></summary>

```python
def find_files(directory, pat):
    """
    Find files in a case sensitive way on Windows.

    Parameters
    ----------
    directory: str
        The directory where you want to find files, can be relative or
        absolute path.
    pat: str
        The pattern of file names you want find, for example,`*.jpg` or
        `*.JPG`.

    Returns
    -------
    A list of file paths matching the given pattern. Empty if no files under
        the directory matches the pattern.
    """
    path_pattern = os.path.join(directory, pat)
    pths = glob.glob(path_pattern)

    match = re.compile(fnmatch.translate(path_pattern)).match
    valid_pths = [pth for pth in pths if match(pth)]

    return valid_pths


print(find_files('test_img', '*.jpg'))
```
</details>

# References

+ Ignore case in glob() on Linux: https://stackoverflow.com/q/8151300/6064933
+ https://bugs.python.org/issue26655
+ [Finding files case insensitively](https://gist.github.com/techtonik/5694830)
