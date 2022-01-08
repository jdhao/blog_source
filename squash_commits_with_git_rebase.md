---
title: "A Guide on Squashing Commits with git-rebase"
date: 2021-06-28T23:43:56+08:00
draft: false
tags: [Git]
categories: [Git]
---

In this post, I would like to share how to squash commits using [git-rebase](https://git-scm.com/book/en/v2/Git-Branching-Rebasing).

<!--more-->

# Squash several commits with rebase

I have a git repo with the following commit history:

```
0022f04 - (HEAD -> master) demo 2 (2021-06-28 16:48:30 +0800) <jdhao>
e4072cf - demo 1 (2021-06-28 15:49:51 +0800) <jdhao>
da88b72 - line 3 (2021-06-28 14:47:43 +0800) <jdhao>
08b0518 - line 2 (2021-06-28 14:46:55 +0800) <jdhao>
ac0bd14 - line 1 (2021-06-28 14:46:33 +0800) <jdhao>
```

Since the most recent two commits are related, I want to squash them into one commit,
i.e., combining commit `0022f04` and `e4072cf` into one. How should I do it?

We can use git-rebase to achieve this. First, run the following command:

```bash
# rebase the recent two commits
git rebase -i HEAD~2
```

Here `HEAD~2` refers to commit `da88b72`. So we are rebasing the most recent two commits against it.

After using this command, git will open the vim editor and show the following interface:

```rebase-todo
pick e4072cf demo 1
pick 0022f04 demo 2

# Rebase da88b72..0022f04 onto da88b72 (2 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
```

The two commits we want to squash are shown inversely, compared to their position in git-log output.
In the interface, each line shows a specific commit. The format is `<command> <commit-hash> <commit-msg>`.

For squashing, a commit is squashed against previous commit, i.e., the line before it.
Note we can not squash first commit, since there is no previous line.
You will see errors if you try to do so:

```
error: cannot 'squash' without a previous commit
You can fix this with 'git rebase --edit-todo' and then run 'git rebase --continue'.
Or you can abort the rebase with 'git rebase --abort'.
```

Okay, back to the rebase interface, we then edit this file and change `pick` in the lines after first line to `squash`.

```
pick e4072cf demo 1
squash 0022f04 demo 2
```

Then we use command `:wq` to save the edit and quit Vim.
Git will automatically open another interface ---
this time for editing the commit message of the squashed commits.
It will pre-populate the file with commit messages of the squashed commits.
You can keep it as is or write a new and unified message.
Save the file and quit. Git will squash commit `0022f04` onto `e4072cf`, creating a new commit.
Now, git log shows the following message:

```
adc8ebd - (HEAD -> master) Demo 1 and 2 (2021-06-28 17:41:16 +0800) <jdhao>
da88b72 - line 3 (2021-06-28 14:47:43 +0800) <jdhao>
08b0518 - line 2 (2021-06-28 14:46:55 +0800) <jdhao>
ac0bd14 - line 1 (2021-06-28 14:46:33 +0800) <jdhao>
```

## How to abort rebase when we are inside vim?

If we want to abort the rebase process when we are inside Vim, how do we do it?
Based on answer [here](https://stackoverflow.com/q/27226886/6064933), we should use command `:cq` to quit Vim.

# Squash commits onto first commit

If we want to squash the first three commits into one, things become a little tricky,
since first commit is literally the root of all your commits, so there is no commit before it.
In this case, we need to use the `--root` option:

```bash
git rebase -i --root master
```

We have the following rebase interface:

```
pick ac0bd14 line 1
pick 08b0518 line 2
pick da88b72 line 3
pick adc8ebd Demo 1 and 2

# Rebase adc8ebd onto bcc1018 (4 commands)
#
```

We need to squash commit `08b0518` and `da88b72` onto commit `ac0bd14`.
So we edit the file to become the following:

```
pick ac0bd14 line 1
squash 08b0518 line 2
squash da88b72 line 3
pick adc8ebd Demo 1 and 2
```

Follow the instructions and complete the rebase process. Now we only have two commits left:

```
dc5d518 - (HEAD -> master) Demo 1 and 2 (2021-06-28 20:07:19 +0800) <jdhao>
5452bfe - Line 1, 2 and 3 (2021-06-28 20:07:04 +0800) <jdhao>
```

Note that if we change the parent commit, the hash of any following commits also changes.
The commit has for `Demo 1 and 2` changes from `adc8ebd` to `5452bfe`.

# Avoid squash commits that have been pushed to remote

It is now rather clear that rebasing will change the commit history.
If you are collaborating with other people and have pushed some commits to the remote,
and your co-workers have already check out those commits, it is best to avoid rebasing those commits.
Otherwise, **your co-workers would be in serious trouble!**

# References

+ squash the first two commits in git: https://stackoverflow.com/a/598788/6064933
+ Git squash older commits: https://stackoverflow.com/a/56262851/6064933
+ Git merging vs rebasing: https://www.atlassian.com/git/tutorials/merging-vs-rebasing
