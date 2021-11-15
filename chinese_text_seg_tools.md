---
title: "几种中文分词工具对比"
date: 2020-11-20T23:01:15+08:00
draft: false
tags: []
categories: [Note]
---

<details>
<summary><font size="2" color="red">Update log</font></summary>

+ <font color="blue">2021-10-23: fix typo，更新链接。</font>
</details>
对几种中文分词工具进行了调研，简单总结一下。

<!--more-->

# 工具介绍与安装

这里用了几种开源的分词工具，具体信息如下：

+ [jieba](https://github.com/fxsjy/jieba)：jieba 是分词领域还挺有名的一个工具。
+ [pkuseg](https://github.com/lancopku/pkuseg-python)：pkuseg 是[北京大学语言计算与机器学习研究组](https://lancopku.github.io/)开源的一个分词工具。
+ [LAC](https://github.com/baidu/lac)：LAC 是[百度 NLP 团队](https://nlp.baidu.com/homepage/index)开源的基于百度自家的 [PaddlePaddle](https://github.com/PaddlePaddle/Paddle) 的词法分析工具。
+ [LTP](https://github.com/HIT-SCIR/ltp): LTP 是[哈工大社会计算与信息检索研究中心](http://ir.hit.edu.cn/)开源的一款中文自然语言处理工具。

这些库都提供了 Python 接口，使用 pip 安装命令如下：

```bash
pip install jieba pkuseg lac ltp
```

# 简单上手试用

```python
from LAC import LAC
import pkuseg
import jieba
from ltp import LTP

my_str = "索引图是一种特殊的图 ，它的存在主要是为了节省空间，索引图附带了一个 color palette/table 或者叫 color map，对应了 256 种颜色（所以 color table 大小为 256x3）），然后图像像素每个位置值在 0-255 之间，数值代表该处像素在 color table 对应的颜色的索引值，实际展示该图片的时候，我们利用这个索引就能在 color table 中找到真正要展示的颜色。从这个描述可以看出，索引图一个像素只需要 1 比特，所以索引图占的空间大小约为 RGB 图的 1/3 ，大大减少了存储占用"

bseg = LAC(mode='seg')
pseg = pkuseg.pkuseg()
ltp = LTP()

print(bseg.run(my_str))
print(pseg.cut(my_str))
print(list(jieba.cut(my_str)))   # jieba.cut() 返回的是 iterator，所以转成了 list 显示
print(ltp.seg([my_str])[0])
```

# 速度 benchmark

同时也对比了一下几款工具的分词速度，

```
In [9]: %timeit bseg.run(my_str)
8.24 ms ± 68.8 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)

In [10]: %timeit list(jieba.cut(my_str))
Building prefix dict from the default dictionary ...
Loading model from cache /var/folders/nj/53p0t04x2d76_9x8hsq0zfwc0000gn/T/jieba.cache
Loading model cost 0.566 seconds.
Prefix dict has been built successfully.
740 µs ± 7.24 µs per loop (mean ± std. dev. of 7 runs, 1 loop each)

In [11]: %timeit pseg.cut(my_str)
5.31 ms ± 160 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)

In [12]: %timeit ltp.seg([my_str])
54.7 ms ± 666 µs per loop (mean ± std. dev. of 7 runs, 10 loops each)
```

可以看出 jieba 的速度是最快的，其次是 pkuseg，然后百度的 LAC，最慢的是哈工大 LTP。检查分词的结果，发现 jieba 的分词准确率不及 pkuseg，LAC 和 LTP，pkuseg，LAC，LTP 结果比较接近。

**注：非专业测评，结果仅供参考，更客观全面的评测还需要在不同数据集上跑测试才能得出。**

# 参考

+ https://www.52nlp.cn/%E4%BA%94%E6%AC%BE%E4%B8%AD%E6%96%87%E5%88%86%E8%AF%8D%E5%B7%A5%E5%85%B7%E7%BA%BF%E4%B8%8Apk-jieba-snownlp-pkuseg-thulac-hanlp
+ https://github.com/ownthink/evaluation
+ https://www.cnblogs.com/maxxu11/p/12604569.html
