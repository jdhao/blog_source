---
title: "Build Web API with Flask -- Post and Receive Image"
date: 2020-04-12T20:11:52+08:00
tags: [Flask, requests, HTTP]
categories: [Python]
---

In this post, I want to write about how to build a simple image processing web
API that returns the size of an image. The topics include how to build this web
API with Flask and how to post image to this web API and get response.

<!--more-->

There are mainly two ways by which we can send an image to the web service.
Based on how you send the image, the way to get the uploaded image on the
server side also varies.

# Post binary image

You can directly post binary image to the server using the `files` parameter of
`requests.post()`:

```python
url = 'http://127.0.0.1:5000/im_size'
my_img = {'image': open('test.jpg', 'rb')}
r = requests.post(url, files=my_img)

# convert server response into JSON format.
print(r.json())
```

In the above code, the `Content-Type` of the Header of the POST request will be
`multipart/form-data`. Then in the server side, you can get the posted image
like this:

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route("/im_size", methods=["POST"])
def process_image():
    file = request.files['image']
    # Read the image via file.stream
    img = Image.open(file.stream)

    return jsonify({'msg': 'success', 'size': [img.width, img.height]})


if __name__ == "__main__":
    app.run(debug=True)
```

In the server side, the posted image will be in `request.files['image']`, which is
of type [`werkzeug.datastructures.FileStorage`](https://werkzeug.palletsprojects.com/en/1.0.x/datastructures/#werkzeug.datastructures.FileStorage).
You can save the image to disk via [`save()`](https://werkzeug.palletsprojects.com/en/1.0.x/datastructures/#werkzeug.datastructures.FileStorage.save)
method of this object:

```python
file.save('im-received.jpg')
```

The image is also stored in `file.stream`, which is a file-like object so that
you can easily read the image for later processing:

```python
# img is PIL Image object
img = Image.open(file.stream)
```

Finally, we construct a Python dict and convert it to JSON format via the
`jsonify()` method provided by Flask.

## How to post multiple files

To post multiple images to the server, you can post a list of file tuples like
the following:

```python
multiple_files = [
    ('image', ('test.jpg', open('test.jpg', 'rb'))),
    ('image', ('test.jpg', open('test.jpg', 'rb')))
]
# simplified form
# multiple_files = [
#     ('image', open('test.jpg', 'rb')),
#     ('image', open('test.jpg', 'rb'))
# ]
r = requests.post(url, files=multiple_files, data=data)
```

In the server side, you can still receive the posted images using
`request.files`:

```python
from flask import Flask, request
# ... other code
files = request.files.to_dict(flat=False) ## files is a list containing two images.
for i, file in enumerate(files):
    file.save(f'image-{i}.jpg')
```

## Post additional data

We usually want to post more meta info than merely the image itself. We can use
the `data` parameter in `requests.post()`:

```python
payload = {'id': '123', 'type': 'jpg'}
r = requests.post(url, files=files, data=payload)
```

To get the `payload` dict in the server side, we use `request.form`, which is
of type [`werkzeug.datastructures.ImmutableMultiDict`](https://werkzeug.palletsprojects.com/en/0.16.x/datastructures/#werkzeug.datastructures.ImmutableMultiDict).
We use `request.form.to_dict()` to convert the received form data into Python
dict:

```python
payload = request.form.to_dict()
id = payload['id']
im_type = payload['type']
```

# Post base64 encoded image

Another way is to just encode the image with base64 and post all the info via
`data` parameter in `requests.post()` in the client side.

```python
import base64

with open('test.jpg', 'rb') as f:
    im_b64 = base64.b64encode(f.read())

payload = {'id': '123', 'type': 'jpg', 'box': [0, 0, 100, 100], 'image': im_b64}
```

In the server side, we fetch the posted payload, get the base64 encoded image
and decode it:

```python
import base64
import io
from PIL import Image

payload = request.form.to_dict(flat=False)

im_b64 = payload['image'][0]  # remember that now each key corresponds to list.
# see https://jdhao.github.io/2020/03/17/base64_opencv_pil_image_conversion/
# for more info on how to convert base64 image to PIL Image object.
im_binary = base64.b64decode(im_b64)
buf = io.BytesIO(im_binary)
img = Image.open(buf)
```

## Post multiple based64 encoded image

To post multiple base64 encoded images to the server, post them as a list of
base64 string:

```python
b64_ims = []
for im_path in im_paths:
    with open(im_path, 'rb') as f:
        im_b64 = base64.encode(f.read())
    b64_ims.append(im_b64)
payload = {"images": b64_ims}
```

In the server side, you can get the posted dict and decode the base64 image one
by one, just like what I have shown above for a single image.

# References #

+ [Upload image in flask](https://stackoverflow.com/q/44926465/6064933).
+ [Send file and JSON in single request](https://stackoverflow.com/q/24642040/6064933).
+ [requests.post() method doc](https://2.python-requests.org/en/master/api/#requests.post).
+ [Post file with requests](https://2.python-requests.org/en/master/user/quickstart/#post-a-multipart-encoded-file).
+ [Get data received in Flask](https://stackoverflow.com/q/10434599/6064933).
+ [Post multipart files](https://2.python-requests.org/en/master/user/advanced/#multipart).
