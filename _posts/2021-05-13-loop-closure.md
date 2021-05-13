---
layout: post
title: loop closure 回环检测算法综述
description: "主流算法资料整理"
modified: 2021-05-13
tags: [sample post]
image:
  path: /images/SLAM/pipe.png
  feature: pipe
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---
# 回环检测

## 发展历程

1. 词袋模型（Bag Of Words,BOW) : DBOW → DBOW2 →DBOW3 →FBOW
2. 随机蕨法（Random ferns）
3. 基于深度学习的方法
   1. 有监督的方法 Places365
   2. 无监督的方法 CALC原理

## 词袋模型

### 步骤

1.提取特征

2.构建字典（所有单词的集合)

[https://mmbiz.qpic.cn/mmbiz_png/rqpicxXx8cNmKmBibtct2ZF6qUPZ97swJ2PNV1iaUwlSz6egmGmoXkFq7FHtUu4Cic01OUeCZibGRM3jFftoPBfUFDg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1](https://mmbiz.qpic.cn/mmbiz_png/rqpicxXx8cNmKmBibtct2ZF6qUPZ97swJ2PNV1iaUwlSz6egmGmoXkFq7FHtUu4Cic01OUeCZibGRM3jFftoPBfUFDg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

3.确定一帧中具有哪些单词，形成词袋向量 (1表示具有该单词，0表示没有)

[https://mmbiz.qpic.cn/mmbiz_png/rqpicxXx8cNmKmBibtct2ZF6qUPZ97swJ2icEXibV2mBefLma4INBMQZgxcIS1uLrWXWXDWFEiap9ibvvseftznSoDoA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1](https://mmbiz.qpic.cn/mmbiz_png/rqpicxXx8cNmKmBibtct2ZF6qUPZ97swJ2icEXibV2mBefLma4INBMQZgxcIS1uLrWXWXDWFEiap9ibvvseftznSoDoA/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

4.比较两帧描述向量的差异。

### 缺陷

并不完全精确，会出现假阳性数据。在回环检测检索的后期阶段需要用其他方法加以验证。如果当前跟踪已经完全丢失，需要重定位给出当前帧的位姿来调整。

## 随机蕨法（Random ferns）

思路：相机的每一帧压缩编码，并且有效的对不同帧之间相似性进行评估。

步骤：

1. 输入一个RGB-D图片，在图像的随机位置评估简单的二进制测试，将整个帧进行编码，形成编码块，每个fern产生一小块编码，并且编码连接起来可以表达一个紧凑的相机帧。
2. 每一个编码块指向一个编码表的一行，和具有等效的编码、存储着关键帧id的fern关联起来，编码表以哈希表的形式存储。
3. 当不断采集新的图片时，如果不相似性大于阈值，新进来的帧的id将会被添加到行中。在跟踪恢复的时候，从哈希表中检索姿态，将最相似的关键帧关联起来。

### 代码

Random Fern在VSLAM中的应用

kinect fusion

https://github.com/Nerei/kinfu_remake

elastic fusion

https://github.com/mp3guy/ElasticFusion

PTAM

https://github.com/Oxford-PTAM/PTAM-GPL

## 基于深度学习的方法

神经网络直接从图像中得到6自由度的相机位姿。相较于传统的视觉定位方法，省去了复杂的图像匹配过程，并且不需要对相机位姿进行迭代求解，但是输入图像必须在训练场景中。

### 有监督的方法

place365

源码地址：[https://github.com/CSAILVision/places365](https://github.com/CSAILVision/places365)

### 无监督的方法

### CALC原理

该方法创建了一个自动编码结构，可以有效的解决边界定位错误。对于一个位置进行拍摄，在不同时间时，由于视角变化、光照、气候、动态目标变化等因素，会导致定位不准。卷积神经网络可以有效地进行基于视觉的分类任务。

源码地址：[https://github.com/rpng/calc](https://github.com/rpng/calc)