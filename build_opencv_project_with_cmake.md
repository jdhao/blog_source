---
title: "Build C++ Project with CMake -- A Simple Example"
date: 2020-12-12T11:07:21+08:00
draft: false
tags: [C++, CMake, OpenCV]
categories: [Programming]
---

For small projects, people tend to directly invoke the compiler with the
suitable options to build the program. While it is feasible, it is tedious to
type those compilation options and flags. For projects with multiple source
files and header files, it becomes inefficient to build by hand. On Linux,
people may write [Makefile](http://web.mit.edu/gnu/doc/html/make_2.html) and
build with [make](https://www.gnu.org/software/make/). On Windows, people may
use Visual Studio for writing and build their project.

<!--more-->

[CMake](https://cmake.org/home/) is a meta build tool. It can generate build
files for various build tools such as make, [Ninja](https://ninja-build.org/),
Visual Studio. So using CMake is way to make sure that our programs can build
across platforms and build tools. Some famous open source projects choose CMake
as their build tools. These projects include [Neovim](https://github.com/neovim/neovim), [OpenCV](https://docs.opencv.org/master/index.html), [MySQL](https://github.com/mysql/mysql-server).

In post, I will share how to build a simple C++ program using CMake.


# The source file

First, create a project directory, add the following source file
`write_text.cc`. It uses the OpenCV library to read an image, write some text
to image and save it on the disk.

```cpp
#include "opencv2/opencv.hpp"

int main() {

    cv::Mat img = cv::imread("wind-turbine.jpg", cv::IMREAD_COLOR);
    cv::putText(img, "Test text", cv::Point(100, 100), cv::FONT_ITALIC, 2.0,
                cv::Scalar(0, 0, 255), 2);
    cv::imwrite("text_img.jpg", img);

    return 0;
}
```

# Create Cmake file CMakeLists.txt

Under project root, create a file named `CMakeLists.txt`. This file describes
how we want to build our project in the language of CMake. The content of is:

```cmake
cmake_minimum_required(VERSION 3.10)

project(opencv_demo)

add_executable(write_text write_text.cc)

set(OPENCV_INCLUDE_DIR /home/jdhao/local/include/opencv4/)
set(OPENCV_LIB_DIR /home/jdhao/local/lib/)

message(STATUS "OpenCV library path: ${OPENCV_LIB_DIR}")

# set include directory
target_include_directories(write_text PUBLIC "${OPENCV_INCLUDE_DIR}")

# set library directory
target_link_directories(write_text PUBLIC "${OPENCV_LIB_DIR}")

# link specific object files we need
target_link_libraries(write_text opencv_imgcodecs opencv_core opencv_imgproc)
```

# Build the Project

It is best to create a build directory for building the project so that we do
not pollute the source directory.

```bash
mkdir build
# use the following command to build if you are in project root
cmake -Bbuild -DCMAKE_BUILD_TYPE=Release

# or use the following command if you are in build directory
# cmake ../ -DCMAKE_BUILD_TYPE=Release
```

By default, cmake will generate a build file for `make` on Linux systems. If
you want to generate build file for other tools such as ninja, you can use
`-G`. For example, to generate build file for ninja, use the following command:

```bash
mkdir build_ninja
cmake -Bbuild_ninja -G "Ninja"
```

The option `-DCMAKE_BUILD_TYPE=Release` specify the [build type](https://stackoverflow.com/q/48754619/6064933).
We can also use `Debug` or `RelWithDebInfo` etc.

To build the project, we can use `cmake --build build` if we are in project
root or `cmake --build .` if we are in directory `build`. If you generate
`Makefile` for make, you can also use `make -C build` to build the project
directly, or use `ninja -C build_ninja` if you generate the build file for
ninja. The advantage of using command `cmake --build` is consistency. You do
not need to worry which specific build tool to invoke. CMake will figure it out
for you.

# Generate complilation databases

If you happen to use ccls for code completion, you can also generate a
compilation databases file named `compile_commands.json` for ccls to use, with
the help of cmake. With the help of this file, ccls can provide code completion
and code analysis for your project.

To produce this file, use the following comamnd:

```bash
cmake -Bbuild -DCMAKE_BUILD_TYPE=Debug -DCMAKE_EXPORT_COMPILE_COMMANDS=YES
ln -s build/compile_commands .
```

# Ref

+ [Doc on CMAKE_EXPORT_COMPILE_COMMANDS](https://cmake.org/cmake/help/latest/variable/CMAKE_EXPORT_COMPILE_COMMANDS.html)
+ https://github.com/MaskRay/ccls/wiki/Project-Setup#compile_commandsjson
