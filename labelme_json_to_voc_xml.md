---
title: "Labelme JSON 标注格式转 voc XML 格式"
date: 2019-12-21 23:37:49+0800
tags: []
categories: [Deep-Learning]
---

labelme 是一款常用的计算机视觉任务标注工具，可以用来标注分类，检测，分割等任务的数据。对于检测任务，labelme 生层的标注文件是 json 格式，每个图像对应一个相应的 json 文件。但是很多任务都使用 PASCAL VOC 的 xml 格式标注，例如[maskrcnn-benchmark](https://github.com/facebookresearch/maskrcnn-benchmark) 任务中的 [voc 数据集](https://github.com/facebookresearch/maskrcnn-benchmark/blob/master/maskrcnn_benchmark/data/datasets/voc.py)。

<!--more-->

labelme 含有将 json 格式标注数据转为 voc 格式的脚本，具体地址见 [这里](https://github.com/wkentaro/labelme/tree/master/examples/bbox_detection)。使用方法：

```
python labelme2voc.py original_data data_dataset_voc --labels labels.txt
```

第一个参数是原始标注目录，包含 json 文件以及对应的图像，第二个参数为生成的 voc 格式数据库，下面有多个文件夹，第三个参数是 label 文件，每行一个 label，第一行为 `__ignore__`，第二行为 `_background_`，其余行为实际的各个 label。

值得注意的是，这个脚本生成的 xml 格式文件，用 maskrcnn 提供的 voc 数据集的接口进行数据训练时，会报错，因为 [maskrcnn 中的 voc 数据类](https://github.com/facebookresearch/maskrcnn-benchmark/blob/master/maskrcnn_benchmark/data/datasets/voc.py#L95)会检查每个标注框的 difficult 信息，所以 difficult 这个 flag 必须赋值。labelme2voc.py 当前的代码没有给 difficult 赋值，必须把

```python
# https://github.com/wkentaro/labelme/blob/master/examples/bbox_detection/labelme2voc.py#L116
maker.difficult(),
```

变为

```python
maker.difficult(0),
```

然后代码才能顺利运行。

### 参考

+ https://github.com/wkentaro/labelme/pull/241
