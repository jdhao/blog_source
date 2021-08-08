---
title: "Cmder Advanced Configurations"
date: 2019-02-03 18:16:51+0800
tags: [Cmder, Windows]
categories: [tools]
---

In this post, I summarize some of the advanced configurations of
[Cmder](https://jdhao.github.io/2017/12/29/cmder-usage/).

<!--more-->

# How to set up the default startup directory for a task?

Open Cmder settings and go to `Startup --> Tasks` and choose a task
(`cmd::Cmder` or `Cmd::Cmder as Admin`, etc.). Click `Startup dir...` on the
bottom right of the page and choose the appropriate directory. Restart Cmder
and you should be able to see that Cmder start up in the given directory.

But, if you have set up startup directory this, [`cmder here` feature will not
any more](https://github.com/cmderdev/cmder/issues/1735): Cmder will always
open the directory you have set up.

# Add custom color scheme to Cmder

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200222141814.png" width="600">
</p>

There is a project
[ConEmu-Color-Themes](https://github.com/joonro/ConEmu-Color-Themes) which
provides a few color schemes for ConEmu, on which Cmder is based.

First, we need to clone this project to our local machine:

```
git clone https://github.com/joonro/ConEmu-Color-Themes
cd ConEmu-Color-Themes
```

This project provides a powershell script `Install-ConEmuTheme.ps1` to simplify
the installation of color themes. To run this script, we need to give it
execution permission:

+ Open powershell and run it as administrator
+ Run the following command inside powershell:

```
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Force
```

Next, in order to avoid any errors, [export your current Cmder
settings](https://superuser.com/q/450144/736190) to a custom directory.

Then, run the following command in Cmder:

```
powershell ./Install-ConEmuTheme.ps1 -ConfigPath /path/to/exported/setting/ConEmu.xml -Operation Add -ThemePathOrName themes\moe-dark.xml
```

It will add the theme `moe-dark` to the theme list. Be careful with the space
in path. If a path has space in it, you will encounter
`ParameterArgumentValidationError` exception. The solution is to enclose the
path with **single quote**.

Finally, open Cmder setting and import the new setting file from the custom
directory. You should be able to see the new color schemes.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200222142040.png">
</p>

# Creating an alias in Cmder

Sometimes, we need to type a command over and over again. It is better to
create a shorter alias for it. A great example is [changing to directories on
other drives on Windows](https://superuser.com/q/135214/736190). Due to
histortical reasons, the `cd` command can only go to directories on the current
drive by default. To go to directories on other drives, we need the `/d`
parameters. Having to use `cd /d` each time is cumbersome.

If you expect `cd` to go to another directory no matter whether it is on the
current drive or another drive, you can create an alias for it. Here is how to
do it in Cmder:

+ Go to `$CMDER_ROOT/config` and open the file `user_aliaes.cmd`
+ Add the following to the end of the file:
    ```
    cd=cd /d $*
    ```

Another way is to open cmder settings (`Win+ Alt + P`), go to `Startup -->
Environment`, and add the following alias for cd:

```
cd=cd /d $*
```

Restart Cmder and you should be able to cd to any directory you want! It is a
small trick, but it works great and saves your time.

# References

+ [Dealing with space in powershell script parameters](https://stackoverflow.com/a/52607756/6064933).
+ [Enable the execetuion of powershell script](https://superuser.com/q/106360/736190).
+ [Microsoft official doc on `Set-ExecutionPolicy`](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6).
+ [doskey documentation](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-xp/bb490894(v=technet.10)).
+ [How to go to directory on another drive on Windows](https://stackoverflow.com/q/11065421/6064933).
+ [Add custom alias in Cmder](https://github.com/cmderdev/cmder/issues/1432#issuecomment-456963455).
+ [Add custom alias in Cmder](https://github.com/cmderdev/cmder/issues/421#issuecomment-414817034).
+ [Make alias which accept parameters](https://stackoverflow.com/q/36799737/6064933).
+ [Change startup directory](https://superuser.com/q/1135129/736190)
+ https://superuser.com/q/556248/736190
+ https://conemu.github.io/en/SettingsEnvironment.html#aliases
