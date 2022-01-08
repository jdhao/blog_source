---
title: "A Tutorial on Creating GitHub Pull Requests"
date: 2018-05-06 15:23:00 +0800
tags: [Git, GitHub]
categories: [technique]
---

Today I make [my first ever GitHub pull request](https://github.com/theme-next/hexo-theme-next/pull/275)
to the great [Hexo](https://github.com/hexojs/hexo) theme [NexT](https://github.com/theme-next/hexo-theme-next).
I will summarize how to make a pull request in following post.

<!-- more -->

# Creating a fork

According to GitHub documentation:

> If you don't have write access to the repository where you'd like to create a
pull request, you must create a fork, or copy, of the repository first.

If you do not have write access to the repository you want to contribute to,
the first step is to fork it. A fork means a complete copy of the original
repository. But now you can do whatever you want on the fork without permission
from the maintainer of the original repo.

It is easy to fork a public repository. Take the theme NexT for an example. Go
to [theme NexT's GitHub repo](https://github.com/theme-next/hexo-theme-next),
and click the "fork" button on the upper right to fork it.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-6/84931782.jpg"
         title="Fork a repo"
         style="float: middle;">

Now I have a forked repo of the original repo. The address of the forked repo
is `https://github.com/jdhao/hexo-theme-next`.

## Create a local clone of your forked repo

If you want to start working on your forked repo, first you need to create a
local clone the forked repo. Go to the forked repo in GitHub, and copy its
address (see image below).

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-6/76914420.jpg"
         title="Copy the address of the forked repo"
         style="float: middle;">

On the command line, use the following command to clone the forked repo:

```sh
git clone https://github.com/jdhao/hexo-theme-next.git # replace this address with your actual address
```

After a while, the forked remote repo will be copied to your local computer.
The folder name is `hexo-theme-next`.

## Add original remote repo as one of your remote

In order to communicate with the original remote repo, we need to add it as one
of our remote repo. Before that, your remote repo is only the forked remote
repo. Use the following command to confirm that:

```sh
cd hexo-theme-next && git remote -v
```

You will see something like:

>origin  https://github.com/jdhao/hexo-theme-next.git (fetch)
>origin  https://github.com/jdhao/hexo-theme-next.git (push)

In the above output, `origin` means your forked remote repo. Then go to the
original remote and copy its address, just as we do for the forked remote. We
then add the original remote as *upstream*:

```sh
 git remote add upstream  https://github.com/theme-next/hexo-theme-next.git
```

Now, using `git remote -v` again, you will see that original repo is also shown
in your remote list:

> origin  https://github.com/jdhao/hexo-theme-next.git (fetch)
> origin  https://github.com/jdhao/hexo-theme-next.git (push)
> upstream        https://github.com/theme-next/hexo-theme-next.git (fetch)
> upstream        https://github.com/theme-next/hexo-theme-next.git (push)

# Make changes

Now you can make changes in your local repo. It is good practice to only change
one feature at a time and commit it. After you have finished making changes to
your local repo, you should commit and push it to your forked remote.

```sh
git add .
git commit -m "change feature xxx"
git push origin master # your forked remote will be updated now
```

# Make your pull request

Now you can make your pull request. Go to the original repo, and click "New
pull request" button,

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-6/52775499.jpg"
         title="Create pull request"
         style="float: middle;">

On the Compare page, click "compare across forks", you will see something like
the following

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-6/2016217.jpg"
         title="Compare fork"
         style="float: middle;">

On the left, there is "base fork". On the right, there is "head fork". Click
the pull-down list to choose the original remote as base fork and choose your
forked repo as head fork.

Then give a title to your pull request. You should also create a description
according to the repo's pull request template. Most serious projects have a
pull request template. Make sure your pull request description follows their
guidelines.

Finally, click the button "create pull request" on the bottom of the page to
finish your pull request. Stay patient and wait for the original repo owner to
review your pull request!

# How to update your pull request

As is often the case, your pull request may need some polishing before it can
be merged. So how do we update our pull request with new changes? It turns out
that git can do that for us "magically"!

You just need to make modifications on your local branch, commit the changes
and push it to your forked remote. Now go to your pull request page, you will
find that the new commit will appear at the bottom of this pull request
discussion (see image below for an example).

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-9/97610550.jpg"
         title="The pull request are updated with new commits"
         style="float: middle;">

# How to sync your master with original repository

A lot of people contribute to an open source project. After you have forked a
project, other people may have committed several changes to the original repo's
master branch. How to keep your forked remote master up to date with the
original repo?

You can fetch the changes from the upstream master and then merge the changes
with your origin master:

```bash
git fetch upstream # fetch latest changes in upstream to your local
git checkout master # make sure that you are on local master
git merge  upstream/master # merge changes with your local master
git push origin master # push changes to your forked remote master
```

In the `git merge` stage, there may be some conflict between your master and
upstream master. In order to merge, you have to resolve the conflicts. See
[here](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/) as a start on how to handle merge conflicts.

# References

+ [How to fork a repo](https://help.github.com/articles/fork-a-repo/#platform-windows)
+ [Create a pull request, general guide](https://help.github.com/articles/creating-a-pull-request/#)
+ [Create a pull request from a fork](https://help.github.com/articles/creating-a-pull-request-from-a-fork/)
+ [Contribute to a project](https://git-scm.com/book/en/v2/GitHub-Contributing-to-a-Project)
+ [How to update a pull request](https://stackoverflow.com/q/9790448/6064933).
+ [How to update a fork](https://stackoverflow.com/a/7244456/6064933)
+ [update a pull request](https://stackoverflow.com/a/15055649/6064933)
