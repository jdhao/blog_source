---
title: "两个大规模中文语料库介绍以及处理"
date: 2019-01-10 00:27:00 +0800
tags: [Python]
categories: [academic]
---

目前进行的工作需要大规模的语料库来生成中文文本图像，因此查找资料，找了一些中文语料库。本文介绍其中的两个最大的语料库，THUCNews 语料库和中文维基百科语料库，以及如何对原始语料库文件进行简单预处理。

<!--more-->

# THUCNews 语料库

THUCNews 语料库由清华大学自然语言处理与社会人文计算实验室收集整理发布，语料库是根据新浪新闻 RSS 订阅频道 2005~2011 年间的历史数据筛选过滤生成，包含了 836062篇文本格式的文档[^1]，文档编码使用 UTF-8 编码。这些文档被划分为 14 大类，分别为：财经、彩票、房产、股票、家居、教育、科技、社会、时尚、时政、体育、星座、游戏、娱乐。

该语料库免费提供给研究者使用，下载地址为[THUCNews.zip](http://thuctc.thunlp.org/message)。

该语料库文档都是 txt 文件，不需要再进行文本提取的处理。

# 中文维基百科语料库

维基百科会定期把各种语言的百科网页全部打包存储起来，这里我们选择其中的中文维基百科网页，这个文件可以作为中文语料库来使用。原始维基百科数据是压缩的 xml 文件，为了提取其中词条的纯文本内容，去掉众多 xml 标记，我们必须要对原始的压缩文件进行处理，提取有用信息。

## 语料库处理

首先下载原始的中文维基百科网页文件，下载地址为<https://dumps.wikimedia.org/zhwiki/latest/zhwiki-latest-pages-articles.xml.bz2>，文件大小在 1.5G 以上，在 Windows 系统下，推荐使用 [Free Download Manager](https://www.freedownloadmanager.org/) 下载。

为了读取其中的文本信息，我们需要借助提取工具，[WikiExtractor](https://github.com/attardi/wikiextractor) 是一款不错的开源提取工具，使用该工具，可以方便地处理语料库，输出为想要的存储格式。首先使用以下命令安装该工具：

```bash
git clone https://github.com/attardi/wikiextractor
cd wikiextractor
python setup.py install
```

wikiextractor 会把整个语料库分割为指定大小的文件，文件的格式默认为 xml 格式，具体格式如下：

```
<doc id="xxx" url="xxx" title="xxxx">
xxxxx
</doc>

<doc id="xxx" url="xxx" title="xxxx">
xxxxx
</doc>

<doc id="xxx" url="xxx" title="xxxx">
xxxxx
</doc>

...
```

每个生成的文件包含若干个词条。要生成 xml 格式的语料，可以使用以下命令：

```
WikiExtractor -o extracted_xml --process 2 -b 512K zhwiki-latest-pages-articles.xml.bz2
```

`-o` 用来指定输出目录，`--process` 用来指定使用的进程数目（默认为 1），`-b` 选项用来控制单个生成文件的大小（默认为 1M，文件越大，包含的词条也越多），最后的参数为要处理的原始压缩语料文件名称。程序运行完成以后，在输出目录下面会生成多个子目录，每个目录下面有一些生成的文件。

如果要生成 json 格式的语料文件，只需要加上 `--json` 选项：

```
WikiExtractor -o extracted_xml --process 2 -b 512K --json zhwiki-latest-pages-articles.xml.bz2
```

每个生成的文件中，每行对应一个以 JSON 格式存储的词条，格式如下：

```
{"id": "xxx", "url": "xxx", "title": "xxx", "text": "xxxxxx"}
```

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200119214325.png">
</p>


其中 `text` 对应的是某个词条的真正内容。

如何提取 JSON 格式字符串中的 text 内容？可以使用[json.loads()](https://docs.python.org/3/library/json.html#json.loads) 方法将符合 JSON 格式的字符串转换为 Python 中的字典。例如

```
In [6]: s = '{"apple": 1, "bananas": 2, "pear": 2.5}'

In [7]: json.loads(s)
Out[7]: {'apple': 1, 'bananas': 2, 'pear': 2.5}
```

下面给出一个简单读取一个文件所有词条 text 并且整合的代码：

```python
texts = ""
# file is a generated file
with open(file, encoding='utf-8') as f:
    # each line in the file is a wiki page
    for line in f:
        # read the line as valid json and select text field
        text = json.loads(line)['text']
        texts += text
```

根据自己的需要，可以把每一行的 text 存为新的文件，或者把原来文件里面的所有词条的 text 合在一起存为一个新文件。

另外，中文维基百科的文字很多都是繁体，如果需要简体中文，可以使用[OpenCC](https://github.com/yichen0831/opencc-python) Python 库把繁体文字统一转换为简体再保存，具体使用方法见 Opencc 的文档，这里不再赘述。

# 参考

+ https://www.zhihu.com/question/22956189
+ https://www.zhihu.com/question/21177095
+ https://blog.csdn.net/wangyangzhizhou/article/details/78348949
+ https://github.com/Embedding/Chinese-Word-Vectors
+ THUCNews 语料库主页：http://thuctc.thunlp.org/
+ 中文 wiki dump 主页：https://dumps.wikimedia.org/zhwiki/

[^1]: 使用 `find THUCNews_ROOT -type f -name '*.txt' -print|wc -l` 可以查看语料库中文本文件的数目。
