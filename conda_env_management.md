---
title: "Virtual Environment Management with Conda"
date: 2020-06-11 00:10:43+0800
tags: [Anaconda]
categories: [Python]
---

[Conda](https://docs.conda.io/projects/conda/en/latest/) is the default package
and environment management tool shipped with
[Anaconda](https://www.anaconda.com/products/individual).

This post logs my cheatsheet and notes for working with virtual environments
with conda.

<!--more-->

# commonly used commands for virtual env

+ Create a new environment: `conda create -n my_env`
+ List environments on the system: `conda env list`
+ Delete an environment: `conda env remove -n my_env`
+ Activate an env:
    + Windows: `conda activate my_env`
    + Mac/Linux: `source activate my_env`
+ Exit/quit an env:
    + Windows: `conda deactivate my_env`
    + Mac/Linux: `source deactivate my_env`

# How to install packages into a specific environment?

Outside an env, we can use the `-n` option to specify which environment we want
to install packages into:

```bash
conda install -n my_env -c some_channel some_package
```

If we are inside the virtual environment, there is no need to use the `-n`
option. We can install a package with `conda install`.

# Remove package in an env

Remove pakcage from an env: `conda remove -n my_env some_package`. If you do
not specify `-n`, `some_package` in current env will be removed. If you are not
in any virtual env, `some_package` in the global Python path will be removed.

# Using pip inside env install packages to global site-package location?

When I am inside a virtual env, I am surprised that `pip install some_package`
installs the package to the global site-package instead of inside the virtual
env.

After some digging, I find that reason is that I was not using the pip command
inside that virtual environment. To check this, use the following command
before using `pip install`:

```
which pip
```

If the pip path is not inside the virtual env, then we are using the
system-wide pip. So `pip install some_package` will install `some_package` into
to global Python path instead of the virtual environment.

# References

+ [Where to install pip packages inside my Conda environment](https://stackoverflow.com/a/54715875/6064933)?
+ [How to add package to conda environment without pip](https://stackoverflow.com/q/33680946/6064933)?
