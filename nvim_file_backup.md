---
title: "File Backup in Neovim"
date: 2019-11-16 19:56:18+0800
tags: []
categories: [Nvim]
---

There are several options related to file backups in Vim/Nvim.

+ `backup`
+ `writebackup`
+ `backupdir`
+ `backupcopy`
+ `backupext`

How are these options related to each other?

<!--more-->

The option `backup` controls whether to make a physical backup when writing a
file.

The option `writebackup` makes sure that a file backup be made before
overwriting a file. The backup will be removed when the file is successfully
written, unless `backup` option is also on. It reduces the risk that your file
is destroyed if something goes wrong when you are writing the file to disk. For
more info about using `backup` and `writebackup` together, see `:h
backup-table`.

The option `backext` controls the extension used for backup files. `backupext`
will be appended to the backup file names (default is `~`). For example, if
your file is `test.txt` and `backupext` is `.bak`, the backup file name will be
`test.txt.bak`.

The option `backupdir` controls where the backup file will be placed. It is a
list of directories (comma separated) to put the backup files. The first item
in this option will be used. You must make sure that the backup directory
exists, or you will see the following error when writing a file:

> E510: Can't make backup file (add ! to override)

Use the following setting to deal with this error:

```vim
let g:backupdir=expand(stdpath('data') . '/backup')
if !isdirectory(g:backupdir)
   mkdir(g:backupdir, "p")
endif
let &backupdir=g:backupdir
```

There is also an option `backupcopy`, which needs special attention. The value
of `backupcopy` can be `yes`, `no` or `auto`:

+ `yes`: it will copy the original file to the backup location and overwrite the
original file.
+ `no`: it will rename the original file (i.e., move the original file to the
backup directory) and write a new file with the same name.
+ `auto`: Nvim will choose what works best for you.

Note that for some applications, you may want to set `backupcopy` to `yes` to
avoid [issues](https://github.com/webpack/webpack/issues/781#issuecomment-95523711).

# References

+ [Why does vim save files with a ~ extension?](https://stackoverflow.com/q/607435/6064933)
+ [E510 error.](https://github.com/neovim/neovim/issues/3496#issuecomment-151024493)
+ [E510: can not make backup file.](https://github.com/carlhuda/janus/issues/367)
+ [What does backupcopy do?](https://github.com/middleman/middleman-livereload/issues/17#issuecomment-15290009)
+ [What does nowritebackup do?](https://vi.stackexchange.com/q/16843/15292)
