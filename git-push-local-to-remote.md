---
title: "How to Push Local Git Repo to Remote Repo in GitHub"
date: 2018-05-16 16:22:54 +0800
tags: [Git, GitHub]
categories: [Git]
---

There are two basic cases when we push our local repo to remote repo. These two
cases differ in their settings. But sometimes, we are easily confused.

<!-- more -->

# First scenario

The first case is that you have created a local repo and want to store it on
GitHub later. In this case, your usual work flow for local repo is:

1. Initialize the local repo (`git init`)
2. Write you code and document, etc.
3. Add changes to index (e.g., `git add .` )
4. Commit the changes (`git commit -m "some message"`)
5. Repeat step 2 -- 4

Then, at some point, you want to push this local repo to GitHub. First, you
need to create a new repo in GitHub. But be careful that you __should not__
create a README file in this repo.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-16/22009101.jpg"
         title="Do not create a README!"
         style="float: middle;">

If you do create a README file, you will encounter errors when you try to push
to this remote repo:

```
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

If you try to pull from the remote repo to fix the above error, you will then
see error message such as:

> fatal: refusing to merge unrelated histories

In order to spare yourself from these troubles, just **do not create a README
file!** Just create an empty repo.

After you have created an empty remote repo, you should add it as one of your
remote. You can then push to it smoothly.

```bash
git remote add upstream <your_repo_url>
git push upstream master
```

If you accidentally created it README file, you can also force to push to this
remote repo:

```bash
git push -f upstream master
```

The above option [`-f`](https://git-scm.com/docs/git-push#git-push--f) will
erase the commit on the remote repo. You can then force push to the remote
repo.

# Second scenario

The other case is that you already have a remote repo[^1] and you want to
continue to develop it locally. In this case, you should first clone this
remote repo and then develop it locally.

```bash
git clone <your_remote_repo>
```

After cloning this remote repo, you can develop it locally. In this case, you
do not have to manually add this repo as your remote. It will be added as your
remote by default with the name `origin`.

In order to push to this repo, we use the following command:

```bash
git push origin master
```

# References
+ <https://stackoverflow.com/questions/17291995/push-existing-project-into-github>
+ <https://stackoverflow.com/questions/23304688/issue-pushing-to-a-github-repository-created-with-initialize-repo-with-a-readme>

[^1]: For example, you have forked someone else's repo.
