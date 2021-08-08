---
title: "CentOS 7 系统安装 Shadowsocks 并且开启多用户模式指南"
date: 2017-08-06 20:38:00 +0800
tags: [CentOS]
categories: [Linux]
---

据说墙升级了，本来用得很流畅的服务也不行了，时不时断掉，让人很恼火。有必要升级一下梯子了，于是我也研究了一下利用 VPS 自建梯子的方法。本文总结一下安装中遇到的问题和解决办法，只针对 CentOS 7 系统，其他 Linux 系统配置方法是类似的。

<!--more-->

关于 Shadowsocks 原理，网上有很多资源，这里不再赘述。要使用 Shadowsocks，首先要有可以访问外网的服务器，配置好服务器（主要是配置 Shadowsocks 的服务端），然后下载对应于不同平台的客户端，对应配置，就可以访问谷歌了。因此第一步，我们需要购买一台境外的 VPS （虚拟服务器）。

# VPS 选择与 操作系统安装

VPS 提供商有很多，常见的有 [Vultr](https://www.vultr.com/)，[Linode](https://www.linode.com/)，[Digital ocean](https://www.digitalocean.com/)，[Bandwagonhost](https://bandwagonhost.com/index.php)。这些 VPS 提供商的网站可能会上不去，因此注册之前最好确保自己有一款免费的爬墙梯子，例如赛风，可以向[这个地址](mailto:get@psiphon3.com)发邮件索要下载地址；或者[蓝灯](https://github.com/getlantern/lantern-binaries)。

在这几个 VPS 提供商中，Vultr 和 Bandwagonhost 比较实惠，Vultr 有一个最便宜的2.5 美元一个月的套餐，很抢手，经常断货，Bandwagon 有一个 19.99 美元包年的套餐，也非常不错。如果选用 Vultr，支付的话可以使用 paypal，paypal 可以添加国内的银联卡，比较方便。使用 Bandwagonhost 支付更方便，直接支持支付宝付款。一般来说， VPS都会提供不同的操作系统供用户选择，例如 CentOS，Ubuntu，FreeBSD 等等。我对CentOS 非常熟悉，所以就选了 CentOS 系统，Vultr 提供的是 CentOS 7 系统，跟CentOS 6 的有一定的区别，需要注意。Bandwagonhost 默认提供的是 CentOS 6 的系统，如果你需要安装 CentOS 7 的系统，需要自己重装系统（很方便，大约几分钟就能搞定）。

## Bandwagonhost 购买及使用

Bandwagonhost 注册成功以后，可以在注册选择自己想要购买的虚拟服务器，然后按照提示操作购买就可以，服务器 location 可以选择 Los Angeles，支付的话选择 Alipay 直接用支付宝付款。

服务器购买以后，点击右上角「Client Area」，

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-18/13424264.jpg"
         title=""
         style="float: middle;">

然后点击「Services」,选择「My Services」，会出现购买的服务器的信息。

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-18/89016061.jpg"
         title="查看购买服务器"
         style="float: middle;">

在出现的页面，会显示订购的服务器的信息，如下图所示

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-18/34776737.jpg"
         title="服务器信息"
         style="float: middle;">

点击「KiwiVM Control Panel」，会进入服务器的控制与操作界面，在这里可以对订购的虚拟服务器进行各种操作，例如修改密码，重装系统，输入简单或者复杂的指令等等，见下图。

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-5-18/69721207.jpg"
         title="服务器设置与操作界面"
         style="float: middle;">

Bandwagonhost 默认安装的是 CentOS 6 系统，如果需要其他系统可以自己重新安装。另外，CentOS 6 系统可以直接一键安装 shadowsocks 服务，简直是为我国人民量身定做。

从短暂的使用上看，Vultr 和 Bandwagonhost 使用起来速度都可以，用 ssh 客户端链接服务器的时候，都不是很稳定，输入命令或者使用 Vim 编辑文本的时候经常没有反应，卡死，我不得不经常重新打开一个 session。Bandwagonhost 修改 firewalld 服务以后，使用 ssh 客户端一段时间内无法连接服务器，不知道什么原因，Vultr 没有出现这种情况。

另外，如果要使用 Vultr 服务，可以[点击此链接注册](https://www.vultr.com/?ref=7197850)，据说会有优惠。Bandwagonhost 一些网站也有优惠码，购买之前可以在网上搜索一下，毕竟都是按照美刀结算的，苍蝇腿也是肉啊，我的推荐链接为 <https://bandwagonhost.com/aff.php?aff=27252>。

# SS 服务端的安装及配置

## 一键式安装

SS 服务的安装，主要参考了[这篇博文](https://www.diycode.cc/topics/738)，傻瓜式安装，对 Linux 系统熟悉的人应该都不会有问题。使用下面的命令安装：

```bash
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```

有四种 SS 服务端可以安装，推荐选择第一个：shadowsocks-python。然后直接按照提示一步一步往下就可以安装了。安装成功以后，会有提示，告诉你服务器的地址，端口号，以及密码。安装成功的提示大概类似于下面：

```
Congratulations, your_shadowsocks_version install completed!
Your Server IP        :your_server_ip
Your Server Port      :your_server_port
Your Password         :your_password
Your Encryption Method:your_encryption_method

Your QR Code: (For Shadowsocks Windows, OSX, Android and iOS clients)
 ss://your_encryption_method:your_password@your_server_ip:your_server_port
Your QR Code has been saved as a PNG file path:
 your_path.png

Welcome to visit:https://teddysun.com/486.html
Enjoy it!
```

使用这些信息就可以在自己客户端使用 ss 服务了，上面的链接有详细操作。

安装好以后，可执行程序位于 `/etc/init.d/` 目录,名称为 `shadowsocks-python`，该程序有四个选项，

```bash
/etc/init.d/shadowsocks-python start #启动
/etc/init.d/shadowsocks-python stop  #停止
/etc/init.d/shadowsocks-python restart #重启
/etc/init.d/shadowsocks-python status #当前状态
```

`/etc/shadowsocks-python` 目录下是服务端的配置文件 `config.json`，如果要对服务端配置进行更改，例如更改密码以及端口号，可以直接修改该文件。修改完文件以后，该配置并不能生效，需要自己重启 shadowsocks 服务：直接使用`/etc/init.d/shadowsocks-python restart` 命令重启服务。

## 开启多用户模式

Vultr 以及 Bandwagonhost 的套餐提供了每月提供了大量的流量，500G 到 1000 G 不等，足够多人使用，根据[这里](https://github.com/shadowsocks/shadowsocks/wiki/Configure-Multiple-Users) 的说明，目前只有 Python 以及 Go 服务端可以开启多用户模式，可以通过修改`config.json` 文件来实现多用户模式(不要忘记在更改后重启 shadowsocks 服务)。一个示例多用户配置如下：

```json
{
    "server":"0.0.0.0",
    "local_address":"127.0.0.1",
    "local_port":1080,
    "port_password":{
     "4278":"fuckgfw1",
     "4279":"fuckgfw2",
     "4280":"fuckgfw3",
     "4281":"fuckgfw4"
    },
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open":false
}
```

请严格遵循 json 文件的格式，除了最后一个选项，每个选项后面都需要一个逗号。

按照网上的教程，更改 json 文件，重启 shadowsocks 服务，发现新增加的端口在客户端仍连接不上。经过查找，发现原因是新增加的端口被系统的 firewall 服务屏蔽了，需要把新增加的端口加入白名单，参考[这里](https://github.com/shadowsocks/shadowsocks-go/issues/135#issuecomment-286698704)给出的解决办法，如果新增加的端口是 1234，那么采用以下命令开启该端口，

```bash
firewall-cmd --permanent --zone=public --add-port=1234/tcp
firewall-cmd --permanent --zone=public --add-port=1234/udp
systemctl restart firewalld
```

`firewalld` 服务的重启是必要的，否则在客户端仍然无法使用该端口连接 SS 服务。经过以上操作，新增加的端口就可以正常连接了。

## 定时检查 SS 服务是否正常运行

由于你懂的原因，SS 服务并不稳定，可能会断掉。断掉以后，我立马采用另外一个梯子去Bandwagonhost 官网看了一下我的帐号，发现 SS 服务自动关闭了，于是自己手动重启了一下。上网搜了一下，可以采用脚本检测 SS 服务进程是否还存在，如果不存在，则自动重启服务，利用 Linux 系统的 [cron](https://en.wikipedia.org/wiki/Cron)来定时执行脚本。有人已经写好了脚本，我们可以直接下载使用，使用以下命令

```bash
wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-crond.sh
chmod +x shadowsocks-crond.sh
```

然后需要确定系统的 crond 服务处于运行状态，使用 `systemctl status crond.service`，示例输出如下:

```
● crond.service - Command Scheduler
   Loaded: loaded (/usr/lib/systemd/system/crond.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2017-06-04 21:19:23 EDT; 2 months 21 days ago
 Main PID: 1638 (crond)
   Memory: 164.0K
   CGroup: /system.slice/crond.service
           └─1638 /usr/sbin/crond -n
```

如果该项服务被关闭了，直接使用 `systemctl restart crond.service` 重启服务即可。然后编辑文件 `/etc/crontab`，在文件最后加上一行，

```
*/5 * * * * root bash /root/shadowsocks-crond.sh
```

以上命令表示每 5 分钟执行一次脚本 `/root/shadowsocks-crond.sh`，当然也可以根据自己的实际情况指定时间，譬如十分钟检查一次，保证服务器断掉之后可以尽快恢复。

# Shadowsocks 客户端安装

配置好服务端以后，我们需要配置客户端才能使用 Shadowsocks。Shadowsocks 的客户端涵盖主流平台，[Windows PC](https://github.com/shadowsocks/shadowsocks-windows/releases)，[Linux](https://github.com/shadowsocks/shadowsocks-qt5/wiki/Installation#gnulinux)，[MAC](https://github.com/shadowsocks/ShadowsocksX-NG/releases)，[Android](https://github.com/Jigsaw-Code/outline-client/releases/tag/daily-2018-05-17)，[iOS](https://github.com/Jigsaw-Code/outline-client/releases/tag/daily-2018-05-17)都有对应的客户端。下载以后，对应自己在服务器端的设置，配置客户端即可，就可以顺利访问谷歌了。Happy Googling~

（全文完）

---

# 参考

+ <https://www.liquidweb.com/kb/how-to-start-and-enable-firewalld-on-centos-7/>
+ <https://shadowsocks.be/6.html>
