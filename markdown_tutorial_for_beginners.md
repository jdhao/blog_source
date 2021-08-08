---
title: "写给初学者的 Markdown 教程"
date: 2018-09-21 16:59:00 +0800
tags: [Markdown]
categories: [technique]
---

这是一篇帮助初学者快速入门 Markdown 的文章，看完此篇，你应该就可以使用 Markdown
写出格式规范的文章了。

<!--more-->

# 什么是 Markdown

Markdown 是一种可标记语言，通过简单的标记，可以将纯文本内容转为内容丰富的 HTML
网页，实现字体加粗，链接，插入图片等等纯文本无法实现的功能。

Markdown 类似编程语言，需要先编写，然后通过渲染器把 Markdown 源文件转为内容丰富
的 HTML 网页（类似于编程语言的编译器，把一种语言翻译为另外一种语言）。

## Markdown 优点

采用 Markdown 生成的文档，格式规范，清晰明了，避免了纯文本文件无法对文本格式进
行控制的尴尬，是书写技术文档的最佳选择之一。同时，Markdown 又不像网页那样复杂，
仅仅使用简单的语法，就能生成丰富的内容，这也是 Markdown 流行的原因。

# Markdown 具体语法

本部分介绍常用的 Markdown 语法，由于 Markdown 类似编程语言，因此对书写格式有一
定要求，符合要求才能生成想要的结果，书写时候一定要遵循规范。

## 标题（heading）

Markdown 中的标题采用 `#` 来标志，`#` 后面跟具体的标题文本。`#` 的个数代表标题
级别：1 个 `#` 代表一级标题，2 个 `#` 代表二级标题，以此类推。

### 注意点

+ `#` 与标题的文本之间，需要留一个空格，否则可能无法正常渲染为标题样式
+ 标题级别不要太多，五六级标题与正文文本字体大小区别已经不大，因此意义也不大

### 效果展示与源代码

源代码：

```
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```

生成的标题效果为：

![markdown_heading.png](https://i.loli.net/2018/09/21/5ba4a55b544a1.png)

## 列表

Markdown 中的列表，分为有序列表以及无序列表两种。

+ 对于有序列表，使用数字开头，后面跟 `.`（英文的句号），空格，后面写上该步骤内容。
+ 对于无序列表，使用 `+` 或者 `-` 开头，空格，后面写具体内容。

你也可以创建多级嵌套列表，在某一级别下另起一行，缩进四个空格开始更低级别。

### 注意点

+ 列表项前面需要空一行，否则某些 Markdown 渲染器无法正常识别列表
+ **列表标志后面需空格**


### 效果展示与源代码

源代码：

```
1. 有序列表，这是步骤一
    1. 步骤 1.1
        1. 步骤 1.1.1
        2. 步骤 1.1.2
        3. 步骤 1.1.3
    2. 步骤 1.2
    3. 步骤 1.3
2. 有序列表，这是步骤二
    1. 步骤 2.1
    2. 步骤 2.2
3. 有序列表，这是步骤三

+ 车
    + 汽车
        + BMW
        + BYD
    + 火车
        + 普通列车
        + 动车
        + 高铁
    + 自行车
+ 动物
    + 猴子
    + 骆驼
    + 大象
+ 人
    + 白人
    + 黑人
        + 非洲黑人
        + 美洲黑人
        + 亚洲黑人
    + 黄人
```

生成的列表效果如下图：

![markdown_list.png](https://i.loli.net/2018/09/21/5ba4a59f0e643.png)

## 强调与着重表示

要使用*斜体*，可以使用一对 `*` 或者 `_`包围要斜体的文字；要**加粗文字**，使用两对 `*` 或者 `_` 包围文字。

如何既斜体又加粗文字？`*` 与 `_` 交替使用即可，*__像这样__* ，或者 _**这样**_ 。

以上文字源代码为：

```
要使用*斜体*，可以使用一对 `*` 或者 `_`包围要斜体的文字；要**加粗文字**，使用两对 `*` 或者 `_` 包围文字。

如何既斜体又加粗文字？`*` 与 `_` 交替使用即可，*__像这样__* ，或者 _**这样**_ 。
```

### 注意点

+ 使用 `_` 进行斜体或者着重表示，可能需要和周围文字之间加上空格，否则无法正常渲
染，建议使用 `*`

## 链接

可以给文字加上链接，链接的格式为 `[]()`，方括号内填写希望显示的文字，圆括号内填
写该文字具体链接的网页地址。

例如，我们不可以访问[谷歌](https://www.google.com/)和
[YouTube](https://www.youtube.com)。直接书写网页地址也可以，用 `<>` 包含网页，
像这样，<https://www.google.com/>。

```
可以给文字加上链接，链接的格式为 `[]()`，方括号内填写希望显示的文字，圆括号内填写该文字具体链接的网页地址。

例如，我们不可以访问[谷歌](https://www.google.com/)和 [YouTube](https://www.youtube.com)。直接书写网页地址也可以，
用 `<>` 包含网页，像这样，<https://www.google.com/>。
```

## 代码块与代码段

Markdown 中可以写 inline code 以及 code block，inline code 使用一对 `` ` `` （
backtick 键）包含，`sell = np.sin(time)`。code block 使用三个 `` ` ``  开始与结
束。

```
import matplotlib
matplotlib.use('agg')
import matplotlib.pyplot as plt
import numpy as np

print("hello")
for i in range(10):

    print(i)

x = np.linspace(0, 4, 100)
y = np.sin(x)
fig, ax = plt.subplots()

ax.plot(x, y)
plt.savefig('test.png')
```

上述代码块源代码为

``````
```
import matplotlib
matplotlib.use('agg')
import matplotlib.pyplot as plt
import numpy as np

print("hello")
for i in range(10):

    print(i)

x = np.linspace(0, 4, 100)
y = np.sin(x)
fig, ax = plt.subplots()

ax.plot(x, y)
plt.savefig('test.png')
```
``````

## 图片

可以在 Markdown 中使用图片，格式为 `！[Image_caption](Image_link)`。有两种方式
来引用图片：

1. 本地图片：直接写上图片绝对路径或者相对当前 Markdown 文件的相对路径
2. 网络图片：直接写上图片链接即可

![这是一张网络图片](https://assets-cdn.github.com/images/modules/logos_page/Octocat.png)

以上图片对应的源代码如下：

```
![这是一张网络图片](https://assets-cdn.github.com/images/modules/logos_page/Octocat.png)
```

### 注意点

如果图片的链接为本地路径，Markdown 文件移动到其它地方，或者分享给他人以后，图片
将不能显示。可以使用一些[图床](https://www.v2ex.com/t/380503)[^1]来存放图片，保
证在哪里都能显示图片。或者，尽量不在 Markdown 里使用图片。

## 表格

Markdown 中也可以书写简单的表格：

|姓名|数学|语文|
|---|---|---|
|张三| 1| 2 |
|李四| 3| 4 |

生成上述表格的源代码为：

```
|姓名|数学|语文|
|---|---|---|
|张三| 1| 2 |
|李四| 3| 4 |
```

自己手写一个表格比较麻烦和枯燥，可以利用在线的工具，生成 Markdown 格式的表格，
一个不错的网站是
[TableGenerator](https://www.tablesgenerator.com/markdown_tables).

## 任务列表

某些种类的 Markdown 还支持任务列表语法，例如：

- [x] 吃饭
- [] 睡觉
- [x] 打怪兽
- [] 写作业
- [] 看电影

生成上述任务列表的源代码如下：

```
- [x] 吃饭
- [ ] 睡觉
- [x] 打怪兽
- [ ] 写作业
- [ ] 看电影
```

`[]` 中加上 `x` 表示某个任务已经完成，否则表示该任务进行中。

# Markdown 源文件编辑与预览

推荐几款编辑与预览的编辑器，VNote，Sublime Text 与 Visual Stuido Code

## Vnote

[Vnote](https://github.com/tamlok/vnote) 是国人开发的 Markdown 笔记软件，专为
Markdown 设计，可以方便编写 Markdown 文件，进行预览，对文件进行管理等。

## Visual Stuido Code

安装 Visual Studio Code，VScode 原生支持 Markdown 文件预览，无需安装插件，按
<kbd>Ctrl</kbd>+<kbd>K</kbd>，再按 <kbd>V</kbd>，会在当前编辑窗口的右边打开文件
预览窗口，可以实时查看生成的 HTML 文件效果。更多指导，请查看 [官方文档
](https://code.visualstudio.com/docs/languages/markdown)。

## Sublime Text

安装 Sublime Text，并且安装插件 [Markdown
Preview](https://github.com/facelessuser/MarkdownPreview)。

写好 Markdown 以后，直接使用快捷键
<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>M</kbd> 打开浏览器预览生成的 HTML 网页效
果。

# 参考资料

+ Markdown cheatsheet：https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#links
+ Markdown 官方文档：https://daringfireball.net/projects/markdown/

[^1]:所谓“图床”，简单来说，就是存储图片的网络空间，这样不管身在何处，只要有网络，就可以看到图片。
