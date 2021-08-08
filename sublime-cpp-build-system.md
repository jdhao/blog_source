---
title: "A Complete Guide on Writing and Building C++ Programs in Sublime Text"
date: 2018-03-28 21:54:23 +0800
tags: [C++, Sublime-Text, Clang]
categories: [technique]
---

In this post, I will show how to write and run simple C++ programs in [Sublime
Text 3](https://www.sublimetext.com/).

<!--more-->

# Writing source files

The following two part are optional. They are intended to increase your speed
of writing C++ programs. If you do not need them, just skip and read how to
build and run C++ source code.

## Code snippet

When you are writing code, you may want to create a snippet so that your
keywords will be expanded to a template to reduce your typing. For example, we
often need to write codes that output something and then output a new line
character. Writing this boiler-plate code over and over again is boring. We can
create snippet for it.

It is easy to create a code snippet in Sublime Text. Go to `Tools -> Developer`
and click `New Snippet...` (see image below)

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-28/95195253.jpg" width="600">
</p>

In the created tab, use the following settings:

```xml
<snippet>
    <content><![CDATA[
cout <<  << '\n';
]]></content>
    <!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
    <tabTrigger>cout</tabTrigger>
    <!-- Optional: Set a scope to limit where the snippet will trigger -->
    <scope>source.c++</scope>
    <decription>Insert a cout with newline</decription>
</snippet>
```

Save this snippet and give it a name, e.g., `cout_oneline.sublime-snippet`. All
snippets in Sublime Text end with the extension `sublime-snippet`.

When you have created this snippet, you can just type `cout` in the C++ source
file and press <kbd>Tab</kbd> key to trigger the snippet (see the below image).

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-28/2638028.jpg" width="600">
</p>

## Auto-completion

If you want to auto-complete your code, you can use [EasyClangComplete](https://github.com/niosus/EasyClangComplete).
You can install this package via [Package Control](https://packagecontrol.io/installation).
Then you need to [install `clang` for your system](https://github.com/niosus/EasyClangComplete#2-install-clang).

The package offers several ways to configure it. I will just demonstrate one
way: configure it via setting file. Open the setting file of `EasyClangComplete`
(see image below):

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-28/17294862.jpg" width="400">
</p>

Example settings are shown below:

```yaml
{
 "common_flags" : [
    // some example includes
    "-I/usr/include",
    "-I$project_base_path/src",

    //include search path of gcc
    "-I/usr/lib/gcc/x86_64-redhat-linux/4.8.5/../../../../include/c++/4.8.5",
    "-I/usr/lib/gcc/x86_64-redhat-linux/4.8.5/../../../../include/c++/4.8.5/x86_64-redhat-linux",
    "-I/usr/lib/gcc/x86_64-redhat-linux/4.8.5/../../../../include/c++/4.8.5/backward",
    "-I/usr/lib/gcc/x86_64-redhat-linux/4.8.5/include",
    "-I/usr/local/include",

    // this is needed to include the correct headers for clang
    "-I/usr/local/lib/clang/4.0.1/include",
  ],
  "cpp_flags" : [
    "-std=c++11",
    "-Wall"
  ],
  "errors_style": "phantoms",
  "hide_default_completions": true,
  "progress_style": "ColorSublime",
  "show_type_info": false,
}
```

In the above settings, the include search paths of gcc can be found using the
following command:

```bash
`gcc -print-prog-name=cc1plus` -v
```

The output is something like the following (use the paths in the green box):

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-28/87002887.jpg" width="600">
</p>

Now you should see that the auto-completion works.

# Compile and run C++ code

In order to compile C++ source files. You have to make sure that a C++ compiler
is installed in your system. For Linux system, [`GCC`](https://gcc.gnu.org/) is
often installed by default. So further configuration is not needed. For
Windows, there is a port of GCC named
[MinGW-w64](https://sourceforge.net/projects/mingw-w64/). Download the on-line
installer and install it. It will install all the component necessary to
compile C++ source. After installation, you should add the directory of
executables to the Windows `PATH`. On my Windows system, the executable
directory is

```
C:\Program Files\mingw-w64\x86_64-7.3.0-posix-seh-rt_v5-rev0\mingw64\bin
```

You may need to reboot your system to make the PATH change take effect.

Sublime Text has its own build system for C++. Unfortunately, it has at least
one drawback: it can not run the executables in the system console (so if you
want to run C++ program that need standard input from terminal, you will fail).
We can write our own build system fairly easily.

## Simple build system to compile and run source code

After finishing writing the code, we now compile the code. We can build our own
custom build system. Go to `Tools –> Build System –> New Build System...`. Use
the following settings (I assume that you are using GCC to compile your code):

```yaml
{
"cmd" : ["g++ -std=c++11 -Wall ${file_name} -o ${file_base_name} && ./${file_base_name}"],
"selector" : "source.c",
"shell": true,
"working_dir" : "$file_path"
}
```

Save it and name it `C++.sublime-build` (build systems in Sublime Text all end
with the extention `sublime-build`). Press
<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>B</kbd> to select the build system.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-28/32186424.jpg" width="600">
</p>

Select the `C++` build system we have just created and press <kbd>Enter</kbd>.
Then the source file will be compiled and run. If everything goes well, you
will see the output of this program in the bottom of Sublime Text window.

## How to run the executable right in the system terminal or console

If you are executing a program which needs input from the standard input (often
it is just the console), the program can not receive input due to the limit of
Sublime Text. How to solve this issue? It is easy. We can add a [`variants`](http://docs.sublimetext.info/en/latest/reference/build_systems/configuration.html#variants)
to the build system we just created and run the executable file in the system
GUI terminal. Different systems differ in their specific settings.

Here is an example setting:

```yaml
{
    "shell_cmd": "g++ -std=c++11 -Wall \"${file}\" -o \"${file_path}/${file_base_name}\" && \"${file_path}/${file_base_name}\"",
    "file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
    "working_dir": "${file_path}",
    "selector": "source.c++, source.cpp, source.cc, source.cxx",

    "variants":
    [
        {
            "name": "Run in Terminal",

            "linux": {
                "shell_cmd": "g++ -std=c++11 -Wall \"${file}\" -o \"${file_path}/${file_base_name}\" && xterm -e '${file_path}/${file_base_name} && echo && echo Press ENTER to continue && read line && exit'",
                // "shell_cmd": "g++ -std=c++11 -Wall \"${file}\" -o \"${file_path}/${file_base_name}\" && gnome-terminal -e 'bash -c \"${file_path}/${file_base_name}&& echo && echo Press ENTER to continue && read line && exit\"'", // for gnome-terminal
                // "shell_cmd": "g++ -std=c++11 -Wall \"${file}\" -o \"${file_path}/${file_base_name}\" && xterm -e '${file_path}/${file_base_name}; bash'", // for xterm
                // "shell_cmd": "g++ -std=c++11 -Wall \"${file}\" -o \"${file_path}/${file_base_name}\" && xterm -hold -e ${file_path}/${file_base_name}", // for xterm
                // "shell_cmd": "g++ -std=c++11 -Wall \"${file}\" -o \"${file_path}/${file_base_name}\" && konsole --hold -e ${file_path}/./${file_base_name}", // for konsole

            },

            "windows":{
                "shell_cmd":   "g++ -std=c++11 -Wall \"${file}\" -o \"${file_path}/${file_base_name}\" && start cmd /k  $file_base_name "
                // "shell_cmd":   "g++ -std=c++11 -Wall \"${file}\" -o \"${file_path}/${file_base_name}\" && start \"$file_base_name\" call $file_base_name"
            },

            "osx":{
                "shell_cmd": "g++ -std=c++11 -Wall \"${file}\" -o \"${file_path}/${file_base_name}\" && xterm -e '${file_path}/${file_base_name} && echo && echo Press ENTER to continue && read line && exit'",
            },

            "shell": true,
        },
    ]
}
```

The above setting file provides settings for different systems. For Linux,
options for GUI terminals such as *genome-terminal*, *xterm* and *Konsole* are
provided. You can uncomment corresponding line to set up for your
currently-used GUI terminal. The above settings will ensure that the GUI
terminal window will not be closed immediately after running your program (so
that you can examine the result of the program). For Windows, we use the
default CMD. For OSX, we use the `xterm`.

When you want to compile and run files which need input from the console, you
can use the build variants `Run in Terminal`. To use it, press
<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>B</kbd> to bring up the command plate,
and choose option `Run in Terminal` (see image below).

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/18-3-28/8942560.jpg" width="600">
</p>

---

# References

+ [Sublime Text build system overview](http://docs.sublimetext.info/en/latest/reference/build_systems/configuration.html#overview)
+ [Sublime Text snippet documentation](http://docs.sublimetext.info/en/latest/extensibility/snippets.html)
+ [Windows, prevent the window from closing after program execution, solution 1](https://superuser.com/a/1012167/736190)
+ [Same as above, another solution](https://stackoverflow.com/a/39882361/6064933)
