---
title: "Missing Level 1 Header in TOC in Latest Hugo"
date: 2020-01-19T21:56:34+08:00
tags: [Hugo, Markdown]
categories: [Blog]
---

After updating Hugo to the latest version (v0.62), apart from the issue of
[missing HTML code](https://jdhao.github.io/2019/12/29/hugo_html_not_shown/), I
have also observed that level 1 header written in Markdown is not rendered in
the generated <abbr title="Table of Content">TOC</abbr>.

<!--more-->

After searching for a little bit, it turns out it has also something to do with
Hugo's new `goldmark` Markdown renderer.
According to Hugo's [documentation](https://gohugo.io/getting-started/configuration-markup/#table-of-contents),
it now renders Markdown headings starting from level 2 and ending in level 3.
That is why level 1 headings are now not rendered.

To fix this issue, you need to add the following settings to your `config.toml`:

```toml
[markup]
  [markup.tableOfContents]
    endLevel = 4
    startLevel = 1
```

# References #

+ https://github.com/gohugoio/hugo/issues/1778
+ https://github.com/Track3/hermit/issues/112
+ https://github.com/okkur/syna/issues/682
