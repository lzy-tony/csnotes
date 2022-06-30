# PGD

## 概述

[ICLR2018][PGD] Towards Deep Learning Models Resistant to Adversarial Attacks

对于对抗攻防进行一个总结，并且用一个统一的模型去描述针对深度神经网络的攻击和防御。

- 提出了统一描述对抗攻防的鞍点优化模型。
- 总结了利用梯度进行攻击的最强一阶攻击手段 PGD（Projected Gradient Descent）。
- 在一阶对抗样本的基础上提出对抗训练的方法，讨论使模型更鲁棒的方法。


## 优化视角的攻防统一模型

机器学习模型的出发点是去寻找合适的参数 $\boldsymbol \theta$ 以最小化风险：

$$\mathbb E_{(\boldsymbol x, y) \sim D}[L(\boldsymbol \theta, \boldsymbol x, y)]$$

由于数据的真实分布不可知，因此传统做法是使用经验分布去最小化经验风险（ERM），在这种范式下出现了对抗样本的问题。因此作者对于 ERM 的范式进行补充，吸纳了对抗样本。

对抗攻防问题可以归纳为一个鞍点优化问题：

$$ \min \limits_{\boldsymbol \theta} \rho(\boldsymbol \theta), \rho(\boldsymbol \theta) = \mathbb{E}_{(\boldsymbol x, y) \sim D} [\max \limits_{\boldsymbol \delta \in \mathcal S} L(\boldsymbol \theta, \boldsymbol x + \boldsymbol \delta, y)] $$

其中 $\mathcal S$ 为所有可能扰动的集合。

在这一范式下：

- 攻击方试图求解内层的最大化问题。
- 防御方试图求解外层的最小化问题。


## 一阶攻击

### PGD

在 FGSM 的基础上进行了优化得到 PGD，从只移动单步到移动多步。

$$\boldsymbol x^{t + 1} = \prod_{\boldsymbol x + \mathcal S} (\boldsymbol x^t + \alpha \text{sign}(\nabla_{\boldsymbol x} L(\boldsymbol \theta, \boldsymbol x, y)))$$

即，在负损失函数的梯度上执行多步投影的梯度下降。

### 一阶攻击

鞍点问题内层的最大化问题是非凹的，因此很难通过优化手段直接求解。作者实验证明：尽管在区域 $\boldsymbol x_i + \mathcal S$ 中有许多分散的不同局部最大值，他们的值是相近的（实验方法：通过随机选取起始点进行 PGD），而更大的局部最大值通常是很难通过一阶手段寻找的（即使通过大量的起始点）。

由这一性质，作者推论得到 PGD 可以用来解决内层的最大化问题，且 PGD 是最强的一阶攻击手段（只利用梯度信息）。


## 对抗训练

对于防御方，我们需要解决外层的最小化问题，进行对抗训练。对抗训练的主要优化方式是使用 SGD。使用 SGD 的关键问题在于如何计算梯度 $\nabla_{\boldsymbol \theta} \rho(\boldsymbol \theta)$。

作者提出直接对 $\max$ 内部的表达式求梯度，即直接将原输入叠加上 PGD 生成的对抗扰动。这一操作的理论依据为 Danskin 定理，尽管不能满足其应用条件（需要连续可微的内部函数），但在实验中获得较好成果。

## 模型容量和对抗训练的部分讨论

- 只提高模型大小本身（在自然样本上训练）即可有效提高针对一阶攻击的鲁棒性。
- 应用FGSM样本进行训练只会导致模型过拟合到出现的样本上，无法有效提高鲁棒性。
- 小的模型有可能难以学习到非平凡的分类器（容量不足，收敛到平凡解）。
- 提高模型大小可以有效减小鞍点优化问题的值。
- 更大的模型和更强的对抗样本会降低迁移性。