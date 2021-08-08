---
title: "Set Timezone inside Docker Container"
date: 2020-06-11 00:03:30+0800
tags: [Docker, Ubuntu]
categories: [Linux]
---

After running a Ubuntu docker container, I found that the default time zone is
UTC. I want to change it to my local timezone.

<!--more-->

This is how to change the timezone when we are building the docker container:

```docker
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends tzdata \
    && rm -rf /var/lib/apt/lists/*
RUN ln -fs /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && dpkg-reconfigure --frontend noninteractive tzdata
```

Since we can enter input when building docker images, by setting
`DEBIAN_FRONTEND` to `noninteractive`, we make sure `apt-get` will install
tzdata package in a non-interactive way.

Package `tzdata` will install a lot of timezone files under
`/usr/share/zoneinfo` directory. We then create a symlink to use
`Asia/Shanghai` as the new timezone:

```bash
ln -fs /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

Finally, we use `dpkg-reconfigure` to configure tzdata to use the new timezone:

```bash
dpkg-reconfigure --frontend noninteractive tzdata
```

The option `--frontend noninteractive` avoids the interactive prompt to choose
area and city.

# References

+ [Avoid user interaction when installing tzdata](https://askubuntu.com/a/1013396/768311).
+ [Automate dpkg-reconfigure](https://serverfault.com/a/846989/435975).
+ [Install tzdata on Ubuntu docker image](https://serverfault.com/a/949998/435975).
+ https://github.com/phusion/baseimage-docker/issues/415#issuecomment-305962877
+ [What is /etc/timezone used for](https://unix.stackexchange.com/q/452559/221410)?
+ [What is /etc/localtime](https://www.freedesktop.org/software/systemd/man/localtime.html#)?
