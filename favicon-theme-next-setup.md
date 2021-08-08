---
title: Customize Favicon for Hexo Blog in Theme NexT
date: 2018-02-08 19:21:00 +0800
tags: [Hexo, NexT]
categories: [Blog]
---

[Favicons](https://en.wikipedia.org/wiki/Favicon) are used widely in websites.
Since you come to this post purposefully, I assume that you have a basic
understanding of what favicon is. With [Hexo](https://github.com/hexojs/hexo),
we can easily customize the favicon of our blog site. In this post, I will
describe how to set up a custom favicon in [theme
NexT](https://github.com/theme-next/hexo-theme-next).

<!--more-->

# Convert your favorite image into favicon

The first step is to convert your favorite image into favicon. You can use
[favicon generator](https://www.favicon-generator.org/)，load your favorite
image into this website, and click the button `Create Favicon` to generate
favicons for your blog. Then download the generated favicons (see image below).

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-2-8/17745879.jpg"
         title="Download generated favicon"
         style="float: middle;">


## Create a favicon with only text

If you want to create a favicon with only text. You should use
[favicon.io](https://favicon.io/). It is simple to use. You can tweak the font
type, font size, text box shape, color etc. Then you can download the 1024x1024
PNG image from the website and use favicon generator to generate various
favicons.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-5/21505223.jpg"
     title="Generate text favicon"
     style="float: middle;">

# Set up in theme NexT

After you have generated the favicons, extract the zipped files, you will see a
lot of files.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-2-8/64877129.jpg"
         title="Favicon files"
         style="float: middle;">

Go the blog root, under the `source` folder, create a folder named `images`.
Copy the extracted files to this folder. Then edit the theme NexT configuration
file (i.e., `theme/next-new/_config.yml`). Find the part about favicon and use
the following settings,

```yaml
favicon:
  small: images/favicon-16x16.png
  medium: images/favicon-32x32.png
  apple_touch_icon: images/apple-icon-180x180.png
  safari_pinned_tab:
  android_manifest: images/manifest.json
  ms_browserconfig: images/browserconfig.xml
```

Then deploy your blog to GitHub. You will see that your blog now has a custom
favicon (see image below, favicon in the circle)~

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-2-8/64469647.jpg"
         title="Blog custom favicon"
         style="float: middle;">

