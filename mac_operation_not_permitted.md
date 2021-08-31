---
title: "macOS Operation Not Permitted?"
date: 2021-08-15T16:51:57+08:00
draft: false
tags: []
categories: [Mac]
---

Some permission related issue on macOS.

<!--more-->

# File lock issue

For some time, when I open kitty terminal on macOS, I always see the following warning message:

> zsh: locking failed for /Users/jdhao/.histfile: operation not permitted: reading anyway

This may be because the `HOME` directory is locked (for me, this is the case).
To unlock the directory, open the Finder and click the `$HOME` directory, press
<kbd>Command</kbd> + <kbd>i</kbd> to get the file info. Under the `General`
section, make sure that the `Locked` button is unchecked.

To change the file lock state on the command line, use `chflag`:

```bash
chflag -R nouchg /path/to/directory
```

After restarting the terminal emulator, the issue should be gone.

# Operation permission issue

Another issue I often get is that when I use `ls` or `rm` on some files, I
often get a message like the following:

> ls ~/Library/Messages
> ls: Messages: Operation not permitted

This is due to the disk access issue. Open macOS `Security & Privacy` settings
and go to `Privacy` tab. Give your terminal emulator `Full Disk Access`.

Restart your terminal and the issue will be fixed.


# References

+ https://superuser.com/questions/279235/why-does-chown-report-operation-not-permitted-on-os-x
+ File lock issue:
    + https://www.macworld.com/article/231333/macos-file-settings-the-difference-between-locked-and-sharing-permissions.html
    + Change lock state on command line: https://superuser.com/q/40749/736190
    + https://www.jeffgeerling.com/tutorial/2008-12-02/dealing-locked-files-mac
+ Operation not permitted: https://osxdaily.com/2018/10/09/fix-operation-not-permitted-terminal-error-macos/
