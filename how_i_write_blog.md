---
title: "How I Manage My Personal Blog"
date: 2021-11-28T18:37:48+08:00
draft: false
tags: [Hugo]
categories: [Blog]
---

In this post-web2.0 age, people seem to blog less and less, and blogs seem to [be dying slowly](https://www.google.com/search?q=blogs+are+dying).
People are separated in different Apps where the content is not searchable by search engines.

However, I still prefer to write using a blog. In this post, I want to share how I manage my blog.

<!--more-->

# Why I blog?

I run this blog site because I enjoy sharing knowledges or techniques I have learned.
A lot of these knowledges are from blog posts of others, GitHub or Stack Overflow.
Since I get these knowledges for free, I have a strong motivation to make what I have learned free for others.
In a bigger sense, free exchange and access of ideas and knowledge makes the world a better place.
This is also what [FOSS](https://en.wikipedia.org/wiki/Free_and_open-source_software) is about.

Although some of the blog posts may only be helpful to myself, but who knows,
I hope they will be helpful to someone else in some day.

# What I write?

I usually share what I have learned during my work or my spare time.
I write anything I am interested.

Occasionally, I also write about what I am reading, what I thought, etc.

# When I write

I often save some notes about a topic or an idea in my free time.
Later, when I have time, I will expand the notes to a full blog post.
This way, I do not need to worry what to write. I always have something to share when I want to.

# Tools I use for writing

All my blog posts are written in Markdown format with the help of the greatest editor [neovim](https://neovim.io/)[^1].
I use neovim because it is a highly configurable editor that no other editors can compete with.

I use [snippet plugins](https://jdhao.github.io/2019/01/15/markdown_edit_preview_nvim/#markdown-editing) to make writing posts quicker.
For previewing, I use [markdown-preview.nvim](https://github.com/iamcco/markdown-preview.nvim), which is the best previewer for Neovim.
I have shared how I write and preview Markdown efficiently in [this post](https://jdhao.github.io/2019/01/15/markdown_edit_preview_nvim/).

I rarely insert images into my blog post since image links tend to break easily.
So I try to explain things without using images as much as I can.
If I need to use images in a post, I store the image in a cloud service and use the generated image links.

# Static blog generator

I generate my blog using a static blog generator. Back in 2017 when I started this blog, I use [Hexo](https://github.com/hexojs/hexo).
In Oct. 2018, I switched to [Hugo](https://github.com/gohugoio/hugo) for its high performance.

For comment system, I used Disqus for a long time. Recently, I have switched to use [utterances](https://utteranc.es/),
which is a lightweight comment system based on GitHub issues.
You get all the power of GitHub issues for your blog comment.

# Blog site hosting

For blog hosting, I am using the good-old [GitHub pages service](https://pages.github.com/).
I have not purchased a personal domain for my blog because I think that is irrelevant.
It is the content of the blog that matters most.
People often get a fancy domain for their blog, and they get tired and stop writing anything.

# Post backup

In case that the post sources get lost, I have been backing up my posts periodically.
Since all the blog posts are written in Markdown, I created a private GitHub repo to store the Markdown source files.
I have created a backup script `post_backup.sh` under the Hugo blog site root to ease the operation:

```bash
#!/bin/bash
msg="backup `date`"
if [ $# -eq 1 ]
  then msg="$1"
fi

echo -e "\033[0;32mBackup posts to GitHub...\033[0m"

cd content/post
# here we start to add and commit the blog to github
git add .
git commit -m "$msg"

# push source to github
git push

echo -e "\033[0;32mBackup done...\033[0m"

# come back to blog root
cd ../..
```

Then I can run `./post_backup.sh` to back up all my post sources.

[^1]: My nvim config is stored in [this repo](https://github.com/jdhao/nvim-config).
If you use Hugo, you can set up the [default content editor](https://gohugo.io/getting-started/configuration/#newcontenteditor) when running `hugo new post content/xxx.md`.
