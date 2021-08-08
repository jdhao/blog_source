---
title: "Server Autologin with Expect"
date: 2018-10-08 23:01:24+0800
tags: [Expect, Cygwin, ssh, Windows]
categories: [Note]
---


I use a Windows 10 machine and need to connect to Linux servers. After using
several [ssh clients](https://www.slant.co/topics/149/~best-ssh-clients-for-windows)
for connecting to the server, I finally settled for
[Cygwin](https://www.cygwin.com/) and
[Wsltty](https://github.com/mintty/wsltty), which is open-sourced and use the
well-known [mintty terminal](https://github.com/mintty/mintty).

But one feature I miss from the SSH tools such as MobaXterm or Xshell is that
these tools can save the server IP, port, user name and even password, so that
you can log into the server with just a click.

With mintty terminal, I have to manually ssh to my server each time, which is
tedious and time consuming. Fortunately, I have found the
[Expect](https://en.wikipedia.org/wiki/Expect) scripting language which is
pretty powerful for this task.

<!--more-->

# Install

If you are using Cygwin, you can install Expect package via its setup program.
If you are using Ubuntu on Windows, you can install expect with apt-get:

```bash
sudo apt-get install -y expect
```

# Usage

## Simple case

Expect can execute command, receive input and send output automatically for
you. Suppose you want to connect to a server, you can use the following
script:

```
#!/usr/bin/expect

set timeout 10
spawn ssh -p 22 USER@SERVER_IP
expect "Password: "
send "PASSWORD\r"
```

You should change `USER`, `SERVER_IP` and `PASSWORD` properly. In the above
script, we first use `spawn` to execute the `ssh` command for us. Then we use
`expect` to capture the output of the terminal to see if it matches the
predefined string. Finally, we use `send` command to send the right password
and a newline character: it is just like you have typed your password and press
<kbd>Enter</kbd> manually in terminal.

You can save this script as `auto_login.exp` and execute it with

```bash
expect auto_login.exp
```

## Accept command line parameters

In the above script, the user name and password is fixed in the script. If you
have multiple user account in the server, it would be better that you can input
the user name and password dynamically from the command line. You can use the
following script for that:

```
#!/usr/bin/expect

set timeout 10
set user [lindex $argv 0]
set password [lindex $argv 1]

spawn ssh -p 22 $user@SERVER_IP
expect "Password: "
send "$password\r"
```

In the above script, [lindex](https://www.tcl.tk/man/tcl8.4/TclCmd/lindex.htm)
command is used to get the parameters supplied in the command line. To use a
variable, add a `$` before it to access its value. In the command line, you can
invoke the script like this:

```bash
expect auto_login.exp user_name some_password
```

# References

+ https://stackoverflow.com/questions/17059682/how-to-pass-argument-in-expect-through-command-line-in-shell-script
+ https://www.geeksforgeeks.org/expect-command-in-linux-with-examples/
