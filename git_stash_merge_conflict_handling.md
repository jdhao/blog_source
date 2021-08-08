---
title: "Resolving Merge Conflict after Git Stash Pop"
date: 2019-12-03 00:57:11+0800
tags: [Git]
categories: [Git]
---

Sometimes, when we are working with our local changes in a Git repo, we may
want to pull the latest updates from the remote repository. To avoid code
conflict between remote files and local files. We can use `git stash` to store
the unfinished local work temporarily.

<!--more-->

Git-stash gives you a clean repo and revert the repo state to last commit. Then
we can use `git pull` to pull the updates.

# Git stash basic knowledge

## Stashed changes are per repository, not per branch.

If you are in a certain branch and do a `git stash`, you may think that the
stashed changes only belong to that branch. However, that is not the case.
Since you have not committed the changes, they do not belong to any branch,
even if you are currently working on a specific branch (see
[here](https://stackoverflow.com/q/20526355/6064933)).

## Show stashed changes

To show stashed changes, use `git stash list`. It will show a list of stash you
have made.

## Show the detailed info of a particular stash

Each stash in the stash list can be referenced by its number. To show changes
in a particular stash, we can use the following command:

```
# show changes in the first stash
git stash show stash@{0}
```

The above command only shows summary changes to files in a stash. To show
detailed changes of a stash, add the `-p` option:

```
git stash show -p stash@{0}
```

# Applying git stash

After the git-pull, we may want to continue our unfinished work. We can use
`git stash pop` to restore unfinished work. You can also use `git stash apply`
instead. The difference between `git stash pop` and `git stash apply` is that
the former will drop the stash if there is no merge conflict, while the later
will never drop the stash even if the stashed changes merge successfully with
the pulled code.

Anyway, if everything goes well, our unfinished changes can merge with the
remote updates without any conflict. However, if we and other collaborator both
have changed the same file at the same place, we will encounter merge errors
after git-stash. The error message is like the following:

```
Auto-merging test.txt
CONFLICT (content): Merge conflict in test.txt
The stash entry is kept in case you need it again.
```

# Conflict markers

When we open the conflict file, we will see some markers like the following:

```txt
abc
<<<<<<< Updated upstream
qwe
=======
lmn
>>>>>>> Stashed changes
```

These markers are called [`conflict
markers`](https://wincent.com/wiki/Understanding_Git_conflict_markers), which
are used to indicate the conflicting changes made to a file.

In the git-stash case, code between `<<<<<<<` and `=======` are the changes
made upstream, and code between `=======` and `>>>>>>>` are your stashed
changes.

# Merge conflict

To merge the conflict, you have basically two methods:

+ Resolve the conflict manually: decide if you want your change or the upstream
change or you want to combine the local and remote change. Then edit the file
accordingly and remove those conflict markers.
+ Resolve the conflict with mergetools. See [here](https://stackoverflow.com/questions/161813/how-to-resolve-merge-conflicts-in-git) for an introduction.

## Mark conflict as resolved

After resolving the conflict, you can use `git add` to mark the conflict as
resolved. But in the git-stash scenario, you may not want to do this since your
changes are not finished yet.

Or you can use `git reset` to mark that the conflict has been solved. You can
continue working on your project until your changes are completed, and then
commit your changes.

After the conflict has been resolved, do not forget to use `git stash drop` to
drop the stash because the stash will not be dropped automatically in case of a
merge conflict.

# References

+ [Preview changes for a git stash.](https://stackoverflow.com/q/3573623/6064933)
+ [Difference between git stash pop and git stash apply](https://stackoverflow.com/q/15286075/6064933).
+ [Git merge left HEAD marks in my files](https://stackoverflow.com/q/10657315/6064933).
+ [Resolve Git stash conflict.](https://stackoverflow.com/q/51166531/6064933).
+ https://stackoverflow.com/q/55366930/6064933
+ [Resolve git stash conflict without commiting.](https://stackoverflow.com/a/7932912/6064933)
+ [Resolve conflict after stash pop](https://www.stefaanlippens.net/resolve-git-unmerged-paths-after-stash-pop/).
