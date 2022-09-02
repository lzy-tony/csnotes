# Module

## 定义 Class

从 `nn.Module` 中继承搭建神经网络模块。在 `__init__()` 中完成网络的搭建并在 `forward()` 中完成运算。

```python
class NeuralNetwork(nn.Module):
    def __init__(self):
        super(NeuralNetwork, self).__init__()
        self.flatten = nn.Flatten()
        self.linear_relu_stack = nn.Sequential(
            nn.Linear(28*28, 512),
            nn.ReLU(),
            nn.Linear(512, 512),
            nn.ReLU(),
            nn.Linear(512, 10),
        )

    def forward(self, x):
        x = self.flatten(x)
        logits = self.linear_relu_stack(x)
        return logits
```

## 可视化

可以通过 `print(model)` 查看搭建的网络结构。上述示例打印结果为：

```
NeuralNetwork(
  (flatten): Flatten(start_dim=1, end_dim=-1)
  (linear_relu_stack): Sequential(
    (0): Linear(in_features=784, out_features=512, bias=True)
    (1): ReLU()
    (2): Linear(in_features=512, out_features=512, bias=True)
    (3): ReLU()
    (4): Linear(in_features=512, out_features=10, bias=True)
  )
)
```

## 组件

### 容器

| API             | 说明     | 其它                                        |
| --------------- | -------- | ------------------------------------------- |
| `nn.Module`     | 基类     |                                             |
| `nn.Sequential` | 序列容器 | `nn.Sequential(nn.Linear(1, 2), nn.ReLU())` |



### 卷积

| API                  | 说明                        | 其它                                                         |
| -------------------- | --------------------------- | ------------------------------------------------------------ |
| `nn.Conv1d`          | 1D 卷积                     | `nn.Conv1d(in_channels, out_channels, kernel_size, stride=1, padding=0, dilation=1, groups=1, bias=True, padding_mode='zeros', device=None, dtype=None)` |
| `nn.Conv2d`          | 2D 卷积                     | `nn.Conv2d(in_channels, out_channels, kernel_size, stride=1, padding=0, dilation=1, groups=1, bias=True, padding_mode='zeros', device=None, dtype=None)` |
| `nn.ConvTranspose2d` | 上采样用，2D Transpose 卷积 | `nn.ConvTranspose2d(in_channels, out_channels, kernel_size, stride=1, padding=0, output_padding=0, groups=1, bias=True, dilation=1, padding_mode='zeros', device=None, dtype=None)` |



### 池化

| API                    | 说明              | 其它                                                         |
| ---------------------- | ----------------- | ------------------------------------------------------------ |
| `nn.MaxPool1d`         | 1D 最大池化       | `nn.MaxPool1d(kernel_size, stride=None, padding=0, dilation=1, return_indices=False, ceil_mode=False)` |
| `nn.MaxPool2d`         | 2D 最大池化       | `nn.MaxPool2d(kernel_size, stride=None, padding=0, dilation=1, return_indices=False, ceil_mode=False)` |
| `nn.AvgPool1d`         | 1D 平均池化       | `nn.AvgPool1d(kernel_size, stride=None, padding=0, ceil_mode=False, count_include_pad=True` |
| `nn.AvgPool2d`         | 2D 平均池化       | `nn.AvgPool2d(kernel_size, stride=None, padding=0, ceil_mode=False, count_include_pad=True` |
| `nn.AdaptiveAvgPool2d` | 2D 自适应平均池化 | `nn.AdaptiveAvgPool2d(output_size)`                          |



### 填充

| API                   | 说明     | 其它                               |
| --------------------- | -------- | ---------------------------------- |
| `nn.ReflectionPad2d`  | 对称填充 | `nn.ReflectionPad2d(padding)`      |
| `nn.ReplicationPad2d` | 复制填充 | `nn.ReplicationPad2d(padding)`     |
| `nn.ZeroPad2d`        | 零填充   | `nn.ZeroPad2d(padding)`            |
| `nn.ConstantPad2d`    | 常量填充 | `nn.ConstantPad2d(padding, value)` |



### 激活函数

| API            | 说明      | 其它                                               |
| -------------- | --------- | -------------------------------------------------- |
| `nn.ReLU`      | ReLU      | `nn.ReLU(inplace-False)`                           |
| `nn.LeakyReLU` | LeakyReLU | `nn.LeakyReLU(negative_slope=0.01, inplace=False)` |
| `nn.Sigmoid`   | Sigmoid   | `nn.Sigmoid()`                                     |
| `nn.Tanh`      | Tanh      | `nn.Tanh()`                                        |



### 正则化层

| API              | 说明          | 其它                                                         |
| ---------------- | ------------- | ------------------------------------------------------------ |
| `nn.BatchNorm1d` | Batch Norm 1D | `nn.BatchNorm1d(num_features, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True, device=None, dtype=None)` |
| `nn.BatchNorm2d` | Batch Norm 2D | `nn.BatchNorm2d(num_features, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True, device=None, dtype=None)` |
| `nn.LayerNorm`   | Layer Norm    | `nn.LayerNorm(normalized_shape, eps=1e-05, elementwise_affine=True, device=None, dtype=None)` |



### 线性层

| API         | 说明   | 其它                                                         |
| ----------- | ------ | ------------------------------------------------------------ |
| `nn.Linear` | 线性层 | `nn.Linear(in_features, out_features, bias=True, device=None, dtype=None)` |



### dropout 层

| API            | 说明                         | 其它                                 |
| -------------- | ---------------------------- | ------------------------------------ |
| `nn.Dropout`   | 以伯努利分布随机丢弃         | `nn.Dropout(p=0.5, inplace=False)`   |
| `nn.Dropout1d` | 随机丢弃整个通道（1D特征图） | `nn.Dropout1d(p=0.5, inplace=False)` |
| `nn.Dropout2d` | 随机丢弃整个通道（2D特征图） | `nn.Dropout2d(p=0.5, inplace=False)` |



### 损失函数

| API                    | 说明                                                         | 其它                                                         |
| ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `nn.MSELoss`           | MSE，接受输入 $x$ 和目标 $y$，`reduction` 可选 `none, mean, sum` | `nn.MSELoss(size_average=None, reduce=None, reduction='mean')` |
| `nn.CrossEntropyLoss`  | 交叉熵，接受 `(minibatch, C)` 或 `(minibatch, C, d1, ..., dk)` ，`minibatch` 可以是 raw | `nn.CrossEntropyLoss(weight=None, size_average=None, ignore_index=- 100, reduce=None, reduction='mean', label_smoothing=0.0)` |
| `nn.BCELoss`           | BCE，比较输入和输出的概率                                    | `nn.BCELoss(weight=None, size_average=None, reduce=None, reduction='mean')` |
| `nn.BCEWithLogitsLoss` | BCE + Sigmoid                                                | `nn.BCEWithLogitsLoss(weight=None, size_average=None, reduce=None, reduction='mean', pos_weight=None)` |

