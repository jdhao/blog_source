---
title: "Linux Tips and Tricks -- s1"
date: 2019-08-09 00:02:27+0800
tags: []
categories: [Linux]
---

In this post, I list some of the often-used Linux command in my daily life.

<!--more-->

# Find all files under current folder with extension `jpg` or `txt`?

```bash
find . -type f \( -iname "*.jpg" -o -iname "*.txt" \) -print
```

Ref:

+ [How to use find command to search for multiple extensions](https://unix.stackexchange.com/q/15308/221410)
+ [Unix find: multiple file types](https://stackoverflow.com/q/7190565/6064933)

# Got "argument list too long error" while deleting files

When I delete files directly using `rm some_dir/*`, I get the error that
argument list is too long. We can use `find` to delete files instead:

```bash
find . -type f -name "*.jpg" -delete
```

Ref:

+ [Argument list too long error for rm, cp, mv commands](https://stackoverflow.com/q/11289551/6064933)

# Add a prefix to each line of a file

It is trival to do this via sed:

```bash
sed -i.bak -e 's/^/<Pattern>/' test.txt
```

`i.bak` means to back up original file and create a new file.

Ref:

+ [Add a prefix string to beginning of each line](https://stackoverflow.com/q/2099471/6064933)

# How to use scp to transfer files between local and remote

## Transfer remote file to local

```bash
scp -P <PORT> USER@remote_IP:/path/to/remote/file /local/directory
```

## Transfer local file to remote

```bash
scp -P <PORT> /path/to/local/file  USER@remote_IP:/path/to/remote_dir
```

You can rename the transfered file if you give a complete path to the file
instead of just a remote directory.

## Transfer remote folder to local

```bash
scp -P <PORT> -r USER@remote_IP:/path/to/remote/dir /path/to/local/dir
```

## Transfer local folder to remote

```bash
scp -P <PORT> -r /path/to/local/folder USER@remote_IP:/path/to/remote/folder
```

When transferring folder from local to remote/from remote local, if the
remote/local folder exists, the folder will be put as a child directory,
otherwise, a new folder will be created with the name you give.

# Download file using curl and rename

```bash
curl -o new_name -L file_link
```

`-L` tells curl to redirect.

# How to show system reboot time?

Use `last reboot` to show system reboot time. `who -b` can also show the system
last reboot time.

Ref:

+ [How long has my Linux system been running?](https://unix.stackexchange.com/q/131775/221410)

# Compress (using `tar`) files from text file?

The files we want to compress are written in a text file, e.g, `img_list.txt`.
Each line in `img_list.txt` represents a file path. How to compress all these
files to a single tar ball?

We can use the `-T` option for tar:

```bash
-T, --files-from=FILE
       get names to extract or create from FILE
```

The command is:

```bash
tar zcvf images.tgz -T img_List.txt
```

Ref:

+ [Tar archiving that takes input from a list of files](https://stackoverflow.com/q/8033857/6064933)

# Only search a pattern in certain filetypes?

Suppose we want to search `PATTERN` in certain filetypes, for example, `*.py`
files.

## Use `grep`

```bash
grep -r -i PATTERN --include \*.py
```

## Use ripgrep

[Ripgrep](https://github.com/BurntSushi/ripgrep) is a fast searching tool,
commonly referred to as `rg`. Using rg, it is easier to do this:

```bash
rg PATTERN -g "*.py"
```

Ref:

+ [grep, but only certain file extensions](https://stackoverflow.com/q/12516937/6064933)
+ https://github.com/BurntSushi/ripgrep/blob/master/GUIDE.md#manual-filtering-globs


# Randomly select N files from a folder?

First use `find` to find the certain files you want to select from, then use
`shuf` to randomly select the files.

```bash
find . -type f -name "*.jpg" -print | shuf -n 100
```

Ref:

+ [How can I select random files from a directory in bash?](https://stackoverflow.com/q/414164/6064933)
