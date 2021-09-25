---
title: "Straight.el Bootstrap and Package Install Issue"
date: 2021-09-15T22:38:38+08:00
draft: false
tags: []
categories: [Emacs]
---

I am using [straight.el](https://github.com/raxod502/straight.el) for managing
my packages. However, due to GitHub access issues, the [bootstrap snippet](https://github.com/raxod502/straight.el#getting-started) can not be downloaded
successfully and run. After staring Emacs, it hangs forever with high CPU
usage.

<!--more-->

Here is what is working for me. After following these steps, I can install
packages without issue.

Here is what works for me:

1. Go to  `~/.emacs.d`, the directory varies based on your system[^1].
2. Clone the straight.el repo:

```bash
git clone https://github.com/raxod502/straight.el.git  straight/repos/straight.el
```

3. Change the git default url using the following command as per [guide here](https://doc.fastgit.org/en-gb/guide.html).

```bash
git config --global url."https://hub.fastgit.org/".insteadOf "https://github.com/"
git config protocol.https.allow always
```

4. Remove the straight.el bootstrap snippet from  `init.el`.

Restart Emacs and just wait. All my packages are installed and build
successfully.

# References

+ https://github.com/raxod502/straight.el/issues/665
+ https://github.com/raxod502/straight.el/issues/750
+ https://github.com/raxod502/straight.el/issues/842
+ https://github.com/raxod502/straight.el/issues/302

[^1]: Check [here](https://www.gnu.org/software/emacs/manual/html_node/emacs/Windows-HOME.html) for the actual path.
