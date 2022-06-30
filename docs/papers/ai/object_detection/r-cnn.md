# R-CNN

## 概述

[CVPR2014][R-CNN] Rich feature hierarchies for accurate object detection and semantic segmentation


## Motivation

将目标检测任务看做是一个有层级的任务：先生成有物体的区域、再进行分类（二阶段）。


## 算法流程

1. 使用 Selective Search 在图片上生成约2000个可能的候选区域（region proposal）。
2. 利用 CNN 提取固定长度的特征。
   - 为了做到这一点，需要对所有区域扭曲到固定大小。
3. 利用线性 SVM 进行分类。

## 训练

### 有监督的预训练

预训练特征提取 CNN。

### 精调

对于扭曲的图像进行精调。

### 分类器

设定 IoU 阈值 0.3 作为 SVM 正例和负例的界限（与训练 CNN 时不同）。