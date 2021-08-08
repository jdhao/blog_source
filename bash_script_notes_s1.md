---
title: "Bash Script Note 1"
date: 2020-04-27 22:00:48+0800
tags: [Bash]
categories: [Linux]
---

# Writing robust script

We should use the following options in our bash script for better quality:

<!--more-->

```bash
set -eux
set -o pipefail
```

Meaning of these option:

+ `-e`: Exit immediately if a command fails.
+ `-u`: If a variable is not set, this is considered an error[^1].
+ `-x`: Expand variables in a command and print it before executing the command (better for debugging if command fails).
+ `-o pipefail`: If any command in a pipe fails, this pipe will fail.

For more options, see [bash set command doc](https://ss64.com/bash/set.html).

# common check conditions for files

Some commonly used options for checking file conditions:

| option          | meaning                                   |
|-----------------|-------------------------------------------|
| `[ -d FILE ]`   | True if FILE exists and is a directory    |
| `[ -f FILE ]`   | True if FILE exists and is a regular file |
| `[ -r FILE ]`   | True if FILE exists and is readable       |
| `[ -w FILE ]`   | True if FILE exists and is writable       |
| `[ -x FILE ]`   | True if FILE exists and is executable     |
| `[ -z STRING ]` | True if length of STRING is zero          |
| `[ -n STRING ]` | True if length of STRING is not zero      |

Note that to check if the opposite is true, use `[ ! condition ]`.

# Use `[ ]` or `[[ ]]` for condition check?

Both `[ ]` and `[[ ]]` can be used to check some conditions, but `[ ]` is POSIX
compatible, and `[[ ]]` is not. Both bash, zsh support `[[ ]]`.

`[[ ]]` is also more versatile and powerful than `[ ]`. For example, it
supports `&&`, `||` and grouping command using `()` etc.

# Compare string equality

We can use `=` and `!=` to compare the equality of strings. Use of `==` is
non-standard (see [here](https://stackoverflow.com/a/2237103/6064933)).

# How to set boolean variable?

In bash, there is not really a boolean type. We can just use literal string
`true` to set a bool variable and check if variable is equal to string `true`.
For example:

```bash
use_python=true
if [[ $use_python = true ]]; then
    # do something
else:
    # do another thing
fi
```

# How to check if a command exists?

We can use `command -v YOUCOMMAND` to check if `YOURCOMMAND` exists. For
example:

```bash
if [[ ! "$(command -v rg)" ]]; then
    # install ripgrep
else
    echo "ripgrep already exists. No action needed."
fi
```

According to [here](https://stackoverflow.com/a/677212/6064933), `which`
command is not a reliable way to check if a command exists since it may return
an exit code of 0 even if no command is found.

# References

+ [Introduction to bash if](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_07_01.html).
+ [More advanced if usage](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_07_02.html).
+ [Difference between `[` and `[[`](http://mywiki.wooledge.org/BashFAQ/031).
+ [Check if a command exists in bash script](https://stackoverflow.com/a/677212/6064933).
+ [How to declare boolean variable](https://stackoverflow.com/q/2953646/6064933).

[^1]: you will see `unbound variable` error message.
