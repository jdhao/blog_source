---
title: "Reading and Writing Text Files on Windows"
date: 2018-12-03 13:06:00 +0800
tags: [Windows, Unicode, encoding]
categories: [Python]
---

If you are using Python 3 on Windows, you may have seen a Unicode decoding
error when opening files in UTF-8 format:

> UnicodeDecodeError: 'gbk' codec can't decode byte 0xaf in position 5: illegal multibyte sequence
>

If you read the same file on Linux or macOS, you will find that this file can
be opened without any error. Why is there a difference? It has something to do
with the default encoding Python chooses to use on different platforms.

<!--more-->

# The reason and the solution

## Reading UTF-8 files

To show the default encoding used by Python on your platform, use the following
snippet:

```
import locale
locale.getpreferredencoding()
```

The method
[`locale.getpreferredencoding()`](https://docs.python.org/3/library/locale.html#locale.getpreferredencoding)
is used to get the encoding for text files on the system. On Linux, the output
is `UTF-8` and on my Windows (a Chinese simplified version of Windows 10 Pro),
the output is `cp936`, which means that `UTF-8` is used as the default encoding
on Linux and `cp936` is used as the default encoding on
Windows[^1].[`cp936`](https://en.wikipedia.org/wiki/Code_page_936_(Microsoft_Windows))
is the abbreviation for "Microsoft code page 936", which encodes all the
characters in the [GBK character
set](https://en.wikipedia.org/wiki/GBK_(character_encoding)).

The solution to this issue is quite simple. If you are sure that the file to
read is encoded in UTF-8, you can use `encoding="utf-8"` in the built-in
[`open()`](https://docs.python.org/3/library/functions.html#open) method:

```python
with open("test.txt", "r", encoding="utf-8"):
    text = f.read()
```

The Python 3 official documentation for `encoding` parameter says:

> `encoding` is the name of the encoding used to decode or encode the file. This should only be used in text mode. The default encoding is platform dependent (whatever `locale.getpreferredencoding()` returns), but any text encoding supported by Python can be used. See the `codecs` module for the list of supported encodings.

## Writing UTF-8 files

You may wonder that when you write non-ASCII characters to files on Windows,
you haven't encountered any issues related to encoding. The reason is probably
that the `gbk` codec used can encode the characters by chance. If you use
characters not in the GBK character set, you will see encoding errors. To
verify this, use the following snippet:

```python
with open("test.txt", "w") as f:
    f.write("조선말")
```

If you run the above script on Windows, you may see the following error message:

> UnicodeEncodeError                        Traceback (most recent call last)
> <ipython-input-1-933a79a7eeca> in <module>()
>       1 with open("test.txt", "w") as f:
> ----> 2     f.write("조선말")
>       3
>
> UnicodeEncodeError: 'gbk' codec can't encode character '\uc870' in position 0: illegal multibyte sequence
>

To solve this issue and save files in UTF-8 encoding, you should use
`encoding="utf-8"` when writing texts to files.

# References
+ https://stackoverflow.com/questions/36303919/python-3-0-open-default-encoding
+ https://github.com/rkern/line_profiler/issues/37#issuecomment-153257214
+ [UTF-8 mode in Python 3.7](https://vstinner.github.io/python37-new-utf8-mode.html)

[^1]: Note that in different regions, the output of `locale.getpreferredencoding()` will change.
