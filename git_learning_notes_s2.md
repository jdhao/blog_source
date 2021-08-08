---
title: "Git Learning Notes (2)"
date: 2020-05-07 00:35:45+0800
tags: [Git, Vim]
categories: [Git]
---

My Git learning notes.

<!--more-->

# How to change most recent commit message

After we have added a local commit, we may find that we need to do some updates
before pushing to remote repo. We can use `git commit --amend` to change last
commit.

With `--amend` option, we can change not just commit message. Files in local
repo can also be updated. After using this option, last local commit will be
replaced with the new commit.

## References

+ https://stackoverflow.com/q/179123/6064933
+ https://help.github.com/en/github/committing-changes-to-your-project/changing-a-commit-message

# How to show changes for a specific file

Sometimes, we want to show the changes for a particular file across commits.
This can be achieved by `git log` command:

```
git log -p <file_path>
```

The `-p` option will show detailed diff info for the file.

A more complete and useful version is:

```
git log --stat -p --follow <file_path>
```

Meaning of options used:

+ `--stat`: show info about file changed, deletions and insertions.
+ `--follow`: show file change info before it was renamed (by default, file
history before renaming is not shown).

To show only the latest five changes, we can use the `-n` option:

```
git log --stat -p --follow -n 5 <file_path>

```

A similar command is `git whatchanged`:

```
git whatchanged -p <file_path>
```

The git-whatchanged command is similar to `git log` and is kept for backward
compatibility. New users are encouraged to use `git log` instead.

## References

+ [Difference between git-log and git-whatchanged](https://stackoverflow.com/q/10022722/6064933).
+ [git-whatchanged doc](https://git-scm.com/docs/git-whatchanged).
+ https://stackoverflow.com/a/12359963/6064933
+ [See changes to a specific file](https://stackoverflow.com/a/31306082/6064933).
+ https://kgrz.io/use-git-log-follow-for-file-history.html
+ [Git log doc](https://git-scm.com/docs/git-log).

# How to set up editor for git commit

I am used to writing my commit message on the command line with `-m` option
(`git commit -m "xxx"`). This works well for short commit message. When we want
to write the detailed explanation for a change in commit message, it is better
to do it in a text editor, e.g., in Vim or Neovim.

There are basically two way to set up the editor.

+ Set up the `EDITOR` and `VISUAL` env variable:
    ```bash
    export EDITOR="nvim"
    export VISUAL="$EDITOR"
    ```
+ Set up the editor git uses via `git config`:
    ```
    git config --global core.editor "nvim"
    ```

After that, when you want to commit, just type `git commit` in the command line.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200507005506.png" width="800">
</p>

Neovim will be opened with a file named `COMMIT_EDITMSG`, you can edit this
file. Anything not starting with `#` will be treated as your commit message.
After you have done writing the commit message, exit the insert mode, save the
file and quilt. This commit will be done.

## References

+ https://stackoverflow.com/q/2596805/6064933
+ https://stackoverflow.com/q/13239368/6064933
