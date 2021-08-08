---
title: "Convert Python Script to Exe on Windows with Pyinstaller"
date: 2019-10-14 21:46:55+0800
tags: [Windows]
categories: [Python]
---

# Introduction #

I have a use case where I want to convert my Python script to standalone
executable so that I do not need to type `python` before it to run the script.
On Linux, it is easy to achieve with the help of
[shebang](https://stackoverflow.com/questions/6908143/should-i-put-shebang-in-python-scripts-and-what-form-should-it-take).
However, on Windows, it does not work. Then I thought I might convert the
script to Windows executable.

<!--more-->

We can use [pyinstaller](https://www.pyinstaller.org/) to convert python script
to Windows executable files. You can install it with pip:

```
pip install pyinstaller
```

# How to use pyinstaller

The most simple way to run pyinstaller is to invoke it without options:

```
pyinstaller my_script.py
```

It will create a `dist/my_script` directory where the generated executable
resides with other DLL files. This executable file can not run alone: it must
rely on other DLL files.

To create a standalone executable, we can use `--onefile` option.

To build an executable which runs on command line and does not need a GUI
windows, we can use the `--console` option.

We can then generate the executable with the following command:

```
pyinstaller --onefile --console my_script.py
```

The generated executable will be `dist/my_script.exe`.

# Reduce the size of executable

One problem is that the generated executable file is huge even if the script
only contains a few lines of code.

## Create a virtual environment ##

According to [post here](https://stackoverflow.com/a/47810133/6064933), to
reduce the size of the produced executable, we can create a clean virtual
environment where only packages required for our script are installed.

In my case, after building the executable inside a virtual env, I have reduced
the executable size from more than 200Mb to only 6Mb!

## Using UPX ##

An additional way to reduce executable size is to use
[UPX](https://upx.github.io/) to compress files. You can download the UPX
windows release file and extract it to a folder. When running the pyinstaller,
you can use `--upx-dir` to include UPX support:

```
pyinstall --onefile --console --upx-dir=/path/to/upx-folder my_script.py
```

In the above command, you only need to specify the path of the directory
containing `upx.exe`.

However, certain DLL files which is need by the executable should not be
compressed by UPX. If they are compressed, you may encounter errors when you
run the produced executable:

```
Error loading Python DLL 'C:\Users\ADMINI~1\AppData\Local\Temp\_MEI45442\python36.dll'.
LoadLibrary: The parameter is incorrect.
```

Those DLLs failing to load should not be compressed by UPX. You can add
`--upx-exclude` option to pyinstaller when building the executable, and this
options can be used multiple times to exclude several files. For example:

```
pyinstaller.exe --onefile --console --upx-dir=upx-3.95-win64\upx-3.95-win64\ --upx-exclude=python36.dll --upx-exclude=vcruntime140.dll my_script.py
```

After that, the executable file can run without errors.

# References #

+ [Reduce executable size](https://github.com/pyinstaller/pyinstaller/issues/1694#issuecomment-384040050).
+ [How to use upx with pyinstaller](https://stackoverflow.com/questions/47730240/how-do-i-use-upx-with-pyinstaller).
+ [Error loading pythonxx.dll](https://github.com/pyinstaller/pyinstaller/issues/3600#issuecomment-433895981).
