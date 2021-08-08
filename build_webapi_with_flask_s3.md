---
title: "Build Web API with Flask --- Work with JSON-like Dict"
date: 2020-04-13T22:29:23+08:00
tags: [Flask, HTTP, requests]
categories: [Python]
---

This is a simple post about how to send JSON-like Dict data to a Flask server
via [requests](https://requests.readthedocs.io/en/master/) package.

<!--more-->

# Post dict to Flask server

To post Python dict using requests package, we can use the `data` parameter:

```python
import requests

payload = {'foo': 1, 'bar': 2}
requests.post(url, data=payload)
```

In the above example, the
[`Content-Type`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type)
for posted data is `application/x-www-form-urlencoded`, you can get the posted
data in the Flask server side via `request.form`:

```python
from flask import request

data = request.form.to_dict()
```

## Post and receive dict with value of list type

One caveat when we use `request.form.to_dict()` is that we only get the first
element for a dict value of list type. For example, if `payload` is the
following dict:

```python
payload = {'id': '123', 'type': 'jpg', 'box': [0, 0, 100, 100]}
```

On the Flask server side, after `request.form.to_dict()`, you only get the
following:

```
{'id': '123', 'type': 'jpg', 'box': '0'}
```

which is completely non-obvious for Flask beginners. To get the full list
instead, we have two ways:

+ Use `requests.form.to_dict(flat=False)` (see [here](https://werkzeug.palletsprojects.com/en/0.16.x/datastructures/#werkzeug.datastructures.MultiDict.to_dict) on
the description about `to_dict()`). One drawback is that values that are non-list
originally are converted to list. So now you get:
    ```
    {'id': ['123'], 'type': ['jpg'], 'box': ['0', '0', '100', '100']}
    ```
+ Use `request.form.getlist('key')` to get the list corresponding to `key`, more
about this [here](https://werkzeug.palletsprojects.com/en/0.16.x/datastructures/#werkzeug.datastructures.MultiDict.getlist).

# Directly posting JSON data

Another way to post Python dict is to directly post and receive JSON data. When
making requests, we can use the `json` parameter of `requests.post()` method:

```python
r = requests.post(url, json=payload)
```

In this way, requests package will serialize your dict into JSON format. The
`Content-Type` in HTTP header will be set to `application/json`. In the Flask
side, we need to use
[`request.get_json()`](https://stackoverflow.com/q/20001229/6064933) to get the
posted JSON data.

## Post or return base64 encoded image in dict

When you post base64 encoded image in dict via requests package, you may see
the following error:

> TypeError: Object of type bytes is not JSON serializable

This is because the Python JSON library cannot serialize byte type. You can
convert the base64 encoded image as string via `decode()` method:

```python
import base64
import requests

with open('test.jpg', 'rb') as f:
    im_b64 = base64.b64encode(f.read())

payload = {'image': im_b64.decode()}
r = requests.post(url, json=payload)
```

Similarly, when you want to return base64 encoded image in the Flask server
side via `jsonify()` method, you need also to convert bytes type to str before
JSON library can serialize it.

# References

+ [Flask: get request.form as normal dict](https://stackoverflow.com/a/50728523/6064933).
+ [Convert form to dict in Flask](https://stackoverflow.com/a/35625509/6064933).
+ [Flask: post a list](https://stackoverflow.com/q/53362322/6064933).
+ [Flask: objects of bytes type not JSON serializable](https://stackoverflow.com/q/51481755/6064933).
+ [Content-Type in POST request](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/POST).
+ [Post JSON via Python requests package](https://stackoverflow.com/q/9733638/6064933).
+ [Send multipart/form-data request in Python](https://stackoverflow.com/q/12385179/6064933).
