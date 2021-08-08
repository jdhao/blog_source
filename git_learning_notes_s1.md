---
title: "Git Learning Notes (1)"
date: 2020-04-23 00:05:20+0800
tags: [Git]
categories: [Git]
---

This is my personal note of learning Git.

<!--more-->

# Uncommited changes in a file are visible in all branches?

I checked out from master branch to branch `feature-foo` and made some
uncommitted changes in a file `foo.py`. When I switch back to branch master, I
am surprised to find that changes in `foo.py` are visible in master branch.

I have also thought that changes in a branch belongs to that branch, but it
seems that I am terribly wrong. The truth is: until you commit your changes in
a certain branch, it will be visible in other branches too.

### References

+ [Git automatically merge changes in one branch to master](https://stackoverflow.com/q/8111991/6064933).
+ [Git keeps showing my changes when I switch branches](https://stackoverflow.com/q/5531362/6064933).
+ [Why are changes in one branch visible in another branch](https://stackoverflow.com/q/47471400/6064933)?


# How to show file version in a specific commit

We can use `git show <revision>:/path/to/file` to show the version of file in a
specific commit.

`<revision>` can be a commit hash or something like `HEAD` or `HEAD~2` or tag
name that can identify a specific commit. For example, to show file `readme.md`
in current HEAD, use the following command:

```bash
git show HEAD:readme.md
```

To show `readme.md` in commit `410d12ed0`, use `git show 410d12ed0:readme.md`.

### References

+ https://www.systutorials.com/how-to-view-a-file-at-a-specific-commit-in-git/
+ https://stackoverflow.com/a/338470/6064933

# Delete files that have been commited after adding them to gitignore

We may have commited some files that we do not want to include in the repo to
the remote repo. How do we delete these files from remote repo?

First, we need to edit `.gitignore` file and add the files we want git to
ignore. Then use the following command to delete these files from remote repo:

```bash
git rm --cached path/to/file
git commit -m "delete unwanted files"
git push origin master
```

The command `git rm --cached test.txt` will remove file `test.txt` from the Git
index, but not from your disk. If you omit option `--cached`, the file will
also be removed from your disk.

### References

+ https://stackoverflow.com/a/7927283/6064933

# Show difference between working directory, index and commit tree for a certain file

Suppose we want to know the difference bewtween working directory, index, and
commit tree for file `test.txt`, we can use the following commands:

+ `git diff test.txt`:  difference between working directory and index
+ `git diff HEAD test.txt`: difference between working directory and HEAD
+ `git diff --staged test.txt` or `git diff --cached test.txt`: difference between index and HEAD.

### References

+ https://stackoverflow.com/q/3527856/6064933
+ https://stackoverflow.com/a/30023448/6064933

# Difference between `git add -A`, `git add .` and `git add -u`?

For the commit tree, there are three types of files: modifed, deleted and new
files. What these commands do to the three types of files are:

| command                      | modified files | deleted files | new files | description                                         |
|------------------------------|----------------|---------------|-----------|-----------------------------------------------------|
| `git add -A`                 | yes            | yes           | yes       | stage modified, deleted and new files               |
| `git add .`                  | yes            | yes           | yes       | stage modified, deleted and new files EndFragment   |
| `git add -u`                 | yes            | yes           | no        | stage modified and deleted files, but not new files |
| `git add --ignore-removal .` | yes            | no            | yes       | stage new and modified files, but not deleted files |

Using `git add -A` and `git add -u` without specifying the path will act on
the entire working tree, regardless of your current directory:

> If no <pathspec> is given when `-u` option is used, all tracked files in the
> entire working tree are updated (old versions of Git used to limit the update
> to the current directory and its subdirectories).

### References

+ https://stackoverflow.com/a/26039014/6064933

# How to list all file in current repository

We can use `git ls-tree` command:

```bash
git ls-tree --name-only --full-tree -r HEAD
```

The meaning of options:

+ `--full-tree`: print the whole tree under the working tree root no matter which directory you are in
+ `--name-only`: print file names only (do not show file type and hash etc.)
+ `-r`: recursively list files under the root directory.

Note that if `-r` is omitted and only `--full-tree` and `--name-only` are used,
only files and directories under repo root are listed.

We can also use `git ls-files` to show all the file in current repo. By
default, `git ls-files` will also show files that are staged but not yet
committed, which is different from `git ls-tree` since it only shows committed
files. `git ls-files` also has more features than that.

### References

+ https://stackoverflow.com/q/8533202/6064933
