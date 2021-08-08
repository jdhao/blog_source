---
title: How to Use Putty on Windows
date: 2018-04-12 00:20:26 +0800
tags: [ssh, Windows, X11]
categories: [tools]
---

[Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/) is a powerful and
slim ssh tool with rich features. It is widely used to connect to the remote
servers. I will summarize some of its settings and usages in this post.

<!--more-->

# Use X server with putty

Putty supports X11 forwarding. To enable this feature, do the following steps:

+  Load a remote session which you want to use GUI application
+  Go to `connection->SSH->X11`, check the option `Enable X11 forwarding` (see image below)

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-2/98035913.jpg"
         title="Enable X11 forwarding"
         style="float: middle;">

+ Go back to `Session` settings and save the change.

After these settings, you should be able to use X11 forwarding. Of course, you
need to install a X server application, e.g.,
[xming](https://sourceforge.net/projects/xming/files/) and
[vcxsrv](https://sourceforge.net/projects/vcxsrv/files/).

# How to remember the username and password of session?

Short answer: remembering username is possible, but [remembering password is
impossible by deliberate
design](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-password-remember).

In order to set a default username for a session. First load the session. Then
go to `Connection->Data`. Fill the username in the text box on the right of
`Auto-login username` (see image below).

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-2/84417505.jpg"
         title="Remember username"
         style="float: middle;">

# How to save the settings for a session

This question has confused me for a while. Each time I set up a session, the
settings are only valid fro the current session. The next time I open the
session, I have to configure it again, which is annoying. The correct steps to
save your settings are as follows:

+ Choose a particular session from the session list and click `Load` button to
load it as the current session (the session name will appear in the text box
under `Saved Sessions`)

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-2/93260343.jpg"
         title="Load a session"
         style="float: middle;">

+ Change to other settings page and set up the session.
+ Go back to the `Session` page and click `Save` button to save your settings.

# How to start a session directly from the command line

First make sure that putty executable has been added to your system `PATH`.
Then use the following syntax:

```
putty -load "<session_name>" 
```

to open a session directly from command line. `<session_name>` must be a valid
session name.

---
# References

+ <http://en.tldp.org/HOWTO/XDMCP-HOWTO/ssh.html>
+ <http://serverfault.com/questions/12295/how-to-make-putty-settings-persistent>
+ <http://tartarus.org/~simon/putty-snapshots/htmldoc/Chapter4.html#config-saving>
+ <https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-startsess>
