---
title: "How to Use The Git-tag Feature"
date: 2021-05-08T00:16:06+08:00
draft: false
tags: [Git]
categories: [Git]
---

My notes on using tag feature of Git.

<!--more-->

We can use git-tag to create a memorable name for a specific commit, for example, `v1.0`, `v2.0`.

# Create tags

## Lightweight and annotated tags

There are two different types of tag: lightweight and annotated tag.

To create lightweight tags, do not use `-a` option:

```
git tag v0.5
```

To create annotated tags, use `-a` option and `-m` (for tag message):

```
git tag -a v0.5  -m "some info about this commit"
```

For annotated tags, when we use `git show {tag-name}`, the tag message will also be displayed.

## Tag a specific commit

To tag a specific commit, we can provide its commit hash:

```
# in this example, 8a5cbc4 is the commit hash.
git tag -a v1.2 8a5cbc4 -m "some information related to this tag"
```

# List tags

To list tags, use `git tag -l` or just `git tag`.

Git-tag also supports globing for tags matching a pattern.
For example, to list all tags in version 1.1 series, use `git tag -l "v1.1.*"`.

# Show a tag

To show a tag, use `git show {tag_name}`, for example, `git show v1.0`.

# Push tags to remote

To push a tag to remote repo, we have to explicitly push it.
`git push origin {branch-name}` does not work in this case.

```
# this will push tag v1.2 to origin
git push oirgin v1.2
```

To push all tags to remote, use command `git push origin --tags`.

# Delete a tag

To delete a tag, use `git tag -d {tag-name}`, for example, to delete tag `v1.2`, we use `git tag -d v1.2`.

To delete a tag in remote repo, use `git push {remote-name} -d {tag-name}`.

# References

+ https://git-scm.com/book/en/v2/Git-Basics-Tagging
+ https://git-scm.com/book/en/v2/Git-Basics-Tagging
+ annotated vs non-annotated tags: https://stackoverflow.com/a/11514139/6064933
