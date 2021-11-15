---
title: "Find Line History in Git"
date: 2020-09-14 23:57:01+0800
tags: [Git]
categories: [Git]
---

When we are working on a real project, we may want to know from which commit a particular line is introduced.

<!--more-->

We can use the following `git-log` command to find:

```bash
git log -S "search term"
```

For example, to find when the line `foo = foo + bar` was introduced, we may use the following command:

```bash
git log -S "foo = foo + bar"
```

It will find the commit that created this line.

Another similar command is `git-blame`,  for example, to find which commit introduced line 100, we can use the following command:

```bash
git blame -L 100,100
```

In fact, `git-blame` shows the most recent change to this line. So if this line has been updated since its creation, for example, you have removed the trailing white spaces or renamed variables in this line, `git-blame` does not work.

Ref:

+ [How to find out in which commit a particular code was added?](https://stackoverflow.com/q/19875104/6064933)
+ [git log -S](https://git-scm.com/docs/git-log#Documentation/git-log.txt--Sltstringgt)
