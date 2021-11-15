---
title: "Git Diff Setup"
date: 2021-10-24T01:39:09+08:00
draft: false
tags: [diff]
categories: [Git]
---

When we run git-diff, git uses its internal diff algorithm to generate diff for
changed files.

<!--more-->

# The git internal diff algorithms

The default diff algorithm used by git-diff is myers diff. However, myers diff
algorithm may not generate meaningful diffs for code changes. So the patience
diff and histogram diff algorithm is also added later.

[This post](https://luppeng.wordpress.com/2020/10/10/when-to-use-each-of-the-git-diff-algorithms/) shows an example where myers diff does not make much sense
and patience or histogram diff produce better diffs. It seems that histogram
diff generally produces better diff.

To use patience or histogram diff in `git diff`, use the following command:

```
git diff --patience
git diff --histogram
```

However, this does not mean that histogram/patience diff always generate better
diffs. An example is given [here](https://gist.github.com/roryokane/79b8ebcb3813ebd934c4)[^1].

f1:
```
aaaaaa
aaaaaa
bbbbbb
bbbbbb
cccccc
cccccc
abc
```

f2:
```
abc
aaaaaa
aaaaaa
bbbbbb
bbbbbb
cccccc
cccccc
```

The output of `git diff --patience f1 f2` is:

```diff
diff --git a/f1 b/f2
index 3299d68..accc3bd 100644
--- a/f1
+++ b/f2
@@ -1,7 +1,7 @@
-aaaaaa
-aaaaaa
-bbbbbb
-bbbbbb
-cccccc
-cccccc
 abc
+aaaaaa
+aaaaaa
+bbbbbb
+bbbbbb
+cccccc
+cccccc
```

The output of `git diff f1 f2` (remember that myers diff is used by default) is:

```diff
diff --git a/f1 b/f2
index 3299d68..accc3bd 100644
--- a/f1
+++ b/f2
@@ -1,7 +1,7 @@
+abc
 aaaaaa
 aaaaaa
 bbbbbb
 bbbbbb
 cccccc
 cccccc
-abc
```

In this case, result of myers diff make much more sense than that of patience
diff.

# Git external diff tools

Git can also utilize external diff tool via `git difftool` command. Note that
`git difftool` has nothing to do with `git diff` (its naming is a bit confusing
since it may make the users believe that git difftool chooses which tool
git-diff uses).

You can run command `git difftool --tool-help` to check a list of supported
external diff tools. To select a certain tool, use `git difftool --tool
<my-diff-tool>`. For example, to use vimdiff, run `git difftool --tool
vimdiff`.

# Refs

+ What is the default git diff tool: https://stackoverflow.com/q/63985751/6064933
+ Original paper of the myers algorithm: https://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.4.6927
+ https://news.ycombinator.com/item?id=23360223
+ patience vs histogram diff: https://stackoverflow.com/q/32365271/6064933
+ https://www.nathaniel.ai/myers-diff/
+ https://en.wikipedia.org/wiki/Diff
+ https://fabiensanglard.net/git_code_review/diff.php

[^1]: The example compares patience against myers diff, but histogram diff
generates the same result as patience diff in this case.
