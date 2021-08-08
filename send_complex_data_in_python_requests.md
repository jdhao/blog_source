---
title: "Post Nested Data Structure to the Server Using Requests"
date: 2021-04-08T23:54:08+08:00
draft: false
tags: [requests, HTTP, Flask]
categories: [Python]
---

In this post, I will share how to post complex data and decode it in the server
side in Python.

<!--more-->

# The problem

To post a simple Python dict with no nested structure to the server, we may use
the below code:

```python
payload = {'name': 'john smith', 'age': 20}

r = requests.post("https://httpbin.org/post", data=payload)
```

By default, when we use `requests.post(url, data=payload)` to post `payload` to
the server. We can check the header of the HTTP request via the following
command:

```
print(r.request.header)
```

The `Content-Type` of this HTTP request will be
`application/x-www-form-urlencoded` by default. To decode the posted dict, if
we use Flask, we can use the following code:


```python
from flask import Flask, request


user_request = request.form.to_dict()
```

When the data structure is simple, it is fine to use the default options. Once
there are nested structure in your Python dictionary, the server can not decode
the message properly if the client is using this Content-Type, for example,
when we have nested dictionary (a 2-D list):

```python
payload = {'matrix': [[1, 2, 3], [4, 5, 6]], 'msg': 'hello'}
```

In this case, you will lost the data structure since
`application/x-www-form-urlencoded` can note keep the data structure.

# Solution

There are two solutions here.

## Serialize data as json string

First, we can encode the complex dictionary into string using json:

```python
# Note that payload must be json-serializable, or you will meet an error.
payload = json.dumps(payload)

r = requests.post(url, data=payload)
```

On the server side (suppose that we are using Flask), we can decode the string
to get the original dict:

```python
import json

from flask import request

# decodes the string into original dictionary
user_request = json.loads(request.data)
```

## Use application/json as Content-Type

Second, we can directly tell the server that we are sending data in json format
using requests:

```python
payload = {'matrix': [[1, 2, 3], [4, 5, 6]], 'msg': 'hello'}

r = requests.post(url, json=payload)
```

In this case, the request header will be something like the following:

```
{'User-Agent': 'python-requests/2.22.0', 'Accept-Encoding': 'gzip, deflate',
'Accept': '*/*', 'Connection': 'keep-alive', 'Content-Length': '2705',
'Content-Type': 'application/json'}
```

The `Content-Type` field will be `application/json`, in which we tell the
server that we are sending JSON data.

On the server side (suppose you are using flask), we can retrieve that data
using the following script:

```python
from flask import request


# directly decodes the request body as JSON.
user_request = request.get_json()
```

# Ref

+ [What are the differences between application/json and application/x-www-form-urlencoded](https://stackoverflow.com/q/9870523/6064933)
+ https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type
