# tensor

## 基本信息

`tensor`（张量）是一种特殊的数据结构，用于存储多维数表。`tensor` 可以在GPU等硬件上运行并优化计算。

根据其维数，张量可以分为不同的阶，一些不同阶的张量如下表：

| 阶   | 数学实例   | Python实例                                 |
| ---- | ---------- | ------------------------------------------ |
| $0$  | 纯量       | `s = 1`                                    |
| $1$  | 向量       | `v = [1.1, 2.2, 3.3]`                      |
| $2$  | 矩阵       | `m = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]`    |
| $3$  | $3$ 阶张量 | `t = [[[2], [4], [6]], [[8], [10], [12]]]` |
| $n$  | $n$ 阶张量 | $\dots$                                    |



## 初始化

### 从数据初始化

```python
x_data = torch.tensor(data)
```

注：`torch.tensor()` 总是复制数据，如果希望避免复制以提高效率可以参考 `torch.as_tensor()`（用于NumPy数组）。



### 从NumPy数组初始化

```python
x_np = torch.from_numpy(np_array)
```



### 从其他张量

```python
x_ones = torch.ones_like(x_data)
x_rand = torch.rand_like(x_data, dtype=torch.float)
```



### 其他

```python
shape = (2, 3, )
rand_tensor = torch.rand(shape)
ones_tensor = torch.ones(shape)
zeros_tensor = torch.zeros(shape)
```


## 属性

```python
tensor = torch.rand(3,4)

print(f"Shape of tensor: {tensor.shape}")
print(f"Datatype of tensor: {tensor.dtype}")
print(f"Device tensor is stored on: {tensor.device}")
```



## 运算

### GPU

张量在CPU上被创建，如果需要利用GPU进行加速，则需显式声明：

```python
if torch.cuda.is_available():
    tensor = tensor.to('cuda')
```

可以指定具体显卡：

```python
tensor = tensor.to('cuda:1')
```


### 索引

`tensor` 的索引与NumPy相似：

```python
tensor = torch.ones(4, 4)
print('First row: ', tensor[0])
print('First column: ', tensor[:, 0])
print('Last column:', tensor[..., -1])
tensor[:,1] = 0
print(tensor)
```


### 合并

`torch.cat()` 在给定维度进行拼接：

```python
tensor = torch.ones(4, 4)
tensor[:,1] = 0
'''
tensor([[1., 0., 1., 1.],
        [1., 0., 1., 1.],
        [1., 0., 1., 1.],
        [1., 0., 1., 1.]])
'''

t1 = torch.cat([tensor, tensor, tensor], dim=1)
'''
tensor([[1., 0., 1., 1., 1., 0., 1., 1., 1., 0., 1., 1.],
        [1., 0., 1., 1., 1., 0., 1., 1., 1., 0., 1., 1.],
        [1., 0., 1., 1., 1., 0., 1., 1., 1., 0., 1., 1.],
        [1., 0., 1., 1., 1., 0., 1., 1., 1., 0., 1., 1.]])
'''
```

`torch.stack()` 对于给定的 `tensor` 序列在新维度进行拼接。


### 转置

```python
tensor.T
```



### 乘法

点乘：

元素对应相乘。

`*`：

- 与标量：

  ```python
  tensor = torch.ones(3, 4)
  tensor * 2
  ```

- 与一维向量：

  相当于每列乘以行向量对应列的值。

  ```python
  a = torch.ones(3, 4)
  b = torch.tensor([1, 2, 3, 4])
  a * b
  ```

- 与矩阵：

  矩阵对应位置相乘，需要 `A, B` 维度完全相同。

  ```python
  a = torch.tensor([[1, 2], [2, 3]])
  a * a
  ```

- `broadcast`（广播）机制：

  将维度低的 `tensor` 进行多次复制补齐直到与维度高的相同。

  所需条件：

  - 每个 `tensor` 维数至少为1；
  - 从最后一维开始比较两个张量：每一维相同、有一个为1、或者一个不存在。

  ```python
  x = torch.empty(5, 7, 3)
  y = torch.empty(5, 7, 3)
  # same shapes are always broadcastable (i.e. the above rules always hold)
  
  x = torch.empty((0,))
  y = torch.empty(2, 2)
  # x and y are not broadcastable, because x does not have at least 1 dimension
  
  # can line up trailing dimensions
  x = torch.empty(5, 3, 4, 1)
  y = torch.empty(   3, 1, 1)
  # x and y are broadcastable.
  # 1st trailing dimension: both have size 1
  # 2nd trailing dimension: y has size 1
  # 3rd trailing dimension: x size == y size
  # 4th trailing dimension: y dimension doesn't exist
  
  # but:
  x = torch.empty(5, 2, 4, 1)
  y = torch.empty(   3, 1, 1)
  # x and y are not broadcastable, because in the 3rd trailing dimension 2 != 3
  ```

  可以利用 `broadcast` 理解不同维的点乘。

`torch.mul()`：

与 `*` 相同。



矩阵乘法：

`torch.mm()`：

```python
a = torch.ones(3, 4)
b = torch.ones(4, 2)
torch.mm(a, b)
```

`torch.matmul()`：

`torch.mm()` 的 `broadcast` 版本。



### in-place 运算

能够将结果存储在操作数里的运算，用以 `_` 结尾的运算表示。

```python
tensor.add_(5)
tensor.copy_(y)
```



### 取值

对于0维 `tensor`，可以用 `item()` 取出其具体数值：

```
agg = tensor.sum()
agg_item = agg.item()
```