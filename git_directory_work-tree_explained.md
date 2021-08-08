---
title: "Git Directory and Work-Tree Explained"
date: 2020-12-25T22:39:22+08:00
draft: false
tags: [Git]
categories: [Git]
---

Git has two option `--git-dir` and `--work-tree`. What is the use for them?

<!--more-->

Usually, when we create a project repository in dir `~/projects/foo` via `git
init` command. The git directory is `~/projects/foo/.git`, where all the
repository info is stored like the following:

```
.git
├── branches
├── COMMIT_EDITMSG
├── config
├── description
├── HEAD
├── hooks
│   ├── applypatch-msg.sample
│   ├── commit-msg.sample
│   ├── post-update.sample
│   ├── pre-applypatch.sample
│   ├── pre-commit.sample
│   ├── prepare-commit-msg.sample
│   ├── pre-push.sample
│   ├── pre-rebase.sample
│   └── update.sample
├── index
├── info
│   └── exclude
├── logs
│   ├── HEAD
│   └── refs
│       └── heads
│           └── master
├── objects
│   ├── 0c
│   │   └── fa957df522a120551c8d6147cacbeffd97d4e1
│   ├── 5e
│   │   └── fb9bc29c482e023e40e0a2b3b7e49cec842034
│   ├── e6
│   │   └── 9de29bb2d1d6434b8b29ae775ad8c2e48c5391
│   ├── info
│   └── pack
└── refs
    ├── heads
    │   └── master
    └── tags
```

The repository's work tree is the root directory of this project, i.e.,
`~/projects/foo`. When we use git command such as `git status` inside in git
repository, git will find `.git` directory in the directory or upward until it
finds the `.git` directory. So we do not need to specify where the `.git` dir
is.

When we want to get the info a repository outside it, we now need to specify
the `--git-dir` and `--work-tree` to tell git which repository we refer to:

```bash
# outside ~/projects/foo
git --git-dir=$HOME/projects/foo/.git --work-tree=$HOME/projects/foo status
```

Note that the path to `--git-dir` and `--work-tree` can be an absolute or
relative path (relative to current working directory). If it is an absolute
path, [tilde `~` won't be expanded](https://stackoverflow.com/a/30878650/6064933)
. You need to use `$HOME` or absolute path.

As a matter of fact, the `.git` and work-tree for a repository does not need to
be in the same location. For example, run the following command:

```bash
mkdir a b
git init --separate-git-dir a/ b/
```

Directory `a/` will act as `.git` directory storing all the info for work-tree
root at `b/`. If you look at the `b/.git`, it will a plain file pointing to the
absolute path of `a/` directory:

```
gitdir: /path/to/a/dir
```

To get info about this repository, we can run git like this:

```bash
git --git-dir=a/ --work-tree=b/ status
```

Refs:

+ [What is the difference in Git between being in a directory (cd) and using the `work-tree` parameter?](https://stackoverflow.com/q/51500812/6064933)
+ [Can I store the .git folder outside the files I want tracked?](https://stackoverflow.com/a/19548676/6064933)
+ https://git-scm.com/docs/git#Documentation/git.txt---git-dirltpathgt
+ https://git-scm.com/book/en/v2/Git-Internals-Environment-Variables
+ [What is GIT_WORK_TREE, why have I never needed to set this ENV var, why now?](https://stackoverflow.com/q/5283262/6064933)
