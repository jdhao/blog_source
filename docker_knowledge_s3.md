---
title: "Notes on Docker -- s3"
date: 2021-03-25T23:31:23+08:00
draft: false
tags: [Docker]
categories: [Note]
---

Personal notes on docker usage.

<!--more-->

# docker: permission denied when pushing

When I run the docker command, I got the following error:

> docker: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.35/containers/create: dial unix /var/run/docker.sock: connect: permission denied.See 'docker run --help'.

This is because docker normally requires root right to run. We can create a
docker group and add current user to this group:

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
```

Restart to make the change take effect. Now we should be able to run docker
command without errors.

Ref:

+ [How to fix docker: Got permission denied issue](https://stackoverflow.com/q/48957195/6064933)

# How do I push a container to another remote repo

Suppose we have a local container and we want to push it to another remote
repository, how do we do it?

First, we need to tag it:

```bash
docker tag IMAGE:[tag] remote_repo_address/user/name:tag
```

Instead of using names, you can also use `CONTAINER ID` or `NAMES` in the
output `docker ps`. The all refer to the same container.

Then we can push to the remote repo:

```bash
docker push remote_repo_address/user/name:tag
```

Ref:

+ [How to push a docker image to a private repository](https://stackoverflow.com/q/28349392/6064933)

# how to map multiple ports

In using docker, we can use `-p <host_port>:<container_port>` to expose
container port to host port. If we need to expose multiple ports, we simply
need to use multiple `-p` followed by port mappings.

```
docker run -it -p <host_port1>:<container_port1> -p <host_port2>:<container_port2>
```

Ref:

+ [How can I expose more than 1 port with Docker?](https://stackoverflow.com/q/20845056/6064933)

# Docker logging file

By default, docker will record the output to stdout and stderr in a container.
The log file is stored on the host machine. The log file location for a
container is:

```
/var/lib/docker/containers/<container-id>/<container-id>-json.log
```

or you can directory find the log location for a container using the following
command:

```
docker inspect <container-id> |grep LogPath
```

Each line in the log file is in JSON format, recording the log message, stream,
and time, like this:

```json
{"log":"echo test\r\n","stream":"stdout","time":"2021-03-23T06:32:05.401925782Z"}
```

Note that the time shown above is neither the time on your host machine nor
your container. Based on [issue here](https://github.com/docker/cli/issues/604)
and  post [here](https://stackoverflow.com/a/55505090/6064933), it seems that
docker is using UTC time zone. So the time shown in the log file may not be the
time on your host machine or docker container.

To show the incoming logs in a container, use `docker logs -f <container-id>`
(like what `tail -f` does).

Ref

+ [Where is a log file with logs from a container?](https://stackoverflow.com/q/33017329/6064933)
+ https://sematext.com/blog/docker-logs-location/

# name or rename a docker container

By default, when we use `docker run` to start a container, docker will use a
random name for this container. We can also use a custom name by using the
`--name` option:

```bash
docker run -it --name my_service ...
```

We can also rename a running container:

```bash
# rename container with the name foo to bar
docker rename foo bar
```

Ref:

+ https://www.tecmint.com/name-docker-containers/

# Add zsh completion for docker command

Adding subcommand and option completion for docker command would be very
helpful. Here is how to do it with the help of [zinit](https://github.com/zdharma/zinit).

```zsh
# use docker completion script provided by Oh-my-zsh: https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/docker
zinit ice as"completion"
zinit snippet OMZ::plugins/docker/_docker

autoload -Uz compinit  && compinit -i
```
