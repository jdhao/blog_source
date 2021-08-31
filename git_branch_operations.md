---
title: "How to Use Git branches"
date: 2020-09-03 23:41:00+0800
tags: [Git]
categories: [Git]
---

Some commonly used Git commands related to branch operations.

<!--more-->

# Create a new branch (locally and remotely)

## Create branch locally

We can use the following command to create a new branch

```
git branch <new_branch>
```

At this time, we are still in original branch. To switch to new branch, use the
following command:

```
git checkout <new_branch>
```

The two steps can be combined with the following command:

```
git checkout -b <new_branch>
```

## Create branch remotely

To create a remote branch, use the following command:

```
git push -u origin <local_branch>
```

The `-u` option sets a upstream branch of the same name for tracking, i.e., now
our local branch corresponds to the remote branch of the same name.


Ref:

+ [How to create remote branch](https://stackoverflow.com/q/1519006/6064933).
+ [Push local to remote and track it](https://stackoverflow.com/a/6232535/6064933).
+ [git push -u option doc](https://git-scm.com/docs/git-push#Documentation/git-push.txt--u)
+ [What exactly does the “u” do? “git push -u origin master” vs “git push origin master”](https://stackoverflow.com/q/5697750/6064933)

## Create a git branch from a specific commit

Sometimes, we may find that we need to fix a flaw in a specific commit, but
current work has not been finished. We may create a new branch from that commit,
and after that, merge that branch to current branch.

This is how we do using git:

```
git branch fix_bug <commit_id>
git checkout fix_bug
```

`<commit_id>` is the hash of that commit as shown in the `git log` output.

The above command can also be simplified using `-b` for checkout:

```
git checkout -b fix_bug <commit_id>
```

After fixing the issue in branch `fix_bug`, we can then merge the change into
the original branch, say, master branch:

```
git checkout master
git merge fix_bug
```

Ref:

+ [Branch from a previous commit using Git](https://stackoverflow.com/q/2816715/6064933)
+ [How to create the branch from specific commit in different branch](https://stackoverflow.com/q/8483983/6064933)

# Show branches

+ show all branches (local and remote): `git branch -a`
+ show all local branches: `git branch -l`
+ show all remote branches: `git branch -r`

# Delete a branch

+ Delete local branch: `git branch -d <branch_name>`
+ Delete remote branch: `git push -d <remote_name> <remote_branch>`

Note that you **can not** delete a local branch when you are on it! You need
first to checkout to another branch.

Ref:

+ [Delete git branch](https://stackoverflow.com/q/2003505/6064933).

# Rename a branch locally and remotely

First we need to rename the local branch:

```bash
git branch -m foo bar
git branch --unset-upstream foo
```

Then we push this branch to the remote:

```bash
# create a remote branch of the same name and track it.
git push -u origin bar
```

If the original remote branch `foo` is the default branch, you need to set
other branch as the default. Otherwise, you can delete remote branch `foo`.
After making sure that `foo` is default remote branch, run the following
command to delete it from remote:

```bash
# suppose that origin is the remote name
git push -d origin foo
```

Ref:

+ How do I rename both a Git local and remote branch name?: https://stackoverflow.com/q/30590083/6064933

# Create a local branch from a remote branch?

I have a remote branch and I would like to create a local branch based on it.
First, we need to fetch the remote changes:

```
git fetch
```

Then we can create a local branch from the remote branch:

```
git checkout -b local_branch_name remote_name/remote_branch
```

If you do not want to change the remote name, you can also also `-t`:

```
git checkout -t <name of remote>/<remote branch name>
```

It will create a local branch of the same name.

Ref:

+ https://stackoverflow.com/q/1783405/6064933
+ https://stackoverflow.com/q/24301914/6064933
+ [Create local branch from remote branch](https://stackoverflow.com/a/48840672/6064933).

# Find remote tracking branch for local branch

If we want to find the remote local branch a local branch is tracking, we can use the following command:

```bash
git branch -vv
```

We can also use the git remote command to show remote tracking branch:

```bash
git remote show <remote name>
```

Ref:

+ https://stackoverflow.com/q/171550/6064933
+ https://stackoverflow.com/q/4950725/6064933

