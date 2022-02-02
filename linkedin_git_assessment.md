---
title: "Selected Questions from LinkedIn Git Assessment"
date: 2022-01-14 19:06:57+0800
draft: false
tags: []
categories: [Git]
---
<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/202202012337737.jpg" width="800">
</p>

I took the [LinkedIn Git assessment](https://github.com/Ebazhanov/linkedin-skill-assessments-quizzes/tree/master/git) and got a certificate[^1], but found that I am not super clear on some of the questions.
Here is an analysis of some of the questions and their answers.

<!--more-->

## different ways of `git add` and their meaning

I have written about this in [this post](https://jdhao.github.io/2020/04/23/git_learning_notes_s1/#difference-between-git-add--a-git-add--and-git-add--u).

## git reset: soft vs mixed vs hard

When using  git-reset, there three different types of reset, in term of disruptiveness:

+ `--soft`: moves HEAD to specified commit, and changes in the reset commits are squashed and kept in the staging area.
+ `--mixed`: moves HEAD to specified commit, changes in the reset commits as well as changes in staging areas are moved to working tree. This is the default reset behaviour.
+  `--hard`:  moves HEAD to the specified commit, but all the changes (changes in the reset commit, in staging area , as well as changes in working tree) are discarded.

Ref:

+ soft vs mixed vs hard: https://stackoverflow.com/a/50022436/6064933
+ what does git reset do: https://stackoverflow.com/q/2530060/6064933

## What the following command do:

```
git checkout master
git cherry-pick kj2342134
```

It pick the commit `kj2342134` and apply it on master, i.e., it will try to patch master branch with commit `kj2342134`.

Note we are likely to meet merge conflict. We need to resolve the conflict and then `git add conflict_file`,
then run `git cherry-pick --continue`.

Ref:

+ what does cherry-pick do? https://stackoverflow.com/q/9339429/6064933

## show changes in files via git diff-tree

```
git diff-tree --no-commit-id --name-status -r <SHA>
```

The output may look like this:

```
D       autocommands.vim
A       core/autocommands.vim
A       core/mappings.vim
A       core/options.vim
A       core/plugins.vim
A       core/ui.vim
A       core/variables.vim
M       init.vim
D       mappings.vim
D       options.vim
D       plugins.vim
D       ui.vim
D       variables.vim
```

Ref

https://stackoverflow.com/a/24819616/6064933

## what does `git commit -a` do?

It will stage modified and deleted files (**but not new files**) to staging area and commit them to index.
So it is equivalent to the following two commands:

```
git add -u
git commit
```

Ref:

+ https://stackoverflow.com/a/6840337/6064933

## Status after modifying staged files

Why do you see the following `git status` output?

```
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   test.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   test.txt
```

It means that after `test.txt` is added to staging area, and you have changed it.

## what does the following command do:

```
git fetch --all
git reset --hard origin/master
```

It will fetch the remote changes, then discard all local changes and move HEAD to remote master.
This will effectively make your local master the same as your remote master.

Ref:

+ Difference between `git pull` and `git fetch`: https://stackoverflow.com/q/292357/6064933

## Does  `git push` push all tags to remote by default?

No, if you create a tag locally, they are not pushed to remote by default when you do `git push`.
To push a certain tag to remote, use:

```
git push origin {some-tag}
```

To push all tags to remote:

```
git push origin --tags
```

Ref:

+ https://jdhao.github.io/2021/05/08/git_tag_notes/#push-tags-to-remote

## check the help for a git subcommand

For example, we want to check doc for `git reset`, how do we do it on command line?
There are two ways:

+ `git help reset`
+ `man git-reset`

## How do we restore to original state if we meet a merge conflict and decided to discard the changes?

Use `git merge --abort` .

Ref:

+ abort a merge: https://stackoverflow.com/a/2534968/6064933

## Show branches that has been merged by current branch?

Use `git branch --merged`

## Clean untracked file in a repo?

We can use `git clean -f` (`-f` means with force) to clean untracked files in a git repo.

Note that this will not remove untracked directories by default.
To also remove untracked directories (be careful if you really want to), add `-d` option: `git clean -d -f`.

To see the dry-run result (do not actually delete the files), use `--dry-run` or `-n`.

## difference between `git fetch` and `git pull`

You can think of git-pull as a two step operation: first we use `git fetch`,
then we use `git merge` to merge the remote tracking branch.

## Show what is in a git stash without applying it

First, use `git stash list` to list a list of stashes. The output is like this:

```
stash@{0}: WIP on feat: c6c7af1 4 and 5
stash@{1}: WIP on feat: c6c7af1 4 and 5
stash@{2}: WIP on feat: c6c7af1 4 and 5
```

Then, to see the changes in first stash, use `git stash show -p stash@{0}`.
We can think of stash as a stack, where top is your most recent stashed changes.


[^1]: You need to be the top 30% to get one.
