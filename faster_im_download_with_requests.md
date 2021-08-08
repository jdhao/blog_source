---
title: "Downloading Images Faster with requests Sessions"
date: 2020-06-21 22:13:05+0800
tags: [HTTP, requests]
categories: [Python]
---

In my [previous post](https://jdhao.github.io/2020/06/17/download_image_from_url_python/#using-requests-package),
I write about how to download an image from URL using
[requests](https://github.com/psf/requests) package. In this post, I want to
share ways to make the download speed faster.

<!--more-->

Various ways exists for downloading an image using the requests package.

# Persistent session or not?

There are two ways to make an HTTP request using requests:

+ using `requests.get()`
+ using `session.get()` where `session` is a [`requests.Session()`](https://2.python-requests.org/en/master/api/#requests.Session) object.

The difference is that with `requests.get()`, a new `session` object is created
each time when we make a request. What does this mean? It means that each time
we make the request, we need to establish a new TCP connection to the remote
host. The connection is closed when we have finished the request.

With `session.get()`, if we are making several requests to the same host, the
connection will be reused so that no time is wasted re-establishing new
connections. This is known as [HTTP persistent connection](https://en.wikipedia.org/wiki/HTTP_persistent_connection).
In summary, using session will reduce the image download time.

# stream or not?

As said in the previous post, for large files, we may want to use `stream`
parameter when making the request, which will reduce the memory overhead. So we
have two ways to get the binary image from the response:

- using `response.content`
- using `response.raw.read()`


# Benchmark

By combining session options and stream options, we have four different ways to
download images using requests.

- `r.raw` with session
- `r.content` with session
- `r.raw` without session
- `r.content` without session


In order to find which is faster, I have run a small benchmark. I combine
`concurrent.futures` and requests to download several images concurrently using
the above four different settings of requests. The complete code can be found
[here](https://github.com/jdhao/im_download_requests).

According to my benchmark, using sessions is faster than requests without
explicit sessions. For the stream option, using `r.raw` is generally faster
than using `r.content`, but it is not always the case. If the image size not
big enough, using either `r.raw` or `r.content` is fine.

The benefit of using explicit sessions is more apparent when we are downloading
more images concurrently. On my Mac, when downing 20 images concurrently, the
output is:

```
avg time (r.raw with session): 0.2751150131225586
avg time (r.content with session): 0.2750370740890503
avg time (r.raw no session): 1.5932393550872803
avg time (r.content no session): 0.8408806085586548
```

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200720020301.jpg" width="800">
</p>

With 40 images, the output is:

```
avg time (r.raw with session): 0.3991949200630188
avg time (r.content with session): 0.42252095937728884
avg time (r.raw no session): 1.937515652179718
avg time (r.content no session): 1.912631618976593
```

From the above results, we can conclude that using requests with sessions will
reduce image download time immensely.

The takeaway from this post is that using sessions will download images faster,
thus saving our time .

# References

+ https://requests.readthedocs.io/en/master/user/advanced/
+ [Difference between using requests.get() and requests.session().get()](https://stackoverflow.com/q/32986228/6064933)?
+ [Python 3 urllib Vs requests performance](https://stackoverflow.com/a/37170963/6064933).
