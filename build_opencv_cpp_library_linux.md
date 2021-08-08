---
title: "Building OpenCV from Source on Linux"
date: 2020-12-07T23:25:18+08:00
draft: false
tags: [C++, OpenCV]
categories: [Linux]
---

In this post, I will summarize how to build OpenCV from source, and introduce
how to use it when writing C++ programs.

<!--more-->

# Build from source

On Ubuntu, run the following command to build OpenCV from source.

```bash
sudo apt update && sudo apt install -y cmake g++ wget unzip
wget -O opencv.zip https://github.com/opencv/opencv/archive/master.zip
unzip opencv.zip
wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/master.zip
unzip opencv_contrib.zip
# Create build directory
mkdir -p opencv_build && cd opencv_build
cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo \
      -DOPENCV_EXTRA_MODULES_PATH=../opencv_contrib-master/modules \
      -DCMAKE_INSTALL_PREFIX=$HOME/local/ \
      ../opencv-master/
# using multiprocessing to compile can save a lot of time
make -j
make install
```

The package will be installed to `$HOME/local/`, with binary file go to
`$HOME/local/bin`, header file go to `$HOME/local/include/opencv4`, dynamic
shared library file go to `$HOME/local/lib`.

A speical note on header files, the structure of OpenCV header files is someting like the following:

```
# generated using tree -L 2 -d $HOME/local/include/opencv4
/home/jdhao/local/include/opencv4/
└── opencv2
    ├── calib3d
    ├── core
    ├── dnn
    ├── features2d
    ├── flann
    ├── gapi
    ├── highgui
    ├── imgcodecs
    ├── imgproc
    ├── ml
    ├── objdetect
    ├── photo
    ├── stitching
    ├── video
    └── videoio
```

The header files are grouped into subdirectories based on their functionality.

Ref:

+ https://docs.opencv.org/master/d7/d9f/tutorial_linux_install.html
+ https://docs.opencv.org/master/d0/d3d/tutorial_general_install.html
+ https://docs.opencv.org/master/db/d05/tutorial_config_reference.html

# Run a simple program utilizing OpenCV

The following program reads an image and resize it to size 300x300 using OpenCV:

```cpp
 #include "opencv2/opencv.hpp"
 #include "opencv2/core/core.hpp"
 #include "opencv2/highgui/highgui.hpp"
 #include "opencv2/imgproc/imgproc.hpp"
 #include <iostream>

 int main(){
     cv::Mat img, dst;
     img = cv::imread("wind-turbine.jpg", cv::IMREAD_COLOR);
     cv::resize(img, dst,  cv::Size(300, 300));
     std::cout << "img width and height: " << img.size().width << ", " << img.size().height << std::endl;

     cv::imwrite("resize_img.jpg", dst);

     return 0;
 }
```

In the above code, we have included some OpenCV header files. If you look at
the header file `opencv.hpp`, you will find that it actually includes other
OpenCV header files, if they are not included in your code already. So in most
cases, include `opencv.hpp` may be enough if you do not care about compilation
time (see also [here](https://docs.opencv.org/3.1.0/db/dfa/tutorial_transition_guide.html#tutorial_transition_hints_headers) and [here](https://answers.opencv.org/question/125065/should-i-include-opencv2opencvhpp/) for more discussions on this).

We can compile the above program with the following command:

```bash
clang++ -Wall \
    -I$HOME/local/include/opencv4/ \
    -L$HOME/local/lib/  \
    -lopencv_core -lopencv_imgcodecs -lopencv_imgproc \
    read_img.cc  -o read_img
```

Note that `cv::imresize()` requires `libopencv_imgproc.so` be linked against.

## Issues met when compiling

I met the following error:

> undefined references to cv::imread()

It is because if we use functions from some OpenCV module, we need to link our
C++ program against the corresponding shared object file. We can specify the
Library to link against with `-l`. For example, `-lopencv_imgproc` means to
link against `libopencv_imgproc.so`.

Ref:

+ [Undefined reference to cv::imread(cv::String const&, int)](https://stackoverflow.com/q/48687259/6064933)
+ [Issue with the linker using opencv and C++ - Undefined Reference](https://stackoverflow.com/q/57385357/6064933)
+ [Cannot get OpenCV to compile because of undefined references?](https://stackoverflow.com/q/24337932/6064933)
+ [how do we know which header file to include](https://answers.opencv.org/question/205794/how-do-i-know-which-header-file-i-should-use-for-my-opencv-c-program/)
+ Books: https://www.packtpub.com/product/learning-image-processing-with-opencv/9781783287659
