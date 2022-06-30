# YOLO v2

## 概述

[CVPR2017] YOLO9000: Better, Faster, Stronger



## Better

YOLO v1 精度上的缺点：

- 相比于 R-CNN 系列，YOLO 的 localization 错误率明显较高。
- YOLO 的 recall 较低（或许受限于一个 grid 只能识别一类物体）。

针对上述缺点，YOLO v2 做出了一系列的改进。



### Batch Normalization

提供正则化，可以去除 dropout 而不过拟合。



### High Resolution Classifier

在更高分辨率的图片上训练主体的分类网络。



### Anchor Box

引入 anchor box，将预测对象从 YOLO v1 中全连接层直接输出坐标改变为利用 anchor box 预测偏置，简化预测问题。



### Dimension Clusters

对于 anchor box，我们希望选取更好的先验以降低学习的难度。这一过程可以通过对于数据集进行 K-means 聚类完成。

注意到如果使用标准的欧氏距离，大框会比小框有更大的距离，考虑到我们追求的目标是更小的 $\text{IOU}$，自定义距离为
$$
d(\text{box}, \text{centroid}) = 1 - \text{IOU}(\text{box}, \text{centroid})
$$



### Direct Location Prediction

在引入 anchor box 的过程中产生了新的问题：由于 R-CNN 系列模型中对于 anchor box 通过偏置推算且没有对应约束，因此 anchor box 可以出现在图片的任意位置。

$$
\begin{cases}
x = (t_x \times w_a) - x_a \\
y = (t_y \times h_a) - y_a
\end{cases}
$$

对于随机初始化的模型这会导致很高的不稳定性（尤其在初始阶段）且需要很久才能收敛。

解决方案是，对于每个 anchor box 都预测 $5$ 个值：$t_x, t_y, t_w, t_h, t_o$，如果 grid 的偏置是 $(c_x, c_y)$、anchor box 有长宽 $p_w, p_h$，则有预测：

$$
\begin{cases}
b_x = \sigma(t_x) + c_x \\
b_y = \sigma(t_y) + c_y \\
b_w = p_w e^{t_w} \\
b_h = p_h e^{t_h}
\end{cases}
$$

而置信度通过 $\sigma(t_o)$ 计算。



### Fine-Grained Features

YOLO v2 的输入图片大小为 $416 \times 416$，经过多次最大池化后得到 $13 \times 13$ 的特征图。这一大小的特征图对于检测大物体足够，但对于小物体还需要更加精细的特征。

YOLO v2 的解决方案是增加 pass-through 层，对于之前的 $26 \times 26$ 的特征图，通过拼接的方式增加到输出的通道中。



### Multi-Scale Training

多尺度图片，在训练过程中定时改变输入图片尺寸使得模型更鲁棒。



## Faster

取代原先的 VGG16，改用 Darknet19、通过更多的降维、平均池化达到更快的速度。



## Stronger

对于分类任务，标签通常较为详细；而对于检测任务，标签通常较为粗略。YOLO v2 希望能够通过统一的方法对这两者进行训练，以提高数据利用并联合训练。

使用 WordTree 对于标签通过语意进行分层，可以将详细和粗略的标签互化，得到 YOLO9000。