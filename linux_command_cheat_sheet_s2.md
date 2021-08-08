---
title: "Linux Tips and Tricks -- s2"
date: 2020-07-23 00:17:04+0800
tags: []
categories: [Linux]
---

A list of Linux command for common operations.

<!--more-->

# Generate fixed width numbers with leading zeros

We can use `seq` command to generate number of fixed width (padded with leading
zeros). For example, to generate number 1 to 999, with width 4 and padded with
leading zeros, we can use the following command:

```bash
seq -f "%04g" 1 999
```

Ref:

+ [Range with leading zero in bash](https://stackoverflow.com/q/13376396/6064933)
+ [How to zero pad a sequence of integers in bash so that all have the same width?](https://stackoverflow.com/q/8789729/6064933)

# Split large files into fixed size part

We can use `split` command to split large files into fixed size parts:

```bash
split [options] zip_file prefix
```

For example, to split `test.zip` into smaller size with each part exactly 4000m,
we can use the following command:

```bash
split -d -b 4000m test.zip test
```

The `-d` option will ensure that numeric indexing is used for the split parts,
`test` is the prefix we use for each part. The generated files are like:

```
test00, test01, test02, ..., test10, ...
```

Ref:

+ [Split files using tar, gz, zip, or bzip2](https://stackoverflow.com/q/1120095/6064933)

# Copy a directory to another directory and show progress

During copy of large files, it is nice to show the progress of copying., we can
use `rsync` to copy a directory to another directory and show progress:

```bash
rsync -ah --progress src dest
```

Since rsync version 3.1.0, we can also use the following options:

```bash
rsync -ah --info=progress2 src dest
```

The progress shown is not the overall progress, it is just the progress for a
single file. Nonetheless, it is better than nothing and shows that something is
being copied.

Note in the above, there is not slash `/` after `src`, which makes sure that
`src` is copied as `dest/src` with all its structures. If you want to copy all
files and folders under `src` to `dest` and do not create `src` directory under
`dest`, you can add a slash after src:

```bash
rsync -ah --progress src/ dest
```

Ref:

+ [How to rsync a directory to a new directory with different name?](https://unix.stackexchange.com/q/178078/221410)
+ [How can I view a progress bar when running rsync?](https://askubuntu.com/q/609303/768311)
+ [rsync -r to existing folder](https://unix.stackexchange.com/q/451007/221410)
+ [Showing total progress in rsync: is it possible?](https://serverfault.com/q/219013/435975)

# Move all files and subdirectories to a directory except one

Suppose I am at a certain directory and want to move all the files and
sub-directories to a directory except one file (`test.jpg`). We can use the
following command to achieve what we want:

```bash
\ls | \grep -v test.jpg |xargs -I{} mv {} other_directory/
```

Note that in the above command we use backslashed version of `ls` and `grep`
command to avoid any command alias, because the command alias may cause the
failure of the above command.

Ref:

+ [Run command shadowed by an alias](https://unix.stackexchange.com/q/39291/221410).
+ [Mv all files except one](https://stackoverflow.com/q/670460/6064933).


# zip all files under a directory without the directory itself?

If we have a `test` folder under which there are a lot of files and
directories. If we use the following zip command to compress the directory:

```bash
zip test.zip -r test/
```

this will add one more level of folder structures. We get the following:

```
--> test
    --> test
        --> all files and folders under original test folder
```

The original `test` folder is also included in the zipped file.  To avoid this,
we can use the following command:

```bash
cd test && zip -r ../test.zip .
```

If there are no directories under `test` or we do not care about directory
structure under `test`,  we can also use the `-j` option:

```bash
zip test.zip -j -r test/
```

The above command will flatten all files under `test` recursively into one
directory, which may not be what you want, especally when there are same name
filder under different child directories.

Ref:

+ [Zip the contents of a folder without including the folder itself](https://unix.stackexchange.com/q/182032/221410)
+ [Zip an archive without including parent directory](https://askubuntu.com/q/521011/768311)
