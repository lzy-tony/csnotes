# YOLO v1

## 概述

[CVPR2016] You Only Look Once: Unified, Real-Time Object Detection


## Motivation

计算机视觉领域内常见的三类任务分别是分类、目标检测、实例分割。

在分类问题的基础上，传统的目标检测使用 Region-based 的方法，采用两阶段的方法：先选择出可能的 region proposal，再在区域的基础上进行分类。典型的做法是 R-CNN 系列的模型。

在 YOLO 中，作者试图将目标检测问题转换为一个回归问题，使用一个一阶段的方法（You Only Look Once）来进行解决，得到更快、更有效的模型。

这也决定了 YOLO 的优点：

- 高效：一阶段的回归问题相比两阶段的 region proposal 方法模型更阶段，计算更快。
- 全局判定：摆脱了局部的 region proposal，可以考虑更全局的信息。
- 通用：YOLO 可以学到目标更为通用、泛化能力更强的特征。



## 基本原理

### 回归问题

#### 预测输出

对于一个目标检测任务，我们希望能够在一张输入图片中找到其所包含的 $n$ 个物体，对于每个物体，给出基本信息：中心位置 $(x, y)$、高 $h$、宽 $w$、置信度 $\text{confidence}$。

考虑将输入模型划分为 $S \times S$ 的网格，如果物体的中心落入这个网格中，那么该网格负责给出该物体的预测。每个网格给出 $B$ 个预测，对于每个预测给出 $5$ 个基本信息；同时还需要给出 $C$ 类物体的概率 $P(\text{Class}_i | \text{Object})$。

此时，模型的预测输出就是一个 $S \times S \times (B \times 5 + C)$ 的张量。



#### 尺寸

为了更好的泛化和计算，约定 $(x, y)$ 分别 **除以 grid 的宽度和高度**；$w, h$ 分别 **除以图片的宽度和高度** 强制映射到 $(0, 1)$。



#### 置信度

令置信度为：

$$
\text{confidence} = P(\text{Object}) \times \text{IOU}_{\text{pred}}^{\text{truth}}
$$

在输出时，置信度也被用于进行 NMS。



### 训练

#### 损失函数

对于 YOLO 中可能出现的诸多问题，模型在损失函数处做了很多设计进行修改。

$$
\begin{align}
&\lambda_{\text{coord}} \sum \limits_{i = 0}^{S^2} \sum \limits_{j = 0}^B \mathbb 1_{ij}^{\text{obj}}[(x_i - \hat x_i)^2 + (y_i - \hat y_i)^2] \\
+& \lambda_{\text{coord}} \sum \limits_{i = 0}^{S^2} \sum \limits_{j = 0}^B \mathbb 1_{ij}^{\text{obj}}[(\sqrt{w_i} - \sqrt{\hat w_i})^2 + (\sqrt{y_i} - \sqrt{\hat y_i})^2] \\ \\
+& \lambda_{\text{coord}} \sum \limits_{i = 0}^{S^2} \sum \limits_{j = 0}^B \mathbb 1_{ij}^{\text{obj}}(C_i - \hat C_i)^2 \\
+& \lambda_{\text{noobj}} \sum \limits_{i = 0}^{S^2} \sum \limits_{j = 0}^B \mathbb 1_{ij}^{\text{noobj}}(C_i - \hat C_i)^2\\ \\
+& \sum \limits_{i = 0}^{S^2} \mathbb 1_i ^{\text{obj}} \sum \limits_{c \in \text{classes}} (p_i(c) - \hat p_i(c))^2
\end{align}
$$

- 在图片中，多数的网格是不包含物体的，此时会有大量的网格的损失函数逼近 $0$，使得梯度不稳定并且掩盖有物体网格的贡献。

  因此，引入 $\lambda_\text{coord}=5, \lambda_{\text{noobj}} = .5$  进行区分。

- 在 MSE 中，大物体和小物体的长宽尺度对于 loss 有所影响。

  因此，在损失函数中，对于 $h, w$ 开根。