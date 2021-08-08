---
title: Commonly-used Methods in Python OS Package
date: 2016-03-17
tags: []
categories: [Python]
---

In this post I summarize the commonly-used methods in Python os package.

<!--more-->

# method related to paths

|   method   | description|
| ---- | --- |
|os.path.join|  join string to from a path |
|os.path.split| split directory and file name  |
|os.path.basename|  get the base name of file from a complete path  |
|os.path.abspath|   get the absolute path a file |
|os.path.dirname|  get the directory name from a path  |

If you want to combine several string to form a valid path, you can use
`os.path.join()`. For example, if you want to combine `"F:\\paper_ref"`,
`"cnn_classify"`, `"hinton2012.bib"` into a complete path，you can manually
combine them using path separator of your system. But this way has some
potential portability issue and is also error-prone. The best way is to use
`os.path.join` method.

```
os.path.join("F:\\paper_ref","cnn_classify","hinton2012.bib")
```

`os.path.split` can split the directory name and file anme, for example,

```
os.path.split("d:/my_program/read_image.cc")
# output: ('d:/my_program', 'read_image.cc')
```

you can use `os.path.basename` to remove directory info and only get the base
name of a file.

```
os.path.basename("f:/test/test.jpg") # output: 'test.jpg'
```

With `os.path.abspath`, we can get the absolute path of a file in a folder.
Suppose there is file named `colour_themes.conf` under current directory, you
can use following command to get its absolute path,

```
os.path.abspath('colour_themes.conf')
# output: '/home/jdhao/files/colour_themes.conf'
```

The `os.path.dirname()` method will return the path of a file (not including
the file name)

```
os.path.dirname("file/music/song1.mp3")
# output: file/music
```

# check path or file

| method | decription |
| ---- | ---- |
|os.path.isabs() |decide if a path is absolute path|
|os.path.isdir() |decide if a path is directory path and check if it really exists|
|os.path.isfile() |decide if the given string is a file, also check if it really exists|
|os.path.exists() |check if a file or directory really exists|

# directory manipulations

|  method |description   |
|---|---|
| os.getcwd()  | get current working directory  |
| os.chdir()  |  change current working directory |
| os.listdir()  | list sub-directory and files under a certain directory[^1]  |
| os.mkdir()  | create a new directory  |
| os.rmdir() |  remove an empty directory, it can not remove non-empty directory[^2] |
| os.remove()  | delete a file  |

# some attribute

There are also several use attribute. For better portability of your code, you
should considering using these attribute instead of hard code.

| attribute  |  description |
|---|---|
| os.linesep  | line break character  |
| os.sep  |  separator in a normal directory path name |
|  os.pathsep |  separator for the `PATH` environment variable|

# References

+ <http://www.cnblogs.com/feeland/p/4463682.html>
+ <https://pymotw.com/2/ospath/>

[^1]: note that this method only output sub-directory and file names, with no path info
[^2]: if you want to delete a non-empty directory, use [shutil.rmtree](https://docs.python.org/3/library/shutil.html#shutil.rmtree)
