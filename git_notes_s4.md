---
title: "Git Learning Notes (4)"
date: 2021-11-27T22:29:36+08:00
draft: false
tags: []
categories: [Git]
---

My Git learning notes.

<!--more-->

# Generate patch and apply it

Sometimes, we need to develop the same project in different places, and in some
place, we may not be convenient to push directly to the remote repo. We can
generate a patch instead and apply the patch somewhere else.

First, run `git diff > mypatch` to generate a patch for the repo. To apply the
patch somewhere, run `git apply mypatch`.

Ref:

+ https://gist.github.com/nepsilon/22bc62a23f785716705c
+ https://stackoverflow.com/q/5159185/6064933

# Git: how to get out of detached head state

In git, when we check out to a specific commit using `git checkout <commit_id>`,
git warns us that:

```
You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b new_branch_name

HEAD is now at 132ba1b... {some commit message}
```

Detached head means that we are current in no branch. To return to the previous
branch we are in, use `git checkout -` or `git checkout original_branch_name`
if you know the branch name.

Ref:

+ https://stackoverflow.com/q/11801071/6064933
+ https://stackoverflow.com/q/2304087/6064933

# git: show commit that changes a specific file

To show all commit that changes a specific file, use `git log --follow --
file_path`. `--follow` will track the commit that changes the file before its
renaming, so it is highly recommended to include this option.

Ref:

+ https://stackoverflow.com/q/3701404/6064933

# use a git mirror site for cloning

Due to access issues, sometimes we may want to use a github mirror service like
[fastgit](https://fastgit.org/). However, manually change the repo url from
github.com to fastgit is not efficient enough. Fortunately, we can utilize the
git url rewrite feature to achieve this globally. Run the following command:

```bash
git config --global url."https://hub.fastgit.xyz".insteadOf https://github.com
```

After that, when you clone a github repo, the actually url used will be the
corresponding fastgit repo.

Ref:

+ https://gist.github.com/Kovrinic/ea5e7123ab5c97d451804ea222ecd78a
+ [How to convert `git:` urls to `http:` urls](https://stackoverflow.com/q/1722807/6064933)

# Ignore a specific commit when blaming?

Some commit may only change the format of a file, when doing git-blame, we may
want to ignore such commits. We can use `git blame --ignore-rev <commit-hash>`
to ignore a commit. Or, use `git blame --ignore-revs-file ignore-file.txt`,
where `ignore-file.txt` contains a list of commits we want to ignore.

Ref:

+ https://akrabat.com/ignoring-revisions-with-git-blame/
+ https://stackoverflow.com/q/34957237/6064933
