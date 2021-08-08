---
title: "An Introduction to Listary: A Blazing Fast Search Tool"
date: 2017-03-01 20:58:00 +0800
tags: [search, Listary]
categories: [tools]
---

I came across this amazing tool called [Listary](http://www.listary.com/)
yesterday and found that it is just an amazing piece of software that will
change my experience on Windows fundamentally!

<!--more-->

Listary is a tool which facilitates tasks such as searching files and launching
applications on your system and operating on them and more... It is definitely
better than [Everything](https://www.voidtools.com/), another tools which is
also used to search files. In my opinion, Listary is definitely the best among
its kind.

# Search files and applications

After installing and running Listary, you are ready to search literally
**everything** in your computer and beyond. Listary has two working modes. The
first one is called "File Manager Mode" and the second is called "Launcher
Mode". The two modes work differently.

## File manager mode

The first mode takes effect once you open the File Explorer and it is mainly
used to search files and folders. If you want to search a file under a
particular directory, just type part of the name of that file. Listary will
automatically list files matching the given name under that directory using
regex. For example, I have a file named `css_intro.html` in
`F:\source_code\webpage`. If I want to find that file, I just need to open the
File Explorer and change to `F:/` disk. Then I type `css html`, the Listary
search bar appears at the lower right of the File Explorer window and the file
with that name is shown at the top of the result list.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-6-6/39363340.jpg"
       title="Search file in file manager mode"
       style="float: middle;">

You can navigate up and down the list by pressing <kbd>CTRL</kbd>+<kbd>P</kbd>
or <kbd>CTRL</kbd>+<kbd>N</kbd>. After a file or folder is selected, press
<kbd>CTRL</kbd>+<kbd>O</kbd> to take actions on it.

## Launcher mode

The second mode is the full-featured mode where you can launch an application,
open a file, search the web using a specific engine and more... This mode is
activated by pressing the <kbd>CTRL</kbd> key twice or press
<kbd>Win</kbd>+<kbd>G</kbd>. Easy and convenient! After you activate this mode,
a search bar will appear in the middle your screen and be ready to accept
input. You can use <kbd>Esc</kbd> to dismiss it.

### Launch an application

Launch an application by typing keywords whether or not it is on your system
`PATH`. For example, if I want to launch the [Foxmail
application](https://en.wikipedia.org/wiki/Foxmail) on my computer, I just need
to type `foxmail` in the search bar. The Foxmail application appears at the top
of returned results. Then I can launch Foxmail by simply pressing
<kbd>Enter</kbd>.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-6-6/11144642.gif"
       title="Launch an application in launcher mode"
       style="float: middle;">

### Search a file and operate on it

This works similarly to search a file in "File manager mode". After the file is
located, you can take action on it by using the shortcut
<kbd>Ctrl</kbd>+<kbd>O</kbd> which will bring up an action menu.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-16/96898592.jpg"
         title="Take action on the found file"
         style="float: middle;">


# Search in the web or in the APP

Listary has built-in command for searching something through the search engine
such as Google, Bing. It is located in `Keywords -> Web` panel (see image
below),

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-16/64640290.jpg"
         title="Keywords list"
         style="float: middle;">

For example, if you want to search "cat" using Google, just type `gg cat` in
the Listary search bar.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-16/66293643.jpg"
         title="Search cat in Google"
         style="float: middle;">

Adding custom search website keywords is also easy. See below for an example,

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-16/42110620.jpg"
         title="Setting keyword for custom search website"
         style="float: middle;">

Or you can search a word using a dictionary in your PC. Here, we use the famous
open source software
[GoldenDict](https://github.com/goldendict/goldendict/wiki/Early-Access-Builds-for-Windows)
as an example, the settings are shown below:

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-6-5/49246613.jpg"
       title="Search word using GoldenDict"
       style="float: middle;">

# Filtering the result by path and file type

You can enable advanced search features by indicating file path and file type.
In order to indicate a file path, simply append a trailing ```\``` to a
character string.

There are six default file type filters (a string followed by a colon):

-  folder: Folder
-  file: File
-  doc: Document
-  pic: Picture
-  audio: Audio
-  video: Video

You can combine file path and file type filtering for quicker location of your
files. For example, in order to find images with *cat* in their names in the `
f:\ ` disk, simply use ` f:\ pic: cat ` as the search query. The result shows
up instantly!

<img src="https://blog-resource-1257868508.file.myqcloud.com/listary_advanced_search.png"
         title="Advanced search using filter and path"
         style="float: middle;">

# Create a project

If you frequently open up a specific folder and do your work under that folder.
You may create a _project_ for it. Go to `Options --> Projects` and click the
plus sign in lower left (see image below) and set up the project folder and
keyword for open the project root directory.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-16/86216040.jpg"
         title="Set up project"
         style="float: middle;">

After the setting, we can search files or directories under that folder easily:
just type the project keyword and give any file or folder names you want to
open.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-1-16/89514323.jpg"
         title="Search a directory under project root"
         style="float: middle;">

# More questions?

If you encounter problems when you are using Listary, you can go to the
[Listary discussion forum](http://discussion.listary.com/) for help. The
developer of Listary are willing to answer usage-related questions about
Listary.

# Support copyright

Listary is free to evaluate indefinitely. If you can afford it, I think it is
better to buy a licence to support the development of this excellent software.

---

# References

+ [The Listary official documentation](http://www.listary.com/docs)
+ [Awesome tools list for Windows](http://www.jeffjade.com/2015/10/19/2015-10-18-Efficacious-win-software/) (it is written in Chinese.)
