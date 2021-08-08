---
title: "最新版 Rime 输入法使用"
date: 2019-02-18 00:32:40+0800
tags: [Windows, Mac]
categories: [tools]
---

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190217233344.png" width="800">
</p>


之前一直听说过 [Rime](https://rime.im/) 这个神级输入法的大名，却一直迟迟没有试用，一方面现有的各种输入法用着可以，另外，对于 Rime 没有 GUI 界面的配置感觉发怵。之前一直使用的是 QQ 输入法，最近一次升级，却造成 [Chrome 浏览器频频崩溃](https://www.v2ex.com/t/535393)，是可忍孰不可忍，正好趁此机会卸载 QQ 输入法，试试 Rime 了。关于 Rime 的配置，网上能够找到的资料实在是过于丰富，为了使文章不过于冗长，一些简单的设置我将不再重复。

<!--more-->

# 用户文件夹下如何配置

网上的很多教程教用户直接在 [用户文件夹](https://github.com/rime/home/wiki/UserData#%E7%94%A8%E6%88%B6%E6%96%87%E4%BB%B6%E5%A4%BE)的 build 目录下修改 `default.yaml` ,`输入方案.schema.yaml`（这里 `输入方案` 只是某种具体方案的代称）, `weasel.yaml` 等文件，这是不正确的方式，因为 Rime 升级时，这些文件会被覆盖。

正确的方式是，在用户文件夹下建立 `default.custom.yaml` 和 `输入方案.custom.yaml`, `weasel.custom.yaml` 文件，这些文件相当于是补丁，部署以后会更新到默认的配置里面。[定制指南](https://github.com/rime/home/wiki/CustomizationGuide#%E5%AE%9A%E8%A3%BD%E6%8C%87%E5%8D%97)里面也有明确提到。

# 添加新的颜色主题

Rime 中，输入法的显示效果称为「颜色主题」(color scheme)。在网上看到一个有趣的主题，如何添加到 Rime 中使用呢？以 Windows 平台为例，假如我们要加入[这里](https://zhuanlan.zhihu.com/p/28516904)提供的「丹青」主题，在系统 custom 设置文件 (即 `weasel.custom.yaml`) 中加入以下配置即可：

```yaml
"preset_color_schemes/tantsing":  # 在配色方案列表裏加入標識爲 tantsing 的新方案
    author: Mijiag
    back_color: 0xE3E3E3
    border_color: 0x000000
    candidate_text_color: 0x000000
    comment_text_color: 0x474747
    hilited_back_color: 0x1A0791
    hilited_candidate_back_color: 0x6F0B73
    hilited_candidate_text_color: 0xE3E3E3
    hilited_text_color: 0xE3E3E3
    name: "丹青／Tantsing"
    text_color: 0x000000
```

引用该主题的时候，使用名字 `tantsing`，效果如下：

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190217233503.png" width="200">
</p>

如果要自己制作 Rime 主题，[「Rime 西米」](https://gjrobert.github.io/Rime-See-Me-squirrel/)是一款网页端工具，可以辅助制作，实时渲染颜色，便于查看最终显示效果。[这里](https://github.com/rime-aca/color_schemes)有一些预定义的主题可供使用。

# 符号自定义

首先从 Rime 安装根目录下的 `data` 子目录拷贝 `symbols.yaml` 文件到 Rime 用户目录[^1]（安装 Rime 以后，我的用户目录并没有像网上教程所说的包含 `symbols.yaml`文件），并且把该文件重命名为 `symbols.custom.yaml`。

然后仿照里面的例子，我们就可以自定义自己的符号了，例如，下面是我增加的一些符号：

```yaml
# 个人常用信息
    '/gr': [jdhao@hotmail.com, 132******68, jdhao]
# 快速输入勾和叉
    '/gc': [✓, 🗹, ✗, ☒]
```

然后，在新建的 `luna_pinyin_simp.custom.yaml` (`luna_pinyin_simp` 是沧月简体输入方案的代号)里面，加入下面的设置即可：

```yaml
patch:
  # 引用 `symbols.custom` 文件里面的符号
  'punctuator/import_preset': symbols.custom
  'recognizer/patterns/punct': "^/([a-z]+|[0-9])$"
```

然后在中文模式下，输入 `/gc`，就会显示出自定义的符号，输入对应的数字，符号就会上屏。

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190217233600.png" width="200">
</p>

## 输入直角引号

有的人喜欢在写作的时候使用直角引号：`「」`和`『』`，Rime 在 `symbols.yaml` 里面，已经提供了这个功能，不过是在 `[` 和 `]` 按键上提供的，中文输入法状态下，按下`[` (`]`)，返回的结果中包含 `「` (`」`)，如果按住 <tab>Shift</tab> 键，按下 `[` (`]`)，返回的结果中包含 `『` (`』`)。

但是因为这毕竟是引号，最好是按下引号的时候，能够输出直角引号，在`symbols.custom.yaml` 中，找到 `punctuator` 部分下的 `hafl_shape` (半角的意思)下面，

把下面的映射

```
    '''' : { pair: [ '‘', '’' ] }
    '"' : { pair: [ '“', '”' ] }
```

改成

```
    '''' : { pair: [ '『', '』' ] }
    '"' : { pair: [ '「', '」' ] }
```

这样就可以直接输入直角引号了，另外一种方式就是后文将要提到的[在词典中添加直角引号映射](#vert_quote_char)。

# 给沧月简体增加词典

在[这里](https://github.com/rime-aca/dictionaries)可以下载用于沧月拼音方案的词库，只下载下面几个文件即可：

+ `luna_pinyin.cn_en.dict.yaml`
+ `luna_pinyin.extended.dict.yaml`
+ `luna_pinyin.hanyu.dict.yaml`
+ `luna_pinyin.poetry.dict.yaml`

这几个文件都是一些词典文件，然后我们可以仿照这些词典文件建立自己额外的词典，增加自己的词汇。例如，建立名为 `jdhao.dict.yaml` 的文件，然后添加几个自己常用的词汇，文件内容如下：

```yaml
# jdhao.dict.yaml 文件内容
name: jdhao
version: "2019-02-16"
sort: by_weight
use_preset_vocabulary: true
import_tables:
  - luna_pinyin
  - luna_pinyin.extended
  - luna_pinyin.hanyu
  - luna_pinyin.poetry
  - luna_pinyin.cn_en
...

GitHub	github	100
Stack Overflow	so	1000
```

上述文件中，我们通过 `import_tables` 把刚才下载的所有词典还有 luna 本身的词典都给包含进来了，然后在下面再加上我们自己建立的词汇。

## 词典的格式

词典的格式为: `词汇<Tab>编码<Tab>词频`，各个项目之间**必须用 Tab（也就是制表符）分割**，刚开始我不知道，结果使用了空格，自己添加的词汇总是不成功，需要注意。如果使用的是 Sublime Text 来编辑这个 YAML 文件一定要注意不要开启`translate_tabs_to_spaces` 选项。词频部分可以不要。

一切添加妥当之后，重新部署。然后试着输入，`up`，候选列表会出现 `U盘`，输入 `yizhanghong`，出现 `一丈红`，说明词汇已经添加成功了！


## <a name="vert_quote_char"></a>直角引号

前面提到了修改自带的引号映射来输入直角引号的方式，但是修改以后，如果想要输入中文的单引号，双引号就不太方便了。利用词典的功能，我们也可以输入直角引号，在`jdhao.dict.yaml` 中加入下面的映射即可：

```
「」	syh	1000
『』	dyh	1000
```

效果如下图所示

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190217233924.png" width="400">
</p>


# 安装并启用小鹤双拼

最新版 weasel 并不带有小鹤双拼输入方案，需要自己下载，步骤如下：

+ 右键任务栏小狼毫的图标，打开菜单，选择「输入法设定」，进入输入法选择界面：

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20190217233952.png" width="400">
</p>

然后点击「获取更多输入方案」，会打开一个 Cmd 窗口,内容如下：

```
Rime package installer

Working directory: d:\Program Files (x86)\Rime\weasel-0.13.0
Package installer directory: d:\Program Files (x86)\Rime\weasel-0.13.0\
Download cache directory: C:\Users\ADMINI~1\AppData\Local\Temp
Rime user directory: C:\Users\Administrator\AppData\Roaming\Rime


Enter package name, URL, user/repo or downloaded ZIP to install:
```

+ 输入要下载的输入法方案即可，双拼方案在[这里](https://github.com/rime/rime-double-pinyin)可以找到，名称为 `double-pinyin`，在上面的对话框输入以后就可以下载 Rime 提供的双拼方案。

然后在上图的输入方案选择界面，选择要启用的输入方案。在输入汉字的界面，按 <kbd>F4</kbd> 选择小鹤双拼，即可启用。

# 安装表情 emoji

Rime 提供了很多表情，地址在 [这里](https://github.com/rime/rime-emoji)，但是对如何安装这些表情却有点语焉不详。

Rime 最新版提供了 [东方破](https://github.com/rime/plum) 来安装输入方案，[这里](https://github.com/rime/plum#windows-bootstrap-script) 也给出了 Windows 系统上使用的方法，但是下载安装以后，在 Cmd 使用如下命令安装 emoji，

```
rime-install emoji
```

或者使用

```
rime-install.bat emoji
```

都会输出下面的错误信息：

```
Rime package installer

Working directory: C:\Users\Administrator\AppData\Roaming\plum
Package installer directory: C:\Users\Administrator\AppData\Roaming\plum\
Download cache directory: C:\Users\ADMINI~1\AppData\Local\Temp
Rime user directory: C:\Users\Administrator\AppData\Roaming\Rime

rime-install: line 2: $'\r': command not found
rime-install: line 53: syntax error near unexpected token `$'do\r''
'ime-install: line 53: `for target in "${targets[@]}"; do
```

这个脚本在 cmd 上运行不了。必须使用 bash 环境才能运行，可以安装 [git for Windows](https://git-scm.com/download/win)。

安装以后，cd 到刚才安装 plum 所在的目录，首先运行以下命令安装 emoji，

```
bash rime-install emoji
```

然后，使用下面的命令给输入方案打 patch，我使用的输入方案是 `luna_pinyin_simp`，所以参考 [这里](https://github.com/rime/rime-emoji) 的命令：

```
bash rime-install emoji:customize:schema=luna_pinyin_simp
```

这样才会 patch 成功，这时，位于 `%appdata%/rime` 目录下的`luna_pinyin_simp.custom.yaml` 文件中会增加以下配置：

```yaml
__patch:
# Rx: emoji:customize:schema=luna_pinyin_simp {
  - patch/+:
      __include: emoji_suggestion:/patch
# }
```

然后重新部署，emoji 添加成功了！

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200430135706.png" width="200">
</p>

# 沧月简体默认输出英文

如果经常和英语打交道，偶尔输入汉字，可以把沧月拼音初始状态设为英语，这样就不用在需要输入英文时切换输入状态了。参考 [这里](https://github.com/rime/home/wiki/CustomizationGuide#%E4%B8%80%E4%BE%8B%E9%BB%98%E8%AA%8D%E8%8B%B1%E6%96%87%E8%BC%B8%E5%87%BA)给出的说明，在 `luna_pinyin_simp.custom.yaml` 文件中加入下面的设置：

```YAML
patch:
  "switches/@0/reset": 1  # 初始的 ascii mode 设置为「西文」
```

# 一些小问题

## 各种配置到底放在哪里啊？

有的配置是某个平台独有的，要放在平台相关的，有的配置只想某个输入方案独有，放在方案的配置里面，如果各个方案都想试用某个配置，就放在 `default.custom.yaml` 里面，参考[这里](https://github.com/rime/weasel/issues/6).

## 有的配置怎么不生效？

很有可能是忘记部署了，任何更改，都必须在部署以后才能生效。当然，也可能是配置填写的时候没有按照格式规范书写，写错了，Rime 的 log 文件地址：

+ 【中州韻】 `/tmp/rime.ibus.*`
+ 【小狼毫】 `%TEMP%\rime.weasel.*`
+ 【鼠鬚管】 `$TMPDIR/rime.squirrel.*`

通过查看最近的 log 文件，也许能发现具体错误在哪里。

## 为什么设置候选词竖排显示无效？

很多教程中说到，在 `weasel.custom.yaml` 里面使用如下的设置，使候选词竖排显示：

```yaml
patch:
  "style/horizontal": false
```

但是部署以后，候选词还是横排显示，这是为何？可能是因为某些 color_scheme 本身把候选词设为了横排显示，例如 Rime 鼠须管有多款主题都在主题设置中把候选词设置为横排[^2]，可以查看主题的配置来确定。

# 参考

+ 教程&指南
    + [Rime 用户指南](https://github.com/rime/home/wiki/UserGuide) (官方文档，需要认真研读)
    + [致第一次安装 Rime 的你](https://www.zybuluo.com/eternity/note/81763)
    + <https://mogeko.github.io/2018/031/>
    + <https://xinlu.ink/tech/rime.html>
- 添加表情
    + <https://github.com/rime/rime-emoji/issues/6>
    + <https://github.com/rime/rime-emoji/issues/4>
+ 词典相关
    + [Rime 额外的词典（针对沧月输入方案）](https://github.com/rime-aca/dictionaries)
    + [另外的一些做好的词典](https://github.com/xiaoTaoist/rime-dict)
    + [Rime 添加词典](https://www.jianshu.com/p/300bbe1602d4)
    + [输入法词库转换程序 GitHub 仓库](https://github.com/studyzy/imewlconverter)
    + [Rime 词典格式详解](https://github.com/LEOYoon-Tsaw/Rime_collections/blob/master/Rime_description.md#dictyaml-%E8%A9%B3%E8%A7%A3)
    + [导入词库](http://tieba.baidu.com/p/2757690418)(写在贴吧里面，格式不好，看着费劲)
+ 排版
    + [中文排版指南(关于直角引号使用)](https://juejin.im/entry/569da374d342d30053b4cd91)
    + [排版（少数派文章）](https://sspai.com/post/45516)

** 题图截取自 [Rime 官方网站](https://rime.im/)。

[^1]: 在我的系统上，根目录地址为 `D:\Program Files (x86)\Rime\weasel-0.13.0`，用户目录地址为 `C:\Users\Administrator\AppData\Roaming\Rime`。
[^2]: Mac 上鼠须管默认提供的主题可以在[这里](https://github.com/rime/squirrel/blob/master/data/squirrel.yaml)查看。
