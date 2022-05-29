---
title: "How to Download Image from URL using Python"
date: 2020-06-17 09:19:30+0800
tags: [requests]
categories: [Python]
---

Recently, I want to download some images using Python.
This is what I've learned after the survey.

<!--more-->

# Using urllib package

The native and naive way is to use
[`urllib.request`](https://docs.python.org/3/library/urllib.request.html#module-urllib.request) module to download an image.

```python
import urllib.request

url = "https://cdn.pixabay.com/photo/2020/05/12/17/04/wind-turbine-5163993_960_720.jpg"

r = urllib.request.urlopen(url)
with open("wind_turbine.jpg", "wb") as f:
    f.write(r.read())
```

However, the above code may error out with following message:

>  urllib.error.HTTPError: HTTP Error 403: Forbidden

In this case, we need to add a HTTP header to the request:

```python
import urllib.request

# The following way works. Ref: https://stackoverflow.com/a/45358832/6064933
req = urllib.request.Request(url, headers={'User-Agent': 'Mozilla/5.0'})
with open("wind_turbine.jpg", "wb") as f:
    with urllib.request.urlopen(req) as r:
        f.write(r.read())
```

# Using requests package

A better way is to use [requests](https://requests.readthedocs.io/en/master/)
package. Here is a simple example to download an image using requests:

```python
import requests

url = "https://cdn.pixabay.com/photo/2020/05/12/17/04/wind-turbine-5163993_960_720.jpg"

r = requests.get(url)
with open("wind-turbine.jpg", "wb") as f:
    f.write(r.content)
```

## Downloading large files with streaming

### response.iter_content ###

In the above code, all content of the image will be read into memory at once.
If the image is large, it may consume too much memory.

Alternatively, we can set `stream` parameter to `True` to stream request. In
this case, only the response header is downloaded. We can retrieve the image in
a whole using `response.content`[^1] or chunk by chunk by using
[`response.iter_content`](https://requests.readthedocs.io/en/master/api/#requests.Response.iter_content) method:

```python
# Using requests to download large files.
with requests.get(url, stream=True) as r:
    with open("wind-turbine.jpg", "wb") as f:
        for chunk in r.iter_content(chunk_size=1024):
            if chunk:
                f.write(chunk)
```

### response.raw ###

When `stream` is `True`, we can also use `response.raw` to stream the download.
`response.raw` is a file-like object. With the help of `shutil.copyfileobj()`,
we can save the image like this:

```python
# using r.raw
with requests.get(url, stream=True) as r:
    with open("wind-turbine.jpg", "wb") as f:
        r.raw.decode_content = True
        shutil.copyfileobj(r.raw, f)
        # or f.write(r.raw.read())
```

# References

+ [Python 3 urllib HTTP 403 error](https://stackoverflow.com/q/28396036/6064933).
+ [Why to use iter_content and chunk_size in python requests](https://stackoverflow.com/q/46205586/6064933)?
+ [How to download image using requests](https://stackoverflow.com/q/13137817/6064933)?
+ [Download large file in python with requests](https://stackoverflow.com/q/16694907/6064933).
+ https://requests.readthedocs.io/en/latest/user/advanced/#body-content-workflow

[^1]: You may want to avoid this for large files!
