---
title: "Serving Concurrent Requests for LibreOffice Service"
date: 2021-06-11T00:57:25+08:00
draft: false
tags: [Docker]
categories: [technique]
---

We have set up a server to convert pptx files to pdf files using LibreOffice
(version: 6.0.7.3 ). Libreoffice is started using `subprocess.run()` command in
Python. The external command I use is something like the following[^1]:

<!--more-->

```bash
soffice --headless --convert-to pdf test.pptx
```

If the client requests this service concurrently, some of the request will fail
with no result. The weird part is that `subprocess.run()` will not report any
errors.  It is just that we can not convert pptx to pdf. If the client only
request the service one pptx file after another, there is no error in getting
the result. It seems that libreoffice can not handle multiple concurrent
requests gracefully.

I captured the stdout and stderr from the external command:

```
subprocess.run(command_list, timeout=5, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
```

I find that although the command is executed without error (r.check_status()
runs without error), `r.stdout` and `r.stderr` will be empty when no pdf is
generated.

So I add a retry strategy. If the `r.stdout` is empty, we will try at most
three times to re-run the subprocess command. This reduces the failure numbers
for concurrent requests, but there are still quite a lot of failures.

Since one instance of libreoffice is limited in its concurrent handling of
requests, why not deploy multiple instances of libreoffice? So we isolate the
relevant code to generate from pptx to pdf as a separate service and deploy it
in multiple docker containers. When new requests comes, it will be distributed
evenly to different instances of this services. To handle more concurrent
requests, we just need to deploy more docker containers. After this step, the
failure rates drops to negligible count.

It seems that there is another way to solve this issue by spawning multiple
LibreOffice instances in the same server, as documented [here](https://stackoverflow.com/a/60039246/6064933).

# Refs

+ https://ask.libreoffice.org/en/question/82515/how-can-i-run-multiple-instances-of-soffice-under-linux/
+ https://github.com/thecodingmachine/gotenberg/issues/94
+ https://github.com/algoo/preview-generator/issues/186

[^1]: there are more discussions on how to convert pptx to pdf [here](https://jdhao.github.io/2020/03/30/pptx_to_image/#use-libreoffice-directly).
