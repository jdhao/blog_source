---
title: "Build A Web API Service Using Flask -- The Basics"
date: 2020-04-06T22:05:07+08:00
tags: [Flask, HTTP, requests]
categories: [Python]
---

Web API is a kind of web application. Simply put, web APIs are built for other
applications to get info from the web server. For Python,
[Flask](https://flask.palletsprojects.com/en/1.1.x/) can be used to build web
applications. In this post, I want to share how to build web APIs with Flask.

<!--more-->

# A simple web API

Supposing that we want to build a simple web API that returns the square root a
number, the following code shows how to do that:

```python
import math
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/sqrt', methods=["POST"])
def sqrt():
    num = request.form.get('num', type=int)

    return jsonify({'result': math.sqrt(num), 'msg': 'success'})
```

In the above code, we first create a flask application using `Flask(__name__)`,
which lets Flask know that we are building an application. We then use the
`@app.route()` decorator to define a URL path that we can later incorparate
with IP and port number to access our web API. The `methods` parameter is used
to specify what kinds of [HTTP
requests](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) the server
can process from the client.

Below the app decorator, we define a [view
function](https://stackoverflow.com/q/46254054/6064933) to process the client
request, the actual name of this view function does not matter. The view
function name does not have be the same as `app.route` path (ref
[here](https://stackoverflow.com/q/55103067/6064933)).

## Processing the client request

To process the client requests in the server side, Flask provides the `request`
module. When a client is using a `POST` request to post a JSON object, the
posted data will be available in `request.form`, which is like a Python
dictionary. Note that by default, the value for a key is of string type.
You have to specify the `type` parameter to convert the value to desired type:

```python
num = request.form.get('num', type=int)
```

If you do not use `type=int`, `num` will be a string instead of an integer..

## Return JSON object to client

Flask provides the [`jsonify`](https://flask.palletsprojects.com/en/1.1.x/api/#flask.json.jsonify)
method to serialize any compatible data type to JSON format. Finally, we
return the result in JSON format.

# Running the development server

After we build the simple web API, we need to test if it works. First, we need
to run the web server. Flask ships with a simple web server for testing
purposes. There are two ways that we can run the web server: via `app.run()` or
via the Flask command line interface.

## Run the HTTP server via `app.run()`

Add the following code to the Flask application module:

```python
if __name__ == "__main__":
    # the server will listen on port 1234. Default is port 5000.
    app.run(port=1234)
```

If we do not specify the host address, the default will be `127.0.0.1`, which
means that we can only access the service in our machine. To use the IP address
of our machine, we can specify the `host` parameter in `app.run`:

```python
app.run(host="0.0.0.0", port=1234)
```

Then other machines in the same network can access the web service.

Finally, we can start the HTTP server with `python application.py` (suppose
that our module is named `application.py`). We will see the following message
on command line:

```
 * Serving Flask app "application" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://127.0.0.1:1234/ (Press CTRL+C to quit)
```

## Run the server via Flask command line interface

The second and officially-recommended way to run Flask application is via the
Flask command line interface (`flask run`).

When we run the application using `flask run`, the following settings does
not work:

```python
if __name__ == "__main__":
    # when debug is True, flask will reload app once code changes.
    app.run(host="0.0.0.0", port=8081)
```

The correct way to run the application is to run the following command:

```bash
export FLASK_APP=application
FLASK_ENV=development flask run --host=0.0.0.0 --port 1234
```

`FLASK_APP` variable is used to specify which file our flask application
resides. `FLASK_ENV` is used to specify the flask running environment. The
default is `production`. If you use `development`, Flask will activate debug
mode, i.e., it will reload the application once the code changes.

`--host` is used to specify the host address. `--port` is used to specify the
listening port. We can also change the port number via `FLASK_RUN_PORT` env
variable:

```bash
export FLASK_APP=application
export FLASK_RUN_PORT=1234
FLASK_ENV=development flask run --host=0.0.0.0
```

## Auto reloading on code changes

Flask provides a debug mode where the app will be reloaded once the code
changes, so we do not need to manually stop the server and run it again.

If we run the server via `app.run()`, we can activate debug mode via
`debug=True`:

```python
if __name__ == "__main__":
    # when debug is True, flask will reload app once code changes.
    app.run(host="0.0.0.0", port=1234, debug=True)
```

We may encounter following errors when we run `python application.py`:

> OSError: [Errno 8] Exec format error

The reason for this error can be found
[here](https://github.com/pallets/werkzeug/issues/1482). To put it simply, if
`application.py` has execution right, then Flaks assumes that the script can be
run via `./application.py`. So we must add
[shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) at the beginning of the module:

```bash
!#/usr/bin/env python
```

Or we must disable the execution right of this module. When the application is
reloaded, it will be run via `python application.py`.

Another way is to run the app via Flask command line interface, as we have
mentioned earlier. Set the `FLASK_ENV` variable to `development` or set `FLASK_DEBUG`
to `1`:

```bash
export FLASK_APP=application
FLASK_ENV=development flask run --host=0.0.0.0 --port 1234

# or the following
# FLASK_DEBUG=1 flask run --host=0.0.0.0 --port 1234
```

# Test the web API

The full URL for the web API is: `http://127.0.0.1:1234/sqrt`.

We can test the web API via curl:

```bash
curl -XPOST -d num=4 "http://127.0.0.1:1234/sqrt"
```

It is more convenient to test the API via
[requests](https://requests.readthedocs.io/en/master/) package using the
following code:

```python
import requests


def post_data():
    url = "http://127.0.0.1:1234/sqrt"

    payload = {'num': 4}
    response = requests.post(url, data=payload)

    return response.json()


def main():
    result = post_data()
    print(result)


if __name__ == "__main__":
    main()
```

If everything is OK, you will see the following output:

```
{'msg': 'success', 'result': 2.0}
```

# References

+ [Flask quickstart guide](https://flask.palletsprojects.com/en/1.1.x/quickstart/#quickstart).
+ [What is endpoint in Flask](https://stackoverflow.com/q/19261833/6064933).
+ [Run flask server](https://flask.palletsprojects.com/en/1.1.x/server/).
+ [change host and port](https://stackoverflow.com/q/41940663/6064933).
+ [configure flask server to be visible across the network](https://stackoverflow.com/q/7023052/6064933).
+ [flask run: configure port](https://github.com/pallets/flask/issues/2661).
+ [Get data received in Flask](https://stackoverflow.com/q/10434599/6064933).
