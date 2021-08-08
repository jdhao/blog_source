---
title: "Set up X11 Forwarding with Git for Windows (git-bash)"
date: 2018-03-02 00:05:00 +0800
tags: [Windows, ssh, X11]
categories: [technique]
---

[As I have posted earlier](https://jdhao.github.io/2017/12/29/cmder-usage/),
Cmder is a better option if you use Windows command line frequently. The full
installation of Cmder comes with [*Git for Windows*](https://gitforwindows.org/), which is handy to provide a Linux-like
environment. In this post, I will write about how to use Git for Windows to
connect to a remote CentOS server and set up X11 forwarding to use the GUI
applications on that server.

<!--more-->

# Enable X11 forwarding on your CentOS server

First, you need to make sure that your server support X11 forwarding. Edit the
file `/etc/sshd_config` and use the following settings:

```csv
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost no
```

If you are using CentOS 7, you should use

```bash
systemctl restart sshd.service
```

to restart the sshd service. If you are on CentOS 6, you should use the
following command instead:

```bash
service sshd restart
```

# Install X server program on Windows

In order to use X11 forwarding, you also need to install a X server on your
Windows computer. You can choose to install
[xming](https://sourceforge.net/projects/xming/files/) or [vcxsrv](https://sourceforge.net/projects/vcxsrv/files/). Vcxsrv is better
since it is updated frequently and is free, while latest version of xming
requires a license. There is [a good
post](https://bbs.nextthing.co/t/need-an-x11-server-for-win7-or-above-vcxsrv-is-based-on-xorg/2882) that introduces vcxsrv.

In this post, we stick to vcxsrv. Install the application, and you will see two
program: `xlaunch` and `vcxsrv`. Xlaunch is used to configure the X server and
vcxsrv is used to start the server. Open xlaunch and use the default settings.
Then start vcxsrv. The program will run in the background. Go to the task bar
and point to program, you will see information like the following:

> JD-PC:0.0 - 0 clients

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-2/60729814.jpg"
          title="vcxsrv program"
          style="float: middle;">

# Configure ssh

After starting the X server, we need to configure the ssh.

First, open git-bash, change directory to `$HOME` and create `.bash_profile`,
write following options in the `.bash_profile`

```bash
export DISPLAY=localhost:0.0
```

Save the file and restart git-bash.

Second, connect to your remote server using the following command:

```bash
ssh -X -v <user>@<server_addresss>
```

The option `-v` is used to diagnose the X connection problems.

## Trouble shotting

After logging into your server, you may notice a warning message during login:

>Warning: untrusted X11 forwarding setup failed: xauth key data not generated

If you try to start a GUI program, e.g., `xclock`, you may see an error message:

>Error: Can't open display:

If that is the case, close the session and try to use the following command to
connect to your server instead:

```bash
ssh -Y -v <user>@<server_addresss>
```

Rerun the `xclock` program with `xclock &`. It should works fine.

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-2/92277952.jpg"
         title="xclock"
         style="float: middle;">

We can run multiple GUI applications, for example `xclock &`, `xeyes &`. If you
point to vcxsrv icon in the task bar, you will see that now it has 2 clients:

>JD-PC:0.0 - 2 clients

---

# References

+ <https://stackoverflow.com/questions/10129267/x-session-in-git-bash-on-windows>
+ <https://serverfault.com/questions/273847/what-does-warning-untrusted-x11-forwarding-setup-failed-xauth-key-data-not-ge>
