---
title: "Cropping Rotated Rectangles from Image with OpenCV"
date: 2019-02-23 01:14:26+0800
tags: [OpenCV]
categories: [Python]
---

In computer vision tasks, we need to crop a rotated rectangle from the original
image sometimes, for example, to crop a rotated text box. In this post, I would
like to introduce how to do this in OpenCV.

<!--more-->

If you search on the internet about cropping rotated rectangle, there are
several answers in the Stack Overflow which suggest using `minAreaRect()` to
find the minimum bounding rectangle, rotating the original image and finally
cropping the rectangle from the image. You can find these questions
[here](https://stackoverflow.com/questions/11627362/how-to-straighten-a-rotated-rectangle-area-of-an-image-using-opencv-in-python)
and
[here](https://stackoverflow.com/questions/37177811/crop-rectangle-returned-by-minarearect-opencv-python).
While some of the answers work, they only work in certain conditions. But if
the rotated rectangle is near the edge of the original image, some part of the
cropped rectangle is cut out in the output.

# The imperfect way

Take the following image with rotated text as an example,

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190227003207.png" width="400">
</p>

The corner points[^1] are (in top left, top right, bottom right, bottom left order):

```
[64, 49], [122, 11], [391, 326], [308, 373]
```

If you crop the rectangle using the following script (based on [this
answer](https://stackoverflow.com/a/48553593/6064933)):

<details>
<summary><font color="red">Click to see the code.</font></summary>

```python
import cv2
import numpy as np


def main():
    img = cv2.imread("big_vertical_text.jpg")
    cnt = np.array([
            [[64, 49]],
            [[122, 11]],
            [[391, 326]],
            [[308, 373]]
        ])
    # find the exact rectangle enclosing the text area
    # rect is a tuple consisting of 3 elements: the first element is the center
    # of the rectangle, the second element is the width, height, and the
    # third element is the detected rotation angle.
    # Example output: ((227.5, 187.50003051757812),
    # (94.57575225830078, 417.98736572265625), -36.982906341552734)
    rect = cv2.minAreaRect(cnt)
    print("rect: {}".format(rect))

    box = cv2.boxPoints(rect)
    box = np.int0(box)

    # print("bounding box: {}".format(box))
    cv2.drawContours(img, [box], 0, (0, 0, 255), 2)

    # img_crop will the cropped rectangle, img_rot is the rotated image
    img_crop, img_rot = crop_rect(img, rect)
    cv2.imwrite("cropped_img.jpg", img_crop)
    cv2.waitKey(0)


def crop_rect(img, rect):
    # get the parameter of the small rectangle
    center, size, angle = rect[0], rect[1], rect[2]
    center, size = tuple(map(int, center)), tuple(map(int, size))

    # get row and col num in img
    height, width = img.shape[0], img.shape[1]

    # calculate the rotation matrix
    M = cv2.getRotationMatrix2D(center, angle, 1)
    # rotate the original image
    img_rot = cv2.warpAffine(img, M, (width, height))

    # now rotated rectangle becomes vertical, and we crop it
    img_crop = cv2.getRectSubPix(img_rot, size, center)

    return img_crop, img_rot


if __name__ == "__main__":
    main()
```
</details>


In the above code, we first find the rectangle enclosing the text area based on
the four points we provide using the
[cv2.minAreaRect()](https://docs.opencv.org/3.1.0/d3/dc0/group__imgproc__shape.html#ga3d476a3417130ae5154aea421ca7ead9)
method. Then in function `crop_rect()`, we calculate a rotation matrix and
rotate the original image around the rectangle center to straighten the rotated
rectangle. Finally, the rectangle text area is cropped from the rotated image
using
[cv2.getRectSubPix](https://docs.opencv.org/3.1.0/da/d54/group__imgproc__transform.html#ga77576d06075c1a4b6ba1a608850cd614)
method.

The original, rotated and cropped image are shown below:

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190227003243.png" width="600">
</p>

We can see clearly that some parts of the text are cut out in the final result.
Of course, you can pad the image beforehand, and crop the rectangle[^2] from
the padded image, which will prevent the cutting-out effect.

# The better way

Is there a better way? Yes!

In the above code, when we want to draw the rectangle area in the image, we use
[cv2.boxPoints()](https://docs.opencv.org/3.1.0/d3/dc0/group__imgproc__shape.html#gaf78d467e024b4d7936cf9397185d2f5c)
method to get the four corner points of the real rectangle. We also know the
width and height of rectangle from `rect`. Then we can directly warp the
rectangle from the image using `cv2.warpPerspective()` function. The following
script shows an example:

<details>
<summary><font color="red">Click to see the code.</font></summary>

```python
import cv2
import numpy as np


def main():
    img = cv2.imread("big_vertical_text.jpg")
    # points for test.jpg
    cnt = np.array([
            [[64, 49]],
            [[122, 11]],
            [[391, 326]],
            [[308, 373]]
        ])
    print("shape of cnt: {}".format(cnt.shape))
    rect = cv2.minAreaRect(cnt)
    print("rect: {}".format(rect))

    # the order of the box points: bottom left, top left, top right,
    # bottom right
    box = cv2.boxPoints(rect)
    box = np.int0(box)

    print("bounding box: {}".format(box))
    cv2.drawContours(img, [box], 0, (0, 0, 255), 2)

    # get width and height of the detected rectangle
    width = int(rect[1][0])
    height = int(rect[1][1])

    src_pts = box.astype("float32")
    # coordinate of the points in box points after the rectangle has been
    # straightened
    dst_pts = np.array([[0, height-1],
                        [0, 0],
                        [width-1, 0],
                        [width-1, height-1]], dtype="float32")

    # the perspective transformation matrix
    M = cv2.getPerspectiveTransform(src_pts, dst_pts)

    # directly warp the rotated rectangle to get the straightened rectangle
    warped = cv2.warpPerspective(img, M, (width, height))

    # cv2.imwrite("crop_img.jpg", warped)
    cv2.waitKey(0)


if __name__ == "__main__":
    main()
```
</details>

Now the cropped rectangle becomes

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190227003325.png">
</p>

If you check carefully, you will notice that there are some black area in the
cropped image. That is because a small part of the detected rectangle is
out of the bound of the image. To remedy this, you may [pad the image](https://stackoverflow.com/q/36255654/6064933)
a bit and do the crop after that. An example is given
[here](https://stackoverflow.com/a/54824218/6064933) (By me :)).

# About the angle of the return rectangle

The last element of the returned `rect` is the detected angle of the rectangle.
But it has confused a lot of people, for example, see
[here](https://stackoverflow.com/q/24073127/6064933) and [here](https://stackoverflow.com/q/15956124/6064933).

This angle is in the range $[-90, 0)$. After much experiment, I have found that
the relationship between the rectangle orientation and output angle of
`minAreaRect()`. It can be summarized in the following image

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190227003358.png" width="400">
</p>

The following description assume that we have a rectangle with unequal height
and width length, i.e., it is not square.

If the rectangle lies vertically (width < height), then the detected angle is
-90. If the rectangle lies horizontally, then the detected angle is also -90
degree.

If the top part of the rectangle is in first quadrant, then the detected angle
decreases as the rectangle rotate from horizontal to vertical position, until
the detected angle becomes -90 degrees. In first quadrant, the width of
detected rectangle is longer than its height.

If the top part of the detected rectangle is in second quadrant, then the angle
decreases as the rectangle rotate from vertical to horizontal position. **But
there is a difference between second and first quadrant. If the rectangle
approaches vertical position but has not been in vertical position, its angle
approaches 0. If the rectangle approaches horizontal position but has not been
in horizontal position, its angle approaches -90 degrees**.

This post [here](https://namkeenman.wordpress.com/2015/12/18/open-cv-determine-angle-of-rotatedrect-minarearect/)
is also good at explaining this.

# Conclusion

In this post, I compared the two methods to crop the rotated rectangle from the
image and also explained the meaning of angle returned by `cv2.minAreaRect()`
method. Overall, I like the second method since it does not require rotating
the image and can deal with this problem more elegantly with less code.

# References

+ <https://docs.opencv.org/3.1.0/dd/d49/tutorial_py_contour_features.html>
- rotation angle
    + [OpenCV's RotatedRect angle does not provide enough information](https://stackoverflow.com/q/24073127/6064933).
    + [MinAreaRect angles - Unsure about the angle returned](https://stackoverflow.com/q/15956124/6064933)
    - <https://namkeenman.wordpress.com/2015/12/18/open-cv-determine-angle-of-rotatedrect-minarearect/>

[^1]: We only need to provide roughly the 4 corner points of the rectangle. Then we can use OpenCV to find the exact the rectangle enclosing the rectangle text area.
[^2]: You need to re-calculate the corner points of the rectangle after padding the original image.
