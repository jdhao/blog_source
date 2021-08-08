---
title: "Better Git log"
date: 2021-01-25T23:04:12+08:00
draft: false
tags: [Git]
categories: [Note]
---

Some of the settings to make `git log` better.

<!--more-->

# Decorate commit

The `--decorate` option will show references to your commit. For example, where
your `HEAD` is, where your master points to etc. To use `--decorate` by default:

```
git config --global log.decorate true
```

# Show short commit hash

By default, git shows long commit hash for each commit. To show a short commit
hash, we can use the `--abbrev-commit` option. To use `--abbrev-commit` by
default:

```
git config --global log.abbrevCommit true
```

# Show commit graph

To show a graphical representation of commit, which is handy when we have
merged commit or multiple branches, we can use `--graph` option:

```
git log --graph
```

# Use color for log output

Use color to differentiate different part of log message:

```
git config format.pretty "%C(magenta)%h%Creset -%C(red)%d%Creset %s %C(green)(%cr) %C(cyan)<%an>%Creset"
```

After this setup, git-log output will be like the following:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20210125233412.png" width="600">
</p>


# Refs

+ https://devhints.io/git-log-format
+ [color for git pretty format](https://stackoverflow.com/q/15458237/6064933)
+ [make --abbrev-commit default for git log](https://stackoverflow.com/q/2500586/6064933)
+ [make --decoreate default for git log](https://stackoverflow.com/q/21607305/6064933)
+ [A better git log](https://coderwall.com/p/euwpig/a-better-git-log)
