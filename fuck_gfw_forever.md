---
title: "Cross the Wall"
date: 2021-01-10T17:49:28+08:00
draft: false
tags: []
categories: [Note]
---

To cross the fucking firewall, we need some handy tools.

<!--more-->

Two good clients for Mac:

+ [ClashX](https://github.com/yichengchen/clashX/releases)
+ [Qv2ray](https://github.com/Qv2ray/Qv2ray/releases)

# ClashX

## Add new subscription

ClashX is easy to set up. It supports subscription URL. To add a new
subscription, click `Config --> Remote Config --> Manage`, click `Add`. In the
popped up window, fill in the remote subscription URL, and give the config a
name in the field `Config Name`. Click `OK`.

## Check the proxy ports it provides

Click `Help --> Ports`, ClashX will show the HTTP and socks5 ports it provides.


# Qv2ray

It requires more setup than ClashX since it is more configurable. First, we
need to install Qv2ray.

## Set up v2ray core

To make it work for v2ray. We need also to install [v2ray core](https://github.com/v2fly/v2ray-core) ourselves since
v2ray core is not packaged. We can download the binary release from [its release page](https://github.com/v2fly/v2ray-core/releases).

Extract the compressed file. Then click `Preferences` and go to `Kernel Settings` page, set the `V2ray Core Executable Path` and `V2ray Assets Directory` (it contains the file `geoip.dat` and `geosite.dat`) to the file we just extracted.

On macOS, we can also use brew to install v2ray (`brew install v2ray`). The
v2ray executable is `/usr/local/bin/v2ray`, and its resource is in
`/usr/local/share/v2ray`.

Click the button `Check V2ray Core Settings`. If everything is fine, a popup window occurs, and tells you that:

> V2Ray path configuration check passed.

## Add a subscription URL

Click the `Show` menu, click `Groups` at the bottom. In the new Windows, click
the icon `Add new subscription` on the bottom left. In the `Subscription
Settings` page on the right, check the button `This group is a subscription`.
Fill in the subscription URL in the field `Subscription Address`, and click the
button `Update Subscription` to update the subscription.

Go back to the previous Window where all groups are listed. Double click the group we have added, it will show all the connections for this group. Click a connection, and right click to bring up the context menu. Then click `Connect to this`.

## Check the proxy ports

Click the `Preferences` menu, then click `Inbound Settings` in the Preferences window. In the `SOCKS Settings` and `HTTP Settings` part, we can set up the port to be used for SOCKS and HTTP.


# Ref

+ https://tlanyan.me/v2ray-clients-download/
+ https://qv2ray.net/getting-started/
