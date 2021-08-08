---
title: "pip bad interpreter issue"
date: 2020-07-29 23:23:45+0800
tags: [pip, Anaconda]
categories: [Python]
---

I am using pip installed via [minconda](https://docs.conda.io/en/latest/miniconda.html). I installed it
under `~/miniconda3` and then move the installation directory to
`~/tools/miniconda3`. After that, I changed the `PATH` accordingly. However,
when I use pip, I am seeing the following error:

<!--more-->

> /home/jdhao/miniconda3/bin/python:  bad interpreter: No such file or directory

this is because the executable `pip` under `~/tools/miniconda3/bin/` is
actually a Python source file. Its first line is a [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)):

> #!/home/jdhao/miniconda3/bin/python

and it still points to the old Python that is no longer there. Hence, the error
message.

A simple solution is to remove miniconda3 directory and install it again.

Ref:

+ https://docs.anaconda.com/anaconda/user-guide/tasks/move-directory/
