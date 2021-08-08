---
title: "uWSGI Install and Use Issues"
date: 2020-07-02T01:02:17+08:00
tags: []
categories: [Python]
---

My notes on several issues when installing and running
[uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/).

<!--more-->

# pip install uwsgi error

When I try to install uwsgi using pip, there are compilation errors. After some
search, based on discussions
[here](https://github.com/unbit/uwsgi/issues/1770#issuecomment-411674051), I
find the cause of this issue is that the version of gcc is too hight. I am
using Ubuntu 18.04, and the default gcc version is 7.4.0.

Based on [this post](https://askubuntu.com/a/1084932/768311), the minimum gcc
we can install from the default Ubuntu 18.04 remote repo is gcc-4.8. There is
no gcc-4.7 available for install. It turns out that gcc-4.8 works fine:

```bash
apt-get update && apt-get install gcc-4.8 g++-4.8
```

Then we need to change the gcc command to point to gcc-4.8:

```bash
rm /usr/bin/gcc && ln -s /usr/bin/gcc-4.8 /usr/bin/gcc
```

After that, I can install uwsgi successfully using pip. Finally, let's revert
the gcc version to its old state:

```bash
rm /usr/bin/gcc && ln -s /usr/bin/gcc-7 /usr/bin/gcc
```

# uwsgi can not find shared object

I use the following command to install uwsgi with conda:

```bash
conda create -n py36 -c conda-forge python==3.6.5 uwsgi
```

When I run `uwsgi --version`, I am seeing the following error message:

> error while loading shared libraries: libicui18n.so.58: cannot open shared
> object file: No such file or directory

The solution is to run `conda install icu=58`, ref [here](https://github.com/conda-forge/uwsgi-feedstock/issues/26).

# uwsgi segmentation fault

I am using uwsgi 2.0.18 (installed via `conda install -c conda-forge uwsgi`)
with python 3.7.7 from Miniconda.  When I use uwsgi to server a simple Flask
application, I am seeing segmentation errors below:

```txt
uwsgi(uwsgi_backtrace+0x2c) [0x5638f8685bbc]
uwsgi(uwsgi_segfault+0x32) [0x5638f8685ff2]
/lib/x86_64-linux-gnu/libc.so.6(+0x3ef20) [0x7f8c09c22f20]
/opt/miniconda/lib/python3.7/lib-dynload/_queue.cpython-37m-x86_64-linux-gnu.so(+0x2932) [0x7f8c07fe9932]
uwsgi(_PyDict_DelItem_KnownHash+0x36b) [0x5638f860ec1b]
uwsgi(_PyEval_EvalFrameDefault+0x2510) [0x5638f85a1680]
uwsgi(_PyEval_EvalCodeWithName+0x2f9) [0x5638f862abc9]
uwsgi(_PyFunction_FastCallDict+0x1d7) [0x5638f86045a7]
uwsgi(+0x1ed7a3) [0x5638f86057a3]
uwsgi(PyObject_CallFunctionObjArgs+0x99) [0x5638f8605a09]
uwsgi(PyObject_ClearWeakRefs+0x33c) [0x5638f86010fc]
uwsgi(+0x1a638c) [0x5638f85be38c]
uwsgi(+0x1b9a27) [0x5638f85d1a27]
uwsgi(+0x1f70b8) [0x5638f860f0b8]
uwsgi(+0x1a6435) [0x5638f85be435]
uwsgi(_PyFunction_FastCallKeywords+0x290) [0x5638f8604020]
uwsgi(_PyEval_EvalFrameDefault+0x40a) [0x5638f859f57a]
uwsgi(_PyFunction_FastCallDict+0x10b) [0x5638f86044db]
uwsgi(_PyEval_EvalFrameDefault+0x1e1d) [0x5638f85a0f8d]
uwsgi(_PyFunction_FastCallKeywords+0xfb) [0x5638f8603e8b]
uwsgi(_PyEval_EvalFrameDefault+0x6d4) [0x5638f859f844]
uwsgi(_PyFunction_FastCallKeywords+0xfb) [0x5638f8603e8b]
uwsgi(_PyEval_EvalFrameDefault+0x6d4) [0x5638f859f844]
uwsgi(_PyFunction_FastCallKeywords+0xfb) [0x5638f8603e8b]
uwsgi(_PyEval_EvalFrameDefault+0x6d4) [0x5638f859f844]
uwsgi(_PyFunction_FastCallDict+0x10b) [0x5638f86044db]
uwsgi(_PyObject_Call_Prepend+0x63) [0x5638f86054b3]
uwsgi(+0x1b56fa) [0x5638f85cd6fa]
uwsgi(PyObject_Call+0x6b) [0x5638f8604bdb]
uwsgi(python_call+0x12) [0x5638f869ffa2]
uwsgi(uwsgi_request_wsgi+0x266) [0x5638f86a2556]
uwsgi(wsgi_req_recv+0xa7) [0x5638f8633557]
uwsgi(simple_loop_run+0xd1) [0x5638f8681521]
uwsgi(simple_loop+0x11) [0x5638f8681301]
uwsgi(uwsgi_ignition+0x315) [0x5638f8686395]
uwsgi(uwsgi_worker_run+0x2b4) [0x5638f868aef4]
uwsgi(uwsgi_run+0x525) [0x5638f868b575]
/lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0xe7) [0x7f8c09c05b97]
uwsgi(+0x21abed) [0x5638f8632bed]
*** end of backtrace ***
```

My MWE flask application:

```python
import concurrent.futures

from flask import Flask

app = Flask(__name__)

@app.route("/toy", methods=["GET"])
def process_request():
    results = do_concurrent()

    if not results:
        return "Fail."

    return "Success"


def simple_task(num):
    return 1


def do_concurrent():
    dummy_list = list(range(100))
    with concurrent.futures.ThreadPoolExecutor(max_workers=10) as executor:
        results = list(executor.map(simple_task, dummy_list))

    return results
```

The uwsgi command to run flask application:

```bash
uwsgi --http 0.0.0.0:6012 --wsgi-file flask_demo.py --callable app --processes 1 --threads 1
```

The scrip to test this service:

```python
import requests


url = "http://0.0.0.0:6012/toy"

r = requests.get(url, timeout=5)
print(r)
```


When I request the service, I am seeing the above segmentation errors.


If I create a conda virtual env with python version 3.6:


```bash
conda create -n py36 -c conda-forge python==3.6.5 uwsgi icu=58 flask
```

run the service using uwsgi and request again, the error disappears.


If I do not use `concurrent.futures`, I see no errors both for python 3.7 and
python 3.6. I am not knowledgeable enough to know the exact cause of this
issue. It seems there is some compatibility issue with uwsgi, python version
and concurrent package.

# symbol not found on macOS

I am using Python 3.7 installed via Anaconda, and uWSGI is installed via pip.

When I run my simple Flask application, I see the following error:

```
ImportError: dlopen(/Users/jdhao/anaconda3/lib/python3.7/lib-dynload/math.cpython-37m-darwin.so, 2): Symbol not found: _PyExc_MemoryErrorImportError: dlopen(/Users/jdhao/anaconda3/lib/python3.7/lib-dynload/math.cpython-37m-darwin.so, 2): Symbol not found: _PyExc_MemoryError
  Referenced from: /Users/jdhao/anaconda3/lib/python3.7/lib-dynload/math.cpython-37m-darwin.so
  Expected in: flat namespace
```

This seems to be a compatibility issue between uWSGI and Anaconda python. There
are two solutions:

1. Uninstall uWSGI installed via pip and install it using conda:

    ```bash
    # uWSGI is provided by the conda-forge channel
    conda install -c conda-forge uwsgi
    ```

2. Using Python 3.6 with uWSGI. We can create a new virtual env with Python 3.6.5
and intall uWSGI inside.

    ```bash
    conda create -n py36 python==3.6.5 flask
    ```

    After that, activate the virtual env and install uWSGI inside:

    ```bash
    conda activate py36
    pip install uwsgi
    ```
