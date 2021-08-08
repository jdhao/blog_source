---
title: "博客图床从七牛云存储迁移到腾讯云存储"
date: 2018-10-20T20:57:35+08:00
tags: [Regex, Mac, 图床, 对象存储]
categories: [Blog]
---

去年建立博客的时候，选择了[七牛](https://www.qiniu.com/)对象存储来存放博客的图片，最近收到了七牛的邮件，之前一直在用的域名只是一个测试域名，再过一阵就要回收了，到时必须使用自己的域名。大概看了一下，申请自己的域名还要涉及注册和备案，我仅仅是需要存储图片，这样太费周章，所以决定把博客的图片从七牛迁移出来。

<!--more-->

经过一番查找比较，最后决定使用腾讯云的对象存储 [COS](https://console.cloud.tencent.com/cos5/)，由于我的图片量很少，所以就算付费，每个月实际的支出也不会太大。本文介绍图片迁移过程。

# 从七牛 bucket 下载图片

七牛提供了命令行工具[qshell](https://developer.qiniu.com/kodo/tools/1302/qshell) 来对七牛存储进行操作，我们可以利用这个工具把 bucket 里面的图片全部下载到本地。对于 Mac 系统，下载以后解压文件，`qshell_darwin_x64` 即为可执行文件，为了方便后续操作，先建立 `qshell` 软链接，指向实际的可执行程序：

```bash
chmod u+x qshell_darwin_x64
ln -s qshell_darwin_x64 qshell
```

然后配置自己的账户：

```bash
qshell account <AK> <SK>
```

`<AK>` 和 `<SK>` 分别是自己的 access key and secret key，可以从七牛账户的个人面板的 [*密钥管理* 模块](https://portal.qiniu.com/user/key)查看。

`qshell` 提供了一个 `qdownload` 功能，可以批量从 bucket 下载图片，但是[该功能的下载流量默认是收费的](https://github.com/qiniu/qshell/blob/master/docs/qdownload.md)，如果想要免费，建立配置文件 `qdownload.conf`，一个示例配置内容如下：

```bash
{
    "dest_dir"   :   "/Users/jdhao/Documents/qiniu_bucket_files",
    "bucket"     :   "github-pages-image-storage",
    "prefix"     :   "",
    "suffixes"   :   "",
    "cdn_domain" :   "olvlcptdv.bkt.clouddn.com",
    "referer"    :   "",
    "log_file"   :   "download.log",
    "log_level"  :   "info",
    "log_rotate" :   1,
    "log_stdout" :   false
}
```

`dest_dir` 填写下载的图片存放路径，确保路径存在；`bucket` 填写你在七牛建立的 bucket 的名称；`cdn_domain` 填写七牛给的测试域名；其他信息可以不用填写。

配置文件填写好以后，使用下面命令下载 bucket 里面所有图片：

```bash
qshell qdownload qdownload.conf
```

程序运行结束以后，所有的图片就被下载到了指定的目录下面。

# 设置腾讯云对象存储

注册腾讯云，然后在对象存储里面建立一个存放博客图片的 bucket，注意权限要设置为「公有读私有写」[^1]，这样别人访问博客的时候，才能看到图片。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/cos_create_bucket.jpg" width="400">
</p>

然后把刚才下载的图片全部上传到这个 bucket 里面，可以批量拖拽上传。值得注意的是，如果七牛 bucket 下有文件夹，那么拷贝到腾讯云，也要保持文件夹结构，否则替换到腾讯的域名以后图片将无法访问。

# 替换原有的图片中七牛域名

接下来，使用命令行工具或者带有正则表达式的编辑器，批量替换所有博客源代码中图片的地址，把七牛云的测试域名，替换为腾讯云存储提供的域名。创建的 bucket 的访问域名可以点击「基础配置」一栏，「基本信息」部分有域名信息。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20181020221711.png" width="400">
</p>

另外，如果之前使用七牛时候使用了图像处理操作，原有图片链接中的图像操作部分也应该去掉。

# 图片上传工具

找了一圈，支持上传到腾讯云存储的有两个不错的程序，一个是[iPic](https://toolinbox.net/iPic/)，另外一个是[PicGo](https://github.com/Molunerfinn/PicGo)。iPic 免费版只能使用新浪微博图床，要使用其他图床，需要付费版，每年 \$9.9，个人觉得太贵，不值这么多，所以放弃。PicGo 支持腾讯云，并且免费，正好满足需要。

具体配置可以参考[官方wiki](https://picgo.github.io/PicGo-Doc/zh/guide/config.html)。可以直接拖拽图片到 PicGO 的菜单栏图标实现图片上传，或者如果已经把图片拷贝到剪切板，使用默认的 `Command + shift + P` 快捷键上传图片，当然快捷键可以根据自己需求修改。

# 腾讯云对象存储配置 CDN

如果直接使用腾讯云对象存储提供的图片 URL，每个月的流量费还是比较贵的，强烈建议搭配腾讯云提供的 CDN，能够减少流量费用支出。具体配置方法见 [这里](https://jdhao.github.io/2020/03/16/tencent_cos_cdn_setup/)。

[^1]: 如果要配置 CDN，可以设置权限为「私有读私有写」，具体设置见 [官方文档](https://cloud.tencent.com/document/product/436/18670)。
