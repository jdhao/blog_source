---
title: "Using Proxy in Python Requests package"
date: 2021-02-07T23:34:47+08:00
draft: false
tags: [requests]
categories: [Note]
---

If we need to access the internet via a proxy server, we should set proxy
properly when making HTTP/HTTPS requests using the [Requests](https://requests.readthedocs.io/en/master/) package.

<!--more-->

Here is a simple code sample:

```python
import requests

proxy = {'http': "http://server_ip:port", 'https': "https://server_ip:port"}

r = requests.get(url, proxies=proxy)
```

In the above code, we set the proxy used for http and https requests. However,
when I run the above code, I see the following error:

> (Caused by SSLError(SSLError(1, '[SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed (_ssl.c:833)'),))

To temporarily disable SSL verification (note that this is dangerous for
production applications! Do this at your own risk!), we can add `verify=False` to the requests method:

```python
r = requests.get(url, proxies=proxy)
```

You will see the following warnings:

>  InsecureRequestWarning: Unverified HTTPS request is being made. Adding
>  certificate verification is strongly advised. See: https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings

# Refs

+ [Proxies with Python 'Requests' module](https://stackoverflow.com/q/8287628/6064933)
+ https://2.python-requests.org/en/master/api/#main-interface
+ https://2.python-requests.org/en/latest/user/advanced/#proxies
+ [Python Requests throwing SSLError](https://stackoverflow.com/q/10667960/6064933)
+ [How do I disable the security certificate check in Python requests](https://stackoverflow.com/q/15445981/6064933)
