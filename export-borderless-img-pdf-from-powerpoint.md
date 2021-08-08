---
title: 如何从 PowerPoint 幻灯片导出没有多余白边的图像或者 PDF 文件
date: 2018-02-06 16:20:00 +0800
tags: [Office, PDF]
categories: [tools]
---

PowerPoint 是一个方便的绘图工具，可以完成一些简单的绘图工作，并且支持将 slide
导出为图片或者 PDF 格式，方便插入到其他文档中，但是默认导出的图片或者 PDF 文件
通常会有大量冗余的白边。如何设置，才能使得导出的文件中的白边尽可能少呢？本文将
给出一种办法。

<!--more-->

# 导出图片

PowerPoint 支持导出单张 slide 为图片，要想使得导出的图片白边尽可能少，首先选中
slide 中的图，把图在水平以及垂直方向都居中放置，然后点击「设计」选项卡，设置「
幻灯片大小」，如下图所示。

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-2-6/55150983.jpg"
         title="设置幻灯片大小"
         style="float: middle;">


点击「幻灯片大小」，选择「自定义幻灯片大小」，

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-2-6/51174973.jpg"
         title="自定义幻灯片大小"
         style="float: middle;">

在弹出的窗口中，逐渐缩小幻灯片的高和宽，

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-2-6/37894304.jpg"
         title="调整幻灯片高和宽"
         style="float: middle;">

点击「确定」，会弹出新的窗口，选择「最大化」，

<img src="https://blog-resource-1257868508.file.myqcloud.com/18-2-6/23970201.jpg"
         title="最大化 slide"
         style="float: middle;">

然后你会发现 slide 的白边变少了，边缘更加紧凑了。然后逐渐更改宽和高，得到自己想
要的结果。最后把当前幻灯片另存为 JPEG 或者 PNG 格式的图片就可以了。

# 导出 PDF

PDF 文件的优点是不会因为放大而变形，因此如果想要保存高质量的图片，PDF 格式是首
选，PowerPoint 支持把幻灯片保存为 PDF 格式，但是会把所有幻灯片全部保存为一个
PDF，并不支持把单张 slide 导出 PDF，因此如果想要导出单张幻灯片到 PDF，新建一个
PowerPoint 文件，把想要导出为 PDF 的 slide 拷贝过去，相当于建立一个只有一张幻灯
片的 PowerPoint 文件。设置 margin 的方式与导出图片时是一样的，也是通过自定义幻
灯片大小来实现的。然后再导出为 PDF 文件即可。

# 参考

+ <https://answers.microsoft.com/en-us/msoffice/forum/msoffice_powerpoint-mso_mac-mso_mac2016/powerpoint-2016-to-pdf-page-margin-issue/d7f69d16-adb4-4b9a-89a0-3f32359e03f9>
+ <https://answers.microsoft.com/en-us/mac/forum/macoffice2011-macpowerpoint/save-a-single-slide-as-a-pdf/9b438c1b-89cc-4a68-b65c-14d8d7ad8461>
