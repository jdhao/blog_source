---
title: "How to Squash Last N Commits in Git?"
date: 2022-01-09T11:47:17+08:00
draft: false
tags: [rebase, squash]
categories: [Git]
---

We often need to squash several related commits to make the commit history cleaner.
There are several different ways to achieve this.

<!--more-->

Here, suppose we want to squash last 2 commits.

# Using git rebase

I have covered this method in [my previous post](https://jdhao.github.io/2021/06/28/squash_commits_with_git_rebase/), so I won't repeat it here.

# soft reset, then commit

First, we run the soft reset command:

```bash
git reset --soft HEAD~2
```

This will reset last two commits, but keep their changes.
If you run `git status` command, you will see that you have uncommitted changes.
These changes are a combination of changes in last two commits.

Now, you just commit again and write the new commit message:

```bash
git commit -m "some msg blah blah blah"
```


# hard reset, then merge --squash

First, we run the hard reset command:

```bash
git reset --hard HEAD~2
```

This will discard changes in the recent two commits, but there are actually not lost.
`HEAD@{1}` represents where your HEAD was before you use hard reset.
`HEAD@{2}` represents your HEAD two moves ago. So `HEAD@{}` variable logs your HEAD movement.
We can check the HEAD movement using [`git reflog`](https://git-scm.com/docs/git-reflog). An example output is like this:


```
0073058 (HEAD -> master) HEAD@{0}: commit: 9th
618c9fd HEAD@{1}: commit: 8th
ed79391 HEAD@{2}: commit: Squashed commit of the following:
f0fb17b HEAD@{3}: reset: moving to HEAD~2
b2ee13a HEAD@{4}: reset: moving to HEAD
b2ee13a HEAD@{5}: reset: moving to HEAD
b2ee13a HEAD@{6}: reset: moving to HEAD
b2ee13a HEAD@{7}: reset: moving to HEAD
b2ee13a HEAD@{8}: reset: moving to HEAD
b2ee13a HEAD@{9}: reset: moving to HEAD
b2ee13a HEAD@{10}: reset: moving to HEAD
b2ee13a HEAD@{11}: commit: 7th
004a853 HEAD@{12}: commit: sixth
f0fb17b HEAD@{13}: commit: fifth
d9f10ed HEAD@{14}: commit: fourth
774b308 HEAD@{15}: commit: thrid
ad2fb58 HEAD@{16}: commit: second
efe6902 HEAD@{17}: commit (initial): first
```

Now, we will run `git merge --squash HEAD@{1}` to squash the recent two commits.
The merge window should be pre-populated with the commit message of recent two commits.

# References

+ [Squash my last X commits together using Git](https://stackoverflow.com/q/5189560/6064933)
+ Meaning of `HEAD@{N}` notation: https://stackoverflow.com/a/21911246/6064933
+ Difference between `merge --squash` and rebase: https://stackoverflow.com/q/2427238/6064933
