---
title: "Note on Using requests package"
date: 2020-07-09T23:30:14+08:00
tags: [requests]
categories: [Python]
---

# How to check request header and body?

When making requests, we may want to see exactly what are being requested. With
requests, it is easy do access the request header and request body:

```python
import requests

url = "http://httpbin.org/post"
payload = {"apple": 10, "pear": [20, 30], "img": "http://example.com/demo.jpg"}
r = requests.post(url, json=payload)

print(f"request headers: {r.request.headers}")
print(f"request body: {r.request.body}")
```

A sample output is:

```
request headers: {'User-Agent': 'python-requests/2.19.1', 'Accept-Encoding':
'gzip, deflate', 'Accept': '*/*', 'Connection': 'keep-alive', 'Content-Length':
'69', 'Content-Type': 'application/json'}
request body: b'{"apple": 10, "pear": [20, 30], "img": "http://example.com/demo.jpg"}'
```

Ref:

+ [How to display body and headers before sending POST request?](https://stackoverflow.com/q/47664001/6064933)
+ [How can I see the entire HTTP request that's being sent by my Python application?](https://stackoverflow.com/a/10588737/6064933)

# How to encode JSON when using application/x-www-form-urlencoded?

When making requests using json data in `requests.post(url, data=json_dict)`,
the `Content-Type` is `application/x-www-form-urlencoded`. How is JSON encoded
by requests?

In requests, this is handled by class
[`RequestEncodingMixin`](https://github.com/psf/requests/blob/master/requests/models.py#L60),
it provides ` _encode_params()` method to convert provided into url-encoded
string (JSON is converted to [query
string](https://en.wikipedia.org/wiki/Query_string) and some characters are
escaped). Under the hood, it is using
[urllib.parse.urlencode()](https://docs.python.org/3/library/urllib.parse.html#urllib.parse.urlencode)
to encode the json data.

```python
url = "http://httpbin.org/post"
payload = {"apple": 10, "pear": [20, 30], "img": "http://example.com/demo.jpg"}
r = requests.post(url, data=payload)

print(f"request body: {r.request.body}")
```

The request body is:

```
apple=10&pear=20&pear=30&img=http%3A%2F%2Fexample.com%2Fdemo.jpg
```

We can also encode JSON directly using `urllib`:

```python
import urllib

payload = {"apple": 10, "pear": [20, 30], "img": "http://example.com/demo.jpg"}
print(urllib.parse.urlencode(payload, doseq=True))
```

The encoded string is the same.

Ref:

+ https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/POST
+ [Python requests module: urlencoding json data](https://stackoverflow.com/q/15737434/6064933)

# Set up max retries

When we use requests to request a URL, we may sometimes get "max retried
exceed" errors due to various causes such as unstable network or frequent
request.

We can add retry features using requests to increase the chance of successful
request:

```python
from requests.adapters HTTPAdapter

from requests.packages.urllib3.util.retry import Retry

session = requests.Session()

retry_strategy = Retry(total=2)
adapter = HTTPAdapter(max_retries=retry_strategy)
session.mount("http://", adapter)
session.mount("https://", adapter)

r = session.get(url)
```

In the above code, we first set the parameter `max_retries` for
[HTTPAdapter](https://requests.readthedocs.io/en/master/api/#requests.adapters.HTTPAdapter),
which can be a simple number or a
[Retry](https://urllib3.readthedocs.io/en/latest/reference/urllib3.util.html#urllib3.util.retry.Retry)
object from the urllib3 package.

Then we change the original adapter used by `session` by using the
[`mount()`](https://requests.readthedocs.io/en/master/api/#requests.Session.mount)
method. The first parameter is the URL prefix for a certain adapter: when the
URL matches the prefix, the corresponding adapter will be used by requests
session.

Ref:

+ [Max retries exceeded with URL in requests](https://stackoverflow.com/q/23013220/6064933)
