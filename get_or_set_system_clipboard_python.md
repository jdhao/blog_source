---
title: "How to Get or Set Clipboard Text in Python"
date: 2021-02-03T23:02:12+08:00
draft: false
tags: []
categories: [Python]
---

There are several Python packages by which we can get and set the system
clipboard.

<!--more-->

# Tkinter

Use [tkinter](https://docs.python.org/3/library/tkinter.html) to get clipboard text.

```python
import tkinter as tk

root = tk.TK()
root.withdraw()  # keep the window from showing

print(root.clipboard_get())
```

# pywin32

Use [pywin32](https://pypi.org/project/pywin32/). It provides the `win32clipboard` module.

```python
import win32clipboard

# set the clipboard
win32clipboard.OpenClipboard()
win32clipboard.EmptyClipboard()
win32clipboard.SetClipboardText('just for a test')
win32clipboard.CloseClipboard()

# get the clipboard text
win32clipboard.OpenClipboard()
data = win32clipboard.GetClipboardData()
print(data)
win32clipboard.CloseClipboard()
```

# pyperclip

Use [pyperclip](https://pypi.org/project/pyperclip/).

```
import pyperclip

# set the clipboard
pyperclip.copy('some text')

# get the clipboard
pyperclip.paste()
```

# Ref

+ [How do I copy a string to the clipboard?](https://stackoverflow.com/q/579687/6064933)
+ [How do I read text from the clipboard?](https://stackoverflow.com/q/101128/6064933)
