---
title: "Creating Beautiful Bash Prompts"
date: 2021-03-31T23:00:26+08:00
draft: false
tags: [Bash, shell]
categories: [Linux]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20210331231139.jpg" width="400">
</p>

This post summarize how to change the Bash shell prompt to a better look. The
end result is shown in the title image.

<!--more-->

# Bash Special escape sequence

To change the bash prompt, we can directly change `PS1` variable. Some special
escape sequences[^1] are predefined by Bash to have special meaning, e.g.,
`\u` means the current user name.

Set `PS1` to `[\u@\h \w]` and start bash, the prompt looks like the following:

```
[jdhao@jdhao-MBP ~/Projects/trial_error]
```

# Using colors

Colors are important for us to create beautiful prompts. For example, `echo -e
"\033[4;32mtest\033[0m"` will print green `test` with underline effect. In this
command, `\033` is the octal representation of `ESCAPE` control character,
which starts an [ANSI escape code](https://en.wikipedia.org/wiki/ANSI_escape_code) to control the terminal behavior. You
can also use `\x1b`, which is the hex value of ESCAPE control character.
See [this post](https://misc.flogisoft.com/bash/tip_colors_and_formatting) on how to use colors in terminal.

A little trick I learned is to define a color for easier reuse:

```
GREEN="\e[0;32m"
COLOR_NONE="\e[0m"
```

When we want to use a color, we can now use the new name instead of the lengthy
and obscure escape code.

```bash
GREEN="\e[0;32m"
COLOR_NONE="\e[0m"

printf "${GREEN}Green text${COLOR_NONE}"
```

# Putting all together -- the use of PROMPT_COMMAND

We can of course write all the logic to set the `PS1` variable in one command,
but it could be messy and unreadable. Instead, we can set `PS1` via the env
variable [`PROMPT_COMMAND`](https://stackoverflow.com/a/3058366/6064933). The
value of `PROMPT_COMMAND` will be executed by Bash as command each time before
Bash tries to show the actual prompt.

The following Bash script shows my final working prompt configuration:

```bash
GRAY="\e[2;37m"
GREEN="\e[0;32m"
PURPLE="\e[1;35m"
COLOR_NONE="\e[0m"

# Detect whether the current directory is a git repository.
function is_git_repository() {
  git branch > /dev/null 2>&1
}

function set_git_branch () {
    # Note that for new repo without commit, git rev-parse --abbrev-ref HEAD
    # will error out.
    if git rev-parse --abbrev-ref HEAD > /dev/null 2>&1; then
        BRANCH=$(git rev-parse --abbrev-ref HEAD)
    else
        BRANCH="bare repo!"
    fi
}

function set_bash_prompt () {

    if is_git_repository; then
        set_git_branch
    else
        BRANCH=''
    fi

    PS1=""
    # set up user and host
    PS1+="${GRAY}\u@\h${COLOR_NONE} "
    # set up working directory
    PS1+="${GREEN}\w${COLOR_NONE} "
    # set up git branch
    PS1+="${GRAY}${BRANCH}${COLOR_NONE}\n"
    # set up prompt character
    PS1+="${PURPLE}>${COLOR_NONE} "
}

export PROMPT_COMMAND=set_bash_prompt
```

Ref:

+ https://wiki.archlinux.org/index.php/Bash/Prompt_customization#Prompts
+ [List of ANSI color escape sequences](https://stackoverflow.com/q/4842424/6064933)
+ Pretty Colored Bash Prompt: https://gist.github.com/milyord/4108547
+ Git current git branch name: https://stackoverflow.com/q/6245570/6064933
+ Check if we are in git repo: https://stackoverflow.com/q/2180270/6064933

[^1]: A list of escape sequences and their meanings can be found [here](https://www.gnu.org/software/bash/manual/html_node/Controlling-the-Prompt.html).
