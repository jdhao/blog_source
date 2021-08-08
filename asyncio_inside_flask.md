---
title: "How to Use Asyncio with Flask Applications"
date: 2020-06-07 20:02:18+0800
tags: [Flask, async]
categories: [Python]
---

I was using [asyncio](https://docs.python.org/3/library/asyncio.html) inside a
view function for Flask to run some asynchronous functions and met an error.

<!--more-->

Here is the demo code:

```python
import asyncio

from flask import Flask, jsonify

app = Flask(__name__)

@app.route("/toy", methods=["GET"])
def index():
    loop = asyncio.get_event_loop()
    result = loop.run_until_complete(hello())

    return jsonify({"result": result})


async def hello():
    await asyncio.sleep(1)
    return 1


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=4567, debug=False)
```

When I request this service, I get the following error from asyncio.event:

> RuntimeError: There is no current event loop in thread 'Thread-1'.

for the following line of code:

```python
loop = asyncio.get_event_loop()
```

From the asyncio [documentation on default event loop policy](https://docs.python.org/3.6/library/asyncio-eventloops.html#asyncio.AbstractEventLoopPolicy):

>  If the current thread doesn’t already have an event loop associated with it,
>  the default policy’s get_event_loop() method creates one when called from
>  the main thread, but raises RuntimeError otherwise

So it seems that the view function `index()` is not run in the main thread. As
a result, there is no event loop associated with the current thread, hence the
error message. To verify this, we can use the `threading` module to help us
find which thread we are currently in:

```python
import threading
import asyncio

from flask import Flask, jsonify


print(f"In flask global level: {threading.current_thread().name}")
app = Flask(__name__)

@app.route("/toy", methods=["GET"])
def index():
    print(f"Inside flask function: {threading.current_thread().name}")

    loop = asyncio.get_event_loop()
    result = loop.run_until_complete(hello())

    return jsonify({"result": result})


async def hello():
    await asyncio.sleep(1)
    return 1


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=4567, debug=False)
```

When running the flask app, we can see the following message:

> In flask global level: MainThread

So the statement outside the view function is run in the main thread.

When we request the service, we can see the following message from the view
function:

> Inside flask function: Thread-1

This verifies that Flask is indeed running the view functions in a separate
thread other than the main thread.

In the [Flask.run()](https://flask.palletsprojects.com/en/1.1.x/api/#flask.Flask.run)
doc, this is also briefly mentioned in the `changelog` part:

> Changed in version 1.0: If installed, python-dotenv will be used to load
> environment variables from .env and .flaskenv files.
>
> If set, the FLASK_ENV and FLASK_DEBUG environment variables will override env and debug.
>
> **Threaded mode is enabled by default.**

Under the hood, `Flask.run()` is using
[werkzeug.serving.run_simple()](https://werkzeug.palletsprojects.com/en/1.0.x/serving/#werkzeug.serving.run_simple)
to server the app and set the `threaded` option to `True` by default:

> **threaded** – should the process handle each request in a separate thread?

What does the threaded mode mean? It means that the server can serve requests
in a non-blocking fashion, i.e., it does not need to wait for one request to
finish to process another request. That is often preferred than the
single-threaded mode. Note, however, that the development server is not for
production purposes. We need to use dedicated WSGI server such as
[gunicorn](https://gunicorn.org/) and [uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/)
in production for better performance.


# References

+ [Can I serve multiple clients using just Flask app.run() as standalone?](https://stackoverflow.com/q/14814201/6064933).
+ [Handle Flask requests concurrently with threaded=True](https://stackoverflow.com/q/38876721/6064933).
