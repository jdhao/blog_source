---
title: "Git Learning Notes (3)"
date: 2021-02-17T15:24:46+08:00
draft: false
tags: [Git, diff]
categories: [Git]
---

Git learning notes.

<!--more-->

# fatal: no files added

When I add files under a directory using `git add some_folder/*`, I see the
following error message:

```
The following paths are ignored by one of your .gitignore files:
ppt/__pycache__
Use -f if you really want to add them.
fatal: no files added
```

This is because `*` is interpreted by the system, so it will add all files
under a directory. If some files under this directory are in the `.gitignore`,
you will see the above error message.

Ref:

+ https://stackoverflow.com/q/12084227/6064933

# git ask for username and password each time I push

I cloned a remote repo using [https protocol](https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols).
Each time when I push to this repo, git asks me to enter my username and
password. This is annoying.

To prevent from entering username each time, we can add username to remote repo
url like this:

```bash
git remote set-url origin https://jdhao@github.com/jdhao/toy_project.git
```

where `jdhao` is my username. In this way, we do not need to enter password
when we push to remote. In fact, we can provide the password after the username
like this:

```bash
git remote set-url origin https://jdhao:some_pwd@github.com/jdhao/toy_project.git
```

However, it is not safe to do so, since your password are stored in plain text
now.

For password, if we are in Linux, we can use the following settings:

```bash
git config --global credential.helper 'cache --timeout=3600'
```

The password will be cached in memory for 3600 seconds, which means that you
do not need to input password for 1 hour. Tweak the time interval to suit your
need.

Ref:

+ https://docs.github.com/en/github/using-git/caching-your-github-credentials-in-git#platform-linux
+ [Is there a way to cache GitHub credentials for pushing commits?](https://stackoverflow.com/a/5343146/6064933)

# Compare same file from different branches

Sometimes, I want to see the difference of same file on different branches. For
example, to find the difference for file `foo.py` on master and dev
branch. There are two slightly different ways to do this.

```bash
git diff master..dev path/to/foo.py
git diff master dev -- path/to/foo.py
```

or

```bash
git diff master:path/to/foo.py dev:path/to/foo.py
```

Ref:

+ [How to compare files from two different branches?](https://stackoverflow.com/q/4099742/6064933)

# Clone a git repository without history?

Due to slow networking speed or other reasons, we may want to clone a
repository without its huge history. We can use `--depth` to restrict the
number of history to include, `--depth 1` will only clone the last commit,
effectively meaning that no history is cloned.

```
git clone --depth 1 http://url/to/git/project
```

Ref:

+ https://git-scm.com/docs/git-clone#Documentation/git-clone.txt---depthltdepthgt
+ [Clone git repository without history?](https://stackoverflow.com/q/30001304/6064933)

# How to check last commit for a given line only?

By default, [git-blame](https://www.atlassian.com/git/tutorials/inspecting-a-repository/git-blame)
will print out the last commit info for each line in a file. What if we are
only interested in a particular line range? In this case, we can use `-L` to
restrict the line range. For example, if I want to blame line 4-5 for a file, I
can use the following command:

```
git blame -L 4,5 core/options.vim
```

Ref:

+ [Retrieve the commit log for a specific line in a file?](https://stackoverflow.com/a/19757493/6064933)
