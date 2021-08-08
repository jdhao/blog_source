---
title: Sublime Text 3 使用相关问题及解决办法
date: 2017-03-19 19:01:00 +0800
tags: [Sublime-Text, encoding]
categories: [tools]
---

这篇文章集中写一些在使用 Sublime Text 3 时遇到的问题以及解决办法，某些问题在不
同系统的解决办法可能都是一样的，但是未经测试，所以使用的时候请先验证。

<!--more-->

# 打开中文文件乱码 [Windows, Linux]

如果中文文件是使用的 [GBK 等中文独有的编码方式
](https://www.zhihu.com/question/19677619)，而不是通用的 UTF-8 编码，使用
Sublime Text 打开以后会发现文件显示乱码，因为 Sublime Text 目前为止并不原生支持
GBK 等中文编码。 好在有一款 Sublime Text 的插件，可以帮助解决这个问题。 这款插
件名字叫 ["ConvertToUTF8"](https://packagecontrol.io/packages/ConvertToUTF8)，
安装好以后打开中文编码文件就可以自动转换为 UTF-8 编码，值得注意的是关闭文件以后
，这个库默认把 UTF-8 编码再转为该文件初始编码，如果不想要这种行为，可以通过设定
`"convert_on_save" : false` 来关闭这项功能。 如果不想下载这个插件，用系统自带的
记事本打开文件，然后另存为 UTF-8 编码也可以。

# 新建一个 build system，编译运行 C++ 文件[Linux, Windows]

Sublime Text 3 自带 CPP 的 build system： "C++ Single File"，但是这个 build
system 只能编译，不能直接运行编译好的程序，所以运行简单的 CPP 文件有点麻烦，编
译好以后，还需要自己去 shell 里面运行。 我们可以新建一个简单的 build system，实
现自己的需求，依次点击 "Tools-->Build System-->New Build System"，使用下面的配
置

```yaml
{
"cmd" : ["g++ -std=c++11 -Wall ${file_name} -o ${file_base_name} && ./${file_base_name}"], 
"selector" : "source.c",
"shell": true,
"working_dir" : "$file_path"
}
```

# 如何使程序直接在系统的 terminal 或者 console 运行

Sublime Text 运行需要从 standard input (一般来说就是 console) 输入数据的程序时
，该程序无法正常接收输入，如何解决这个问题呢？ 我们可以给编译 C++ 增加一个可选
的方式，Sublime Text 里面称为  "variants"，然后指定在 GUI terminal 里面运行编译
好的文件。 Linux 系统与 Windows 系统设置稍有不同。

## Linux 配置

完整的配置文件如下

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
            "shell_cmd": "g++ -std=c++11 -Wall \"${file}\" -o \"${file_path}/${file_base_name}\" && xterm -e '${file_path}/${file_base_name} && echo && echo Press ENTER to continue && read line && exit'",
           // "shell_cmd": "gnome-terminal -e 'bash -c \"${file_path}/${file_base_name}&& echo && echo Press ENTER to continue && read line && exit\"'", // for gnome-terminal 
           // "shell_cmd": "xterm -e '${file_path}/${file_base_name}; bash'", // for xterm
           // "shell_cmd": "xterm -hold -e ${file_path}/${file_base_name}", // for xterm
           // "shell_cmd": "konsole --hold -e ${file_path}/./${file_base_name}", // for konsole
            "shell": true,
        },
    ]
}
```

上述配置文件提供了针对 genome-terminal, xterm, Konsole 的配置，可以按照自己正在
使用的 GUI terminal 选择对应的配置。 上述配置还可以保证程序在运行结束以后，
terminal 的窗口不会立即关闭。 当运行需要从 console 输入数据的程序时，直接使用
`CTRL + SHIFT + B` 快捷键，然后选择 `Run in Terminal` 选项即可。

![](https://blog-resource-1257868508.file.myqcloud.com/c++_run_in_terminal.png)

## Windows 配置

以上的配置针对的 Linux 系统，如果你使用的是 Windows 系统，情况会稍有不同，下面
的配置是适用于 Windows 的配置，

```
{
    "shell_cmd": "g++ -Wall -std=c++11 \"${file}\" -o \"${file_path}/${file_base_name}.exe\"",
    "file_regex": "^(..[^:]*):([0-9]+):?([0-9]+)?:? (.*)$",
    "working_dir": "${file_path}",
    "selector": "source.c++",

    "variants":
    [
        {
            "name": "Run",
            "shell_cmd":"\"${file_path}/${file_base_name}\""
        },
        {
            "name": "Run in CMD",
            "shell_cmd":   "start cmd /k  $file_base_name "
            // "shell_cmd":   "start \"$file_base_name\" call $file_base_name"
        }
    ]
}
```

使用 `CTRL + B` 来编译源代码，然后使用 `CTRL + SHIFT + B` 来选择在 Sublime Text
的 console 运行还是直接在 Windows CMD 窗口运行，上述的配置也可以保证程序运行以
后，terminal 的窗口不会自动关闭，方便用户查看输出结果。

# 输入法图标不跟随输入光标

在 Sublime Text 中使用中文时，发现输入法的候选字的框不跟随输入光标，这个 bug 官
方没有解决，有一个插件
[IMESupport](https://packagecontrol.io/packages/IMESupport) 可以解决这个问题。

# 更改 package 的 user setting，保存的时候一直提示 "Unexpect trailing characters"[Windows, Linux]

安装了一个 package 以后，需要进行个性化设置，然后保存设置的时候一直提示错误，错
误信息如下

> Error trying to parse settings: unexpected trailing characters

这个原因是输入的 setting 不是有效的 JSON 格式，我的问题是忘记在 setting 外加大
括号了。例如下面的 setting 就是有问题的

```yaml
    // Convert when previewing file: true or false
    "preview_action" : false,
```

正确的写法应该是

```yaml
{
 // Convert when previewing file: true or false
    "preview_action" : false,
}
```

# 新建一个 snippet [Windows, Linux]

所谓 snippet，简单来说就是一个代码片段，你为这个 snippet 设立一个关键词，当你输
入关键词以后，就会激活这个 snippet，省去了重复输入的麻烦。 在编写 C++ 程序的时
候每次都需要输入头文件，using declaration，比较麻烦，可以创立一个 snippet，建立
好 CPP 文件以后输入关键词，自动填充预定义的模板。 建立方法如下，依次打开`Tools
-> Developer -> New Snippet...`, 在生成的文件里填写如下内容，

```html
<snippet>
    <content><![CDATA[
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

using std::cout;
using std::vector;
using std::string;

int main(){
    return 0;
}

]]></content>
    <!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
    <tabTrigger>cpp</tabTrigger>
    <!-- Optional: Set a scope to limit where the snippet will trigger -->
    <!-- <scope>source.python</scope> -->
</snippet>
```

然后给创建的 snippet 起一个名字，如 `C++_default_template.sublime-snippet`, 然
后在文件中输入 cpp，再点击<kbd>Tab</kbd>，就会直接输入对应的 snippet。

# 如何编辑 Sublime Text 自带的 build system [Windows, Linux]

在 Sublime Text 中，package 都是以 `.sublime-package` 的压缩文件形式存在，以
C++ build system 为例，如果要编辑 Sublime Text 自带的 C++ build system，首先要
安装 [PackageResourceViewer](https://github.com/skuroda/PackageResourceViewer)
，然后使用 <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd>，打开 Package Control
的窗口，输入 `prv`, 选择 `Open Resource`，然后输入 `C++`，向下移动光标，选择
`C++ Single File.sublime-build`，打开这个文件，按照自己意愿编辑，编辑以后保存就
可以了。

# 使用 Package Control 安装插件的时候提示 “There are no packages available for installation”[Windows, Linux]

问题如题所示，重装 Package Control 解决了这个问题。 首先，[卸载 Package
Control](https://packagecontrol.io/docs/troubleshooting#Purging_and_Reinstalling)
，然后，[重新安装 Package Control](https://packagecontrol.io/installation#st3)
。

# 使用 Ctrl + backtick 无法打开 console[Windows, Linux]

Sublime Text 默认打开 console 的快捷键 (shortcut) 是
<kbd>Ctrl</kbd>+<kbd>\`</kbd>，但是在我的系统上，按这个组合键，没有任何反应，[
根据 Stackoverflow 上的回答
](https://stackoverflow.com/questions/13965877/the-shortcut-key-to-open-console-in-sublime-text-2-does-not-work)
， 点击打开 console，然后输入 `sublime.log_input(True)`, 监听按键，发现按下
<kbd>Ctrl</kbd>+<kbd>\`</kbd> 时，Sublime 并未记录到按键。于是根据别人的答案，
自己设置了另外一个快捷键组合 <kbd>Ctrl</kbd>+<kbd>'</kbd> 来打开 console, 在自
己的 user key binding 设置里面加上一项：

```yaml
    { "keys": ["ctrl+'"], "command": "show_panel", "args": {"panel": "console", "toggle": true} },
```


# 参考

+ [Sublime Text 解决中文编码问题](http://www.zhanglian2010.cn/2016/11/sublime-text-3-convert-to-utf8/)
+ [示例 build system](https://gist.github.com/xhacker/1eb3f87dd81a3c2015e7)
+ [build system 中一些关键词还有内置变量含义](https://goo.gl/zYPUio)
+ [解决输入法不跟随光标问题](https://www.kancloud.cn/digest/sublime-text-complete-guide/61426)
+ [Unexpected trailling characters  解决办法](https://goo.gl/5076f9)
+ [snippet 的文档](http://docs.sublimetext.info/en/latest/extensibility/snippets.html?highlight=snippet).
+ [Windows，如何使程序运行后窗口不自动关闭](https://superuser.com/a/1012167/736190)
+ [Windows，防止程序运行后窗口自动关闭](https://superuser.com/a/427981/736190)
+ [Windows，防止窗口关闭另外一种方法](https://stackoverflow.com/a/39882361/6064933)
