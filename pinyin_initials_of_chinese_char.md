---
title: "Get Pinyin Initials of Chinese characters"
date: 2020-12-04T23:08:18+08:00
draft: false
tags: []
categories: [Note]
---

To get a reasonably large collection of traditional and simplified Chinese
characters in use toady, we may use [zhon](https://zhon.readthedocs.io/en/latest/):

<!--more-->

```
pip install zhon
```

To get pinyin of a Chinese character, we use [python-pinyin](https://github.com/mozillazg/python-pinyin):

```
pip install pypinyin
```

Here a script to get pinyin initials for valid Chinese character without tones:

```python
from zhon import cedict
from pypinyin import pinyin, Style
import yaml


def main():
    all_chars = set(cedict.all)

    ch_initials = {}
    for c in all_chars:
        ch_pinyin = pinyin(c, style=Style.NORMAL, errors='ignore')
        # if no pinyin for this char exists
        if not ch_pinyin:
            continue

        py_init = ch_pinyin[0][0][0]
        ch_initials[c] = py_init

    fname = 'zh_char_initial.yaml'
    with open(fname, 'w') as f:
        yaml.dump(ch_initials, f)


if __name__ == "__main__":
    main()
```

# Ref

+ https://pypinyin.readthedocs.io/zh_CN/master/api.html
