---
title: "Delete Keys Matching A Pattern with py-redis-cluster"
date: 2021-07-20T23:59:30+08:00
draft: false
tags: [redis]
categories: [Python]
---

We have a Python web service where we store some `key-val` pairs in redis.
Occasionally, I want to delete some of the keys matching a certain pattern.
Current, we are using [redis-py-cluster](https://github.com/Grokzen/redis-py-cluster) for redis-related operations.

<!--more-->

We can use `scan_iter()` method to search such keys and delete them. The first
parameter to `scan_iter()` is the matching pattern. The code looks roughly like
this:

```python
for k in redis_client.scan_iter("prefix:*"):
    redis_client.delete(k)
```

The above code kinda works, but it is awfully slow. We can add the `count`
option to accelerate deletion. Option `count` specify how many keys per scan
will return. According to [here](https://redis.io/commands/scan), the default count is 10, which is rather small.

```python
batch_size = 500
keys = []

for k in redis_client.scan_iter("prefix:*", count=batch_size):
    keys.append(k)
    if len(keys) >= batch_size:
        redis_client.delete(*keys)
        keys = []
if len(keys) > 0:
    redis_client.delete(*keys)
```

Using a large `count` will speed up the deletion process significantly. I have
benchmarked on about 20000 keys. Here is what I have found:

```
batch size  Time taken (seconds)
100         929
500         260
1000        175
2000        133
4000        107
5000        106
10000       93
```

# Refs

+ https://gist.github.com/dingmaotu/b465509f5c5d54dceacf5a2eb985c739
