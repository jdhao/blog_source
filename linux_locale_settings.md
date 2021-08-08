---
title: "Setting Up Locale in Linux"
date: 2019-09-27 23:56:47+0800
tags: [CentOS, Ubuntu, locale, Docker]
categories: [Linux]
---

# Introduction #

When I logged into a Ubuntu server, the system interface and messages are all
shown in Chinese. I want them to be displayed in English. After some searching,
I found out that we can change the [locale](https://en.wikipedia.org/wiki/Locale_(computer_software)) settings.

<!--more-->

The definition of locale is:

> In [computing](https://en.wikipedia.org/wiki/Computing "Computing"), a
> **locale** is a set of [parameters](https://en.wikipedia.org/wiki/Parameter)
> "Parameter") that defines the user's language, region and any special variant
> preferences that the user wants to see in their [user interface](https://en.wikipedia.org/wiki/User_interface)

# Set up locale for normal Linux system

In Linux systems, there are several environment variables related to the locale
settings, such as `LANG`, `LC_COLLATE`, `LC_TIME`, `LC_ALL` etc. A valid locale
is like `en_US.UTF-8`. You can check
[this documentation](https://wiki.archlinux.org/index.php/locale#Generating_locales)
on how locale is formed.

To see your default settings, use the `locale` command without any option. The
output looks like:

```bash
LANG=en_US.UTF-8
LANGUAGE=zh_CN:zh
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
```

These settings are often pre-set by the system root. On CentOS, the system-wide
default locale settings are stored in `/ect/locale.conf`. On Ubuntu, the system
locale settings are store in `/etc/default/locale`.

Among the locale env variables, `LC_xxx` variables except `LC_ALL` are ordinary
environment variables that can be set individually.

Special variables are `LANG`, `LANGUAGE` and `LC_ALL`. Their priority are
listed [here](https://www.gnu.org/software/gettext/manual/html_node/Locale-Environment-Variables.html#Locale-Environment-Variables):

1. `LANGUAGE`
2. `LC_ALL`
3. `LC_xxx`, according to selected locale category:  `LC_CTYPE`,  `LC_NUMERIC`,  `LC_TIME`,  `LC_COLLATE`,  `LC_MONETARY`,  `LC_MESSAGES`, ...
4. `LANG`

`LANG` is a special variable that will set the `LC_*` variable if it is not
set. `LC_ALL` will set all other `LC_XXX` variables to the same value. Usually,
you do not need to set up `LC_ALL` in your bash_profile. `LANGUAGE` is a
variable used for message handling, which has a higher priority than `LC_ALL`,
`LC_xxx` and `LANG` for messages (see the link [here](https: //www.gnu.org/software/gettext/manual/html_node/The-LANGUAGE-variable.html#The-LANGUAGE-variable))
. To verify this, suppose that we have the following settings inside
`.bash_profile`:

```bash
export LANG=en_US.utf8
export LANGUAGE=zh_CN.utf8
```

After sourcing the file, you will find that the messages given for command
output will be in Chinese (i.e., `LANG` is overridden.)

# Set up locale for docker container #

## Ubuntu docker

I try to build a Ubuntu docker image. I want to set my locale to `en_US.UTF-8`
inside the container by simply adding the following ENV instruction:

```
ENV LC_ALL=en_US.UTF-8
```

When I run the built image, I get the following warning:

> bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)

The `locale` command inside container gives the following message:

```
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_MESSAGES to default locale: No such file or directory
locale: Cannot set LC_ALL to default locale: No such file or directory
LANG=
LANGUAGE=
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=en_US.UTF-8
```

It seems that the locale file `en_US.UTF-8` does not exist inside container.
After some searching, I found the correct way to set up locales inside the
Ubuntu container. First, we need to install the `locales` package, which
provides the `locale-gen` command:

```
RUN apt-get update && apt-get install -y locales
```

After installing the `locales` packages, a file named `local.gen` will be
created under `/etc`. According to the documentation of `locale-gen`:

> locale-gen is a program that reads the file /etc/locale.gen and invokes
> localedef for the chosen localisation profiles.  Run locale-gen after you
> have modified the /etc/locale.gen file.

We should uncomment the line `# en_US.UTF-8 UTF-8` in `locale.gen` and run
`locale-gen` command to generate the needed locale file. After that we can set
related settings to `en_US.UTF-8`:

```
RUN sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && \
    locale-gen
ENV LC_ALL en_US.UTF-8
```

We can also directly generate the locale file by appending the locale as the
argument to `locale-gen`:

```
RUN locale-gen en_US.UTF-8
```

Putting this all together, we may use the following instructions inside
Dockerfile:

```
RUN apt-get update && apt-get install -y locales && locale-gen en_US.UTF-8
```

## CentOS docker

If you tried to build an image based on a CentOS image. You need to use
`localedef` to generate locale files instead of `locale-gen`:

```
# -i: specify the locale definition file
# -f: specify the character set
RUN localedef -i en_US -f UTF-8 en_US.UTF-8
```

Other settings are similar to that of Ubuntu.

# References

- [Change locale in CentOS 7](https://www.osetc.com/en/centos-7-rhel-7-change-the-system-locale.html)
- [https://help.ubuntu.com/community/Locale](https://help.ubuntu.com/community/Locale)
- [The meaning of LANGUAGE variable](https://superuser.com/q/412288/736190)
+ [Set up locale inside Ubuntu docker](https://stackoverflow.com/a/28406007/6064933).
+ [Set up locale in Debian and Ubuntu](https://unix.stackexchange.com/a/247019/221410).
+ [CentOS: set up locale](https://unix.stackexchange.com/q/140299/221410).
