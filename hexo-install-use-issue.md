---
title: How to Install Hexo and Theme NexT from Scratch on Windows
date: 2017-02-26 15:52:00 +0800
tags: [Markdown, NexT, Hexo, LaTeX, Pandoc]
categories: [Blog]
---

In this post, I will describe the complete step to install both
[Hexo](https://hexo.io/) and theme
[NexT](https://github.com/theme-next/hexo-theme-next) on Windows.

<!-- more -->

# Install Hexo and deploy your post

First, we need to install the Hexo framework. The main steps are as follows:


## Install Node.js and Cmder

We need to install [Node.js](https://nodejs.org/en/download/) and [git for
Windows](https://git-scm.com/download/win). You can skip installing git for
Windows if you choose to install [Cmder](http://cmder.net/)(highly recommended
on Windows). Choose the Full package if you decide to install Cmder (only the
full package contains git for Windows).

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-4-10/48824353.jpg"
     title="Choose Full version"
     style="float: middle;">

## Set up the blog frame

Install the Hexo command line tool,

```bash
npm install -g hexo-cli
```

Then, we need the build the frame of our blog. Go to a directory where you want
to set up your blog, for example, `d:/`. Use the following command:

```bash
hexo init hexo-blog
cd hexo-blog
npm install
```

The above command will create a directory named `hexo-blog` and set up the
frame of your blog.

## Replace the default renderer

This step is optional. But it is highly recommended.

Change the default renderer shipped with Hexo. The renderer is used to
translate your markdown file to HTML files. But it has weak functionality and
does not support LaTeX style equations. We need to remove it first:

```bash
hexo uninstall hexo-renderer-marked --save
```

We can use the Pandoc renderer which is more powerful. Make sure that you have
installed [Pandoc](https://pandoc.org/) , and then use the following command to
install the Panodc renderer,

```bash
hexo install hexo-renderer-pandoc --save
```

## Set up the GitHub pages

Set up your [GitHub pages](https://pages.github.com/) and choose to set up a
`user site` (see image below). The name of the newly created repo must have the
format `username.github.io`, where `username` is your GitHub user name. For me,
the user name is `jdhao`.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-4-10/90061901.jpg"
     title="Set up user site"
     style="float: middle;">


## Create website pages

This is discussed thoroughly in [Official
documentation](https://hexo.io/docs/configuration.html). We are going to create
4 more pages for our blog site and they are `tags`, `categories`, `archives`
and `about` page. Use the following command to create the 4 pages:

```bash
hexo new page "tags"
hexo new page "categories"
hexo new page "archives"
hexo new page "about"
```

## Write posts and preview your site

You can write your personal introduction in file `source\about\index.md`. In
the command line, you can use

```
`hexo new post "some_post"`
```

to initialize a new template post named `some-post.md`, which is located in
directory `source\_posts` under the Hexo root directory.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-4-10/223624.jpg"
     title="Directory for storing your posts"
     style="float: middle;">

The template post will only include a yaml format header, which is used to
configure the post meta info. A sample header is shown below:

```yaml
---
title: How to Install Hexo and Theme NexT from Scratch on Windows
date: 2017-02-26 15:52:00 +0800
lastmod: 2018-02-08 15:42:00 +0800
tags: [Markdown, NexT, Hexo, LaTeX]
categories: [technique]
---
```

Then you can write your post in this file using your favorite editor. After
finishing your post, you can use `hexo generate` to generate your blog website
locally.

To preview your blog website, you can use `hexo server` to set up a local http
server. Then you can preview your website in the browser by using the local
address `localhost:4000`.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-4-10/63675385.jpg"
     title="Blog preview"
     style="float: middle;">

## Deploy your blog

In order to deploy your blog to GitHub, you need to install the deployer,

```bash
npm install hexo-deployer-git --save
```

Then we need to configure our GitHub user name and email. My configuration is
as follows:

```bash
git config --global user.email "jdhao@hotmail.com"
git config --global user.name "jdhao"
```

Change the command above to suit your need. In the file
`hexo-blog\_config.yml`, we need to configure the address of your GitHub pages
repo.

```yaml
deploy:
  type: git
  repo: https://github.com/jdhao/jdhao.github.io
  branch: master
```

You can use `hexo d -g` to generate and deploy your blog to GitHub. You will be
prompted to input your email and password to confirm your identity. After that,
your site will be deployed to GitHub. Anyone will be able to visit your blog.

### Generate your ssh key

You can generate your `ssh key` to avoid inputting email and password every
time. First, use the following command to generate your key,

```bash
ssh-keygen -t rsa -C "jdhao@hotmail.com" # please use your own email address
```

The above command will generate two file: one (file name is `id_rsa`) for your
private key and the other (file name is `id_rsa.pub`) for your public key. The
files are located in `C:/Users/jdhao/.ssh` (look for something similar on your
system). Open the file `id_rsa.pub` and [add your public key to
GitHub](https://help.github.com/articles/connecting-to-github-with-ssh/).

After adding the public key, we need to confirm it is successfully configured:

```bash
ssh -T git@github.com
```

You will see something like:

>The authenticity of host 'github.com (IP ADDRESS)' can't be established.
>RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
>Are you sure you want to continue connecting (yes/no)?

Input `yes` and press <kbd>Enter</kbd>, and you will see the following output:

>Hi jdhao! You've successfully authenticated, but GitHub does not
>provide shell access.

This message will confirm that your setting is correct. You do not need to
input your email and password from now on.

Now, it is all set. You can start writing your posts.

# Install NexT theme

Go to the blog root `D:/hexo-blog` and execute the following command to install
the latest NexT theme,

```bash
git clone https://github.com/theme-next/hexo-theme-next themes/next-new
```

NexT theme has its own configuration files, it is located in
`themes/next-new/_config.yml`. You can customize as you wish. There are several
plugins which is useful.

## Estimate post word count and read time.

The plugin
[hexo-symbols-count-time](https://github.com/theme-next/hexo-symbols-count-time)
can be used to calculate the post word number and estimate your read time.
Install this plugin

```bash
npm install hexo-symbols-count-time --save
```

In the Hexo configuration file, use the following setting:

```yaml
symbols_count_time:
  symbols: true
  time: true
  total_symbols: false
  total_time: false
```

## Local search

Install the plugin
[hexo-generator-searchdb](https://github.com/theme-next/hexo-generator-searchdb)
to add the local search feature:

```bash
npm install hexo-generator-searchdb --save
```

Edit the Hexo `_config.yml` file and add the following settings:

```yaml
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```

Also, you need to edit the theme Next configuration
`themes/next-new/_config.yml`, and change the setting for local search,

```yaml
local_search:
  enable: true
```

## Compress your blog

Install the plugin
[hexo-filter-optimize](https://github.com/theme-next/hexo-filter-optimize) to
compress your blog,

```bash
npm install hexo-filter-optimize --save
```

In the Hexo `_config.yml` file, add the following setting,

```yaml
filter_optimize:
  enable: true
  # remove static resource query string

  #   - like `?v=1.0.0`

  remove_query_string: true
  # remove the surrounding comments in each of the bundled files

  remove_comments: false
  css:
    enable: true
    # bundle loaded css file into the one

    bundle: true
    # use a script block to load css elements dynamically

    delivery: true
    # make specific css content inline into the html page

    #   - only support the full path

    #   - default is ['css/main.css']

    inlines:
    excludes:
  js:
    # bundle loaded js file into the one

    bundle: true
    excludes:
  # set the priority of this plugin,

  # lower means it will be executed first, default is 10

  priority: 12
```

After using this plugin, you may find some of the icons are not displayed
correctly. This issue has been raised
[here](https://github.com/theme-next/hexo-filter-optimize/issues/4). The
solution is to edit the theme NexT configuration file and use custom CDN
address for fontAwesome,

```yaml
fontawesome: https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css
```

## Show related posts

It would be nice to show related posts in the post page. Just install the
[hexo-related-popular-posts](https://github.com/tea3/hexo-related-popular-posts),

```bash
npm install hexo-related-popular-posts --save
```

Use the following setting in the theme NexT configuration file,

```yaml
related_posts:
  enable: true
  title: Related posts
  display_in_home: false
  params:
    maxCount: 5
    #PPMixingRate: 0.0
    #isDate: false
    #isImage: false
    #isExcerpt: false
```

Now, you have have finished all the configurations. Happy writing!
