---
title: "Fix Nvidia Apt Repository Public Key Error"
date: 2022-05-05T22:37:59+08:00
markup: markdown
tags: [Ubuntu]
categories: [Linux]
---

When running `apt-get update` inside Ubuntu 18.04, I have the following error:

<!--more-->

> W: GPG error: https://developer.download.nvidia.cn/compute/cuda/repos/ubuntu1804/x86_64  InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY A4B469963BF863CC
> E: The repository 'https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64  InRelease' is no longer signed.
> N: Updating from such a repository can't be done securely, and is therefore disabled by default.
> N: See apt-secure(8) manpage for repository creation and user configuration details.

This is because Nvidia is changing their public keys for their repositories, see [the details here](https://developer.nvidia.com/blog/updating-the-cuda-linux-gpg-repository-key/).
The instructions in the original post does not work for me. I still see errors after running the command.
Since I am building a Docker image, I followed the instructions [here](https://github.com/NVIDIA/nvidia-docker/issues/1631#issuecomment-1112828208) and it works:

```docker
RUN rm /etc/apt/sources.list.d/cuda.list
RUN rm /etc/apt/sources.list.d/nvidia-ml.list
RUN apt-key del 7fa2af80
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/3bf863cc.pub
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/7fa2af80.pub
```

The two `rm` commands are crucial to fix the issue.
The directory `/etc/apt/sources.list.d/` stores the apt repository list for some packages,
so that you do not need to touch the main source list file `/etc/apt/sources.list`.
Files under this directory has the same format as the main `sources.list` file,
and they are usually added by automated tools.

The command `apt-key del 7fa2af80` will delete the old public key for Nvidia.
The `apt-key adv --fetch-keys` commands is used to add the new public keys for Nvidia.

# references

+ The use of directory `sources.list.d`
	+ https://stackoverflow.com/a/26021071/6064933
	+ https://stackoverflow.com/a/22969010/6064933
+ update public key using apt-key: https://askubuntu.com/a/865494/768311
+ apt-key doc: http://manpages.ubuntu.com/manpages/bionic/man8/apt-key.8.html
