---
title: "Serving Flask Applications with uWSGI"
date: 2020-06-13 21:44:44+0800
tags: [Flask]
categories: [Python]
---

In my [older post](https://jdhao.github.io/2020/04/06/build_webapi_with_flask_s1/), I have shared how to run the builtin development server to serve the web service. For production, we need to use more powerful web servers. Flask complies to the [WSGI](https://en.wikipedia.org/wiki/Web_Server_Gateway_Interface) (Web Server Gateway Interface) specification and can work with any web server that conforms to WSGI.

In this post, I want share how to run Flask applications using
[uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/) --- a popular WSGI-compliant server.

<!--more-->

# Install and simple running

To install uWSGI, we can use pip:

```bash
pip install uwsgi
```

To serve a simple flask application using uWSGI, we can run the following
commands:

```bash
uwsgi --http 0.0.0.0:8081 --module flask-app:app --master --processes 2 --threads 2
# or run following command
# uwsgi --socket 0.0.0.0:8081 --protocol=http --module flask-app:app
```

# Run uWSGI via config

When we add more options via the command line, it becomes less convenient. We
can also use a config file (in `ini` format) to run uWSGI:

```ini
[uwsgi]
chdir=/path/to/project
wsgi-file=flask-demo.py
callable=app
http=0.0.0.0:1234
need-app=true
lazy-apps=true
master=true
processes=2
threads=2
single-interpreter=true
die-on-term=true
procname-prefix-spaced=flask demo
vacuum=true
pidfile=demo.pid
worker-reload-mercy=5
```

Save this file as `uwsgi.ini` (or whatever you like), and then run the
application using `uwsgi --ini uwsgi.ini`. If the extension of config file is
`ini`, we also directly run uWSGI: `uwsgi uwsgi.ini`. Note that the first line
(`[uwsgi]`) is necessary and **must not be omitted**.

Below are the meanings of some of these options:

## chdir

The `chdir` option specifies the directory to run the application.

## wsgi-file and callable

Option `wsgi-file` specify the source file where your application resides.
`callable` is the name of your application. For example, if you define your
application like this: `app = Flask(__name__)`, then you should set `callable =
app`.

## master

With master mode on, the master process will restart a worker process if it
dies. Generally, it is good idea to turn master mode on with `master = true`.

See also: https://stackoverflow.com/q/35072176/6064933

## single-interpreter

 By default, uwsgi will enable multiple interpreter mode, i.e., running
 multiple apps in a single process using different interpreter. If we are
 running a single app, it is not necessary to run in this mode.

Ref:

+ http://lists.unbit.it/pipermail/uwsgi/2010-April/000247.html
+ https://docs.newrelic.com/docs/agents/python-agent/web-frameworks-servers/python-agent-uwsgi-web-server

## die-on-term

without `die-on-term = true`, you can not kill uwsgi process via `kill xxx`.
Instead, uWSGI will reload a process if it were killed. This may not be what
you want.

## need-app

if `need-app = true`, uWSGI will not run if it can not find the application to
run. Otherwise, it will load even without an application. When you request the
service, you will get HTTP 500 responses. So setting `need-app` to `true` is a
sane choice.

## lazy-apps

With `lazy-apps=true`, each process will initialize applications independently of
each other. If you do not set this option, you may run into troubles. For
example, someone reported that [PyTorch model are not running properly](https://discuss.pytorch.org/t/basic-operations-do-not-work-in-1-1-0-with-uwsgi-flask/50257/7) using
uWSGI and flask if `lazy-apps` option is not set.

See also official discussion [here](https://uwsgi-docs.readthedocs.io/en/latest/articles/TheArtOfGracefulReloading.html#preforking-vs-lazy-apps-vs-lazy).

## proc-name-spaced

It will add a custom prefix to uwsgi processes so that you can find the
processes for a specific application easily. For example, with the following
config:

```ini
proc-name-spaced = demo
```

the output will be:

```
jdhao 29516  1.0  0.0 110104 14852 pts/31   S+   21:16   0:00 demo uWSGI master
jdhao 29520  5.5  0.0 186184 28080 pts/31   S+   21:16   0:00 demo uWSGI worker 1
jdhao 29521  0.0  0.0 110104  7644 pts/31   S+   21:16   0:00 demo uWSGI http 1
```

## py-autoreload

`py-autoreload=2` will check if file is modified every 2 seconds and restart
application if modification detected. This should only be used for debugging,
and **not** for production.

Ref:

+ https://uwsgi-docs.readthedocs.io/en/latest/Configuration.html#ini-files

## pidfile

The option `pidfile` will create a file under specified location with the pid
of uWSGI master process. To kill the uWSGI process safely, you can run the
following command:

```bash
uwsgi --stop demo.pid
```

Ref:

+ [How to kill all instance of uwsgi?](https://stackoverflow.com/a/31971005/6064933)

# worker-reload-mercy

When I try to kill the uwsgi process via `Ctrl-C`, I found that uwsgi takes
long time to stop and print the following message when it finally stops:

```
worker 1 buried after 30 seconds
goodbye to uWSGI.
VACUUM: pidfile removed.
```

To close the uwsgi processes quicker, we can add the `worker-realod-mercy`
option and set it to a small value:

```ini
# set it to 5 seconds
worker-reload-mercy = 5
```

It sets the number of seconds uWSGI will wait for the worker to exit before
killing it brutally.

Then when you start uwsgi, you will see the following log message:

```
your mercy for graceful operations on workers is 5 seconds
```

Ref:

+ https://www.mail-archive.com/uwsgi@lists.unbit.it/msg05155.html
+ https://github.com/unbit/uwsgi/issues/844#issuecomment-455756013
+ [NGINX - Python - UWSGI kill issue](https://stackoverflow.com/q/37846202/6064933)

# References

+ https://uwsgi-docs.readthedocs.io/en/latest/ParsingOrder.html
+ https://www.techatbloomberg.com/blog/configuring-uwsgi-production-deployment/
+ Configuring uWSGI for Production: The defaults are all wrong: https://www.youtube.com/watch?v=p6R1h2Nn468
