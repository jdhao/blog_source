---
title: "How Is Newline Handled in Python and Various Editors?"
date: 2018-12-08T22:30:50+0800
tags: [Nvim, Windows, Mac, Sublime-Text]
categories: [technique]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181208223253.png" width="800">
</p>

Every programmer knows newline character, but may be not so familiar. In this
post, I want to share what I have learned about newline handling in various
cases.

<!--more-->

# Newline characters on different platforms

Due to historical reasons, different platforms use different characters to
signify a new line. On Windows, `<CR><LF>` (byte code `0x0D0x0A`) is used to
represent newline. On Linux, `<LF>`(byte code `0x0A`) is used to represent
newline. On older Mac[^1], `<CR>`(byte code `0x0D`) is used.

`<CR>` and `<LF>` date back to the old time when typewriters is used for
printing texts on paper. `<CR>` represents *carriage return*, which means to
put the carriage to its left-most position. `<LF>` represents *line feed*,
which means to move the paper a little higher so that you can type on a new
line. You can see that these two actions combined will start a new line ready
for typing.

# Newline handling in Python

Python 2 and Python 3 have different way of handling newlines. In Python 2,
there is a [universal newline
mode](https://docs.python.org/3/glossary.html#term-universal-newlines), which
means that no matter what the file line ending is, it will all be translated to
`\n` when reading files with mode specifier `rU`.

In Python 3, things have changed. The old `U` mode specifier has been
deprecated in favor of a `newline` parameter in the `open()` method.  According
to the [documentation](https://docs.python.org/3/library/functions.html#open):

> newline controls how universal newlines mode works (it only applies to text mode). It can be None, '', '\n', '\r', and '\r\n'. It works as follows:
> - When reading input from the stream, if newline is None, universal newlines mode is enabled. Lines in the input can end in '\n', '\r', or '\r\n', and these are translated into '\n' before being returned to the caller. If it is '', universal newlines mode is enabled, but line endings are returned to the caller untranslated.
> - When writing output to the stream, if newline is None, any '\n' characters written are translated to the system default line separator, [`os.linesep`](https://docs.python.org/3/library/os.html#os.linesep). If newline is '' or '\n', no translation takes place.

when reading text files, `newline` is `None` by default, which means that
system-dependent newline will be quietly replaced by `\n`. If you are not aware
of this behavior, you may get into trouble. For example, when you read a file
with `\r\n` line ending and want to split the text into lines on Windows
platform, if you use the following snippet:

```python
with open("some_file.txt", "r") as f:
    text = f.read()
lines = text.split(os.linesep)
```

you will not be able to split the text into lines. That is because on Windows
platform, `os.linesep` is `\r\n`. But Python has secretly translated the `\r\n`
in the file to `\n`!

When writing files, you should also be aware that `\n` will be translated to
platform-dependent line endings.

# Newline handling in different editors

## Vim

When reading a file into the buffer, Vim will automatically detect the file
format[^2]. Then Vim will replace the platform-dependent newline characters
with a special mark to mark the end of each line. When writing the buffer
content back into the file, Vim will write the actual newline characters based
on the detected file format.

For example, if you open a file with Windows-style line ending, Vim will
replace all `<CR><LF>` with its own newline mark. If you try to search these
two characters using their byte code (`\%x0A` for `<CR>` and `\%x0D` for `<LF>`
), you will find nothing. Neither can you find `<CR>` characters using `\r` in
Windows file in Vim (suppose `fileformats` include `dos`). When searching in
Vim, `\n` is used to specify end of line, no matter what the actual newline
character is for this file. So you can search the line end with `\n`.

### How do I show the `<CR>` characters in Vim then?

You can open a Windows file in Vim and use `e ++ff=unix`[^4] to force Vim to
treat this file as a unix file. Vim will then treat the `\n` characters as
newline, thus removing it from the buffer. But the `\r` characters in the file
will now be treated as normal characters and will be shown as `^M`. You will
see it now.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181208223336.png" width="400">
</p>

You can also press `<Ctrl-V>` and then press `<Enter>` to type a carriage
return character. Then you can search this character using `\r`.

### A pitfall in searching and replacing newlines

In Vim, `\n` is used to represent newline only when you are searching it. If
you want to represent a newline in replacement, use `\r` instead[^3]. This
makes no sense, but that is how Vim works.

## Sublime Text

According to discussions
[here](https://forum.sublimetext.com/t/all-line-endings-are-converted-on-file-save/5507/20?u=flying_cat),
Sublime Text will also convert platform-dependent newline to `\n` in memory.
When writing to files, it will write newlines based on the detected file type
(Windows, Unix or Mac).

## Notepad++

[Notepad++](https://notepad-plus-plus.org/) is also a popular code editor. It
can detect your line endings, but it will not replace the newline with `\n`. To
show the newline characters in a file, go to `View --> Show Symbol` and toggle
on option `Show End of Line`, you will be able to see the newline characters.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181208223415.png" width="400">
</p>

# Conversion between different file formats?

In Vim, you can use `set ff=<Format>` to covert the current file to desired
format, where `<Format>` can be `unix`, `dos` or `mac`.

In Sublime Text, just choose the desired format from the bottom right status
bar.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181208223435.png" width="400">
</p>

In Notepad++, go to `Edit --> EOL Conversion` and choose the desired file
format.

There are also tools such as [`dos2unix` and
`unix2dos`](https://waterlan.home.xs4all.nl/dos2unix.html) which convert
between different file formats.

# References

+ [Newline handling in Python3](https://stackoverflow.com/a/53679203/6064933).
+ [Discussions of newline characters on StackOverflow](https://stackoverflow.com/q/1552749/6064933).
+ [Different parts of a typewriters explained](https://www.explainthatstuff.com/typewriter.html).
+ https://groups.google.com/d/msg/vim_use/s0HF9GJyCo8/I0VKA2Z32BIJ
+ https://stackoverflow.com/q/34939473/6064933
+ [See line breaks and carriage returns in editor](https://stackoverflow.com/a/45459733/6064933).
+ [How to get rid of ^M](https://unix.stackexchange.com/q/32001/221410).
+ [Why does Vim sometimes shows ^M](https://stackoverflow.com/q/21228946/6064933).
+ https://blogs.msdn.microsoft.com/oldnewthing/20040318-00/?p=40193

---
Title image is taken from [here](https://www.iclarified.com/48838/microsoft-releases-new-visual-studio-code-editor-for-mac-os-x-windows-linux-video).


[^1]: Newer Mac system also use the unix-style newline character, see discussion [here](https://superuser.com/q/439440/736190).
[^2]: In vim, use `:h fileformats`, `:h file-read` and `:h file-formats` for more info about how Vim detects and file format and reads files.
[^3]: In replace, `\n` means null character `\0`, which is show as `^@` in Vim. See [here](https://unix.stackexchange.com/q/247329/221410) for more discussions.
[^4]: Use `:h ++ff` to find more information about what this command means.
