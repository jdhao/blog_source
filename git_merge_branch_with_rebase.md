---
title: "Merging Branches with Git Rebase"
date: 2022-03-07T22:45:16+08:00
draft: false
tags: []
categories: [Git]
---

When merging branches, the most direct way we can use is to `git merge`.
However, with git-merge, the commit history and graph will be messy if there are a lot of branches.

<!--more-->

We can also use `git rebase` to merge branches to make the commit history *cleaner*.
Suppose we have a `feat` branch and `master` branch, and they share a common base commit.

```
-----A------B----C------D(master)
             \
               \---E---F (feat)
```

To merge the two branches using git-rebase, run the following command:

```
git checkout feat
git rebase -i master
```

Note that after running the above step, we may get merge conflict.
We need to fix the conflict, then use `git add some_file` to mark that conflict for `some_file` has been resolved.
Use `git rebase --continue` to fix conflict in other files,
or finish the rebasing process (when there is no conflict anymore).

After the above step, we have the following commit graph:

```
              (master)
---A------C------D------E'---F'(feat)
```

In order to move master to tip of the commit, we can run the following command:

```bash
git checkout master
git merge --ff feat
```

The `-ff` option for git-merge will ascend the master to the same commit as the `feat` branch.


```
---A------C------D------E'---F'(feat)
                             \
                              \(master)
```

To delete the feat branch, run the following command:

```
git branch -d feat
```

Now, you have succeed in merging the two branches using git rebasing.
