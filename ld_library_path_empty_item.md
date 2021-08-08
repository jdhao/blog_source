---
title: "Empty Entry in LD_LIBRARY_PATH May Lead to Security Issues"
date: 2021-07-03T00:44:08+08:00
draft: false
tags: []
categories: [Linux]
---

When I want to build glibc using the provided configure script (`./configure --prefix=xxx`),
I saw the following error message:

<!--more-->

> configure: error:
> *** LD_LIBRARY_PATH shouldn't contain the current directory when
> *** building glibc. Please change the environment variable
> *** and run configure again.

How could my `LD_LIBRARY_PATH` contains the current directory? Upon inspecting
the env variable, I see a spurious `::` inside the value of `LD_LIBRARY_PATH`,
which means an  empty item. An empty item is interpreted by `ld` as the current
working directory. As a result, as a result, `ld` may load library from the
current working directory, causing unintended effects, or even security
vulnerability if an attacker puts some harmful library in the current
directory.

The `::` in my `LD_LIBRARY_PATH` is caused by the initial empty `LD_LIBRARY_PATH`.
For example, if we use `export LD_LIBRARY_PATH=/home/xxx/local/lib:
$LD_LIBRARY_PATH` while `LD_LIBRARY_PATH` is empty, it actually becomes
`/home/xxx/local/lib:`.

To fix this issue, we need to check if the value of `LD_LIBRARY_PATH` is empty
before using it:

```bash
if [-z $LD_LIBRARY_PATH]; then
  export LD_LIBRARY_PATH=/home/xxx/local/lib
else
  export LD_LIBRARY_PATH=/home/xxx/local/lib:$LD_LIBRARY_PATH
fi
```

# References

+ https://github.com/rust-lang/cargo/issues/5796
+ https://bugzilla.redhat.com/show_bug.cgi?id=638365
+ https://www.openwall.com/lists/oss-security/2010/09/29/1
+ https://joshumax.github.io/general/2017/06/08/how-torch-broke-ls.html
+ https://github.com/Linuxbrew/legacy-linuxbrew/issues/807
