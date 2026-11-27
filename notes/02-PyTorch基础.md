---
createdAt: 2026-11-27T07:54:47.095Z
updatedAt: 2026-11-27T07:55:15.686Z
lastViewedAt: 2026-11-27T07:54:47.095Z
field: 大模型/深度学习
---
# PyTorch 基础

> 张量、数据读取、模型构建、参数管理与保存加载。数学原理见 [数学基础](01-数学基础.md)，训练流程见 [训练与工程实践](03-训练与工程实践.md)。

## 1. 张量基础操作

### 1.1 产生张量

```python
import torch

# convert a list to a matrix
theList = [1, 3]
theTensor = theList.tensor()
theNumpy = theTensor.numpy()

# create special tensors
rangeTensor = torch.arange(12)
zerosTensor = torch.zeros((2, 3, 4))
zerosTensor = torch.zeros_like(theTensor)
onesTensor = torch.ones((2, 3, 4))

# generate a boolean tensor through logical operators
boolTensor = firstTensor == secondTensor
```

### 1.2 属性查询与操作

```python
# check the shape / number of elements
theShape = theTensor.shape
theNumberOfTensor = theTensor.numel()

# change the shape / merge tensor
changeTensor = theTensor.reshape(2, 1)
torch.cat((firstTensor, secondTensor), dim=0)

# math operations
torch.exp(x)
oneElementTensor = x.sum()
```

> 注：`theList.tensor()`、`theTensor.numpy()`、`firstTensor`/`secondTensor` 为原文写法，照录待复核。

## 2. 数据准备

### 2.1 CSV 文件

```python
import pandas as pd

data_file = "./data"
data = pd.read_csv(data_file)
inputs, outputs = data.iloc[:, :-1], data.iloc[:, -1]

# 将异常值替换为平均值
inputs["values"] = inputs["values"].fillna(inputs["values"].mean())

# 对字符串列进行独热编码
inputs = pd.get_dummies(inputs, dummy_na=True, dtype=int)
```

### 2.2 DataLoader

```python
from torch.utils.data import DataLoader

# batch_size 是步长，单次训练使用的样本数量
# 小则准确性不高、训练慢；太大则降低泛化能力、训练快、占内存大
# shuffle 代表是否打乱顺序
train_loader = DataLoader(trainset, batch_size=train_batch_size, shuffle=True)
test_loader = DataLoader(testset, batch_size=test_batch_size, shuffle=True)
```

## 3. 构建模型

### 3.1 MLP 构建块

```python
import torch
import torch.nn as nn

class MLP(nn.Module):
    def __init__(self, input_dim, hidden_dim, output_dim):
        super().__init__()
        self.hidden = nn.Linear(input_dim, hidden_dim)
        self.relu = nn.ReLU()
        self.output = nn.Linear(hidden_dim, output_dim)

    def forward(self, x):
        x = self.hidden(x)
        x = self.relu(x)
        x = self.output(x)
        return x
```

### 3.2 Sequential（自定义实现）

`Sequential` 是一种特殊的模块：

```python
class MySequential(nn.Module):
    def __init__(self, *args):
        super().__init__()
        for block in args:
            self._modules[block] = block

    def forward(self, x):
        for block in self._modules.values():
            x = block(x)
        return x
```

### 3.3 带参数的块

```python
class MyLinear(nn.Module):
    def __init__(self, *args):
        super().__init__()
        self.weight = nn.Parameter(torch.randn(in_units, units))
        self.bias = nn.Parameter(torch.randn(units,))

    def forward(self, x):
        linear = torch.matmul(x, self.weight.data) + self.bias.data
        return F.relu(linear)
```

## 4. 参数管理

### 4.1 读取参数

```python
net[2].state_dict()
net[2].bias
net[2].bias.data
net.state_dict()["2.bias"].data
type(net[2].bias)  # torch.nn.parameter.Parameter
```

### 4.2 自定义初始化

参数初始化的作用与方法见 [数学基础](01-数学基础.md#3-梯度计算与优化)。

```python
def init_normal(m):
    if type(m) == nn.Linear:
        nn.init.normal_(m.weight, mean=0, std=0.01)
        nn.init.zeros_(m.bias)

net.apply(init_normal)

def init_constant(m):
    if type(m) == nn.Linear:
        nn.init.constant_(m.weight, 1)
        nn.init.zeros_(m.bias)

net[0].apply(init_constant)

def xavier(m):
    if type(m) == nn.Linear:
        nn.init.xavier_uniform_(m.weight)
```

### 4.3 共享参数层

```python
shared = nn.Linear(8, 8)
net = nn.Sequential(
    nn.Linear(4, 8), nn.ReLU(),
    shared, nn.ReLU(),
    shared, nn.ReLU(),
    nn.Linear(8, 1)
)
```

## 5. 保存与加载

### 5.1 张量与模型

```python
# 张量的保存与加载
x = torch.arange(4)
torch.save(x, 'x-file')
x2 = torch.load("x-file")
# 多维张量、张量字典同理

# 模型保存
torch.save(net.state_dict(), 'mlp.params')
clone = MLP()
clone.load_state_dict(torch.load('mlp.params'))
```

### 5.2 检查点

```python
# 文件后缀为 pt 或 pth
torch.save(net.state_dict(), path)

# 保存检查点
torch.save({
    'epoch': epoch,
    "model_state_dict": net.state_dict(),
    "optimizer_state_dict": optimizer.state_dict(),
    "loss": loss
}, path)

checkpoint = torch.load(path)
model.load_state_dict(checkpoint['model_state_dict'])
optimizer.load_state_dict(checkpoint['optimizer_state_dict'])
epoch = checkpoint['epoch']
loss = checkpoint['loss']
```

### 5.3 加载部分参数

```python
model.load_state_dict(torch.load(path), strict=False)
```

## 6. 正则化与丢弃法的实现

### 6.1 权重衰减

```python
# 从头实现
def l2_penalty(w):
    return torch.sum(w.pow(2)) / 2

l = loss(net(x), y) + lambda_ * l2_penalty(w)

# 简洁实现（权重与偏置分组）
trainer = torch.optim.SGD([
    {"params": net[0].weight, "weight_decay": wd},
    {"params": net[0].bias}
], lr=lr)
print("w的L2范数:", torch.norm(w).item())
```

### 6.2 Dropout

```python
# 从头实现
def dropout_layer(x, dropout):
    assert 0 <= dropout <= 1
    if dropout == 1:
        return torch.zeros_like(x)
    if dropout == 0:
        return x
    mask = (torch.randn(x.shape) > dropout).float()
    return mask * x / (1.0 - dropout)

# 简洁实现
net = nn.Sequential(
    nn.Flatten(), nn.Linear(784, 256), nn.ReLU(),
    nn.Dropout(dropout1), nn.Linear(256, 256), nn.ReLU(),
    nn.Dropout(dropout2), nn.Linear(256, 10)
)
```

## 7. 线性回归

- 线性回归是对 n 维输入的加权加上偏差：$y = \langle w, x\rangle + b$。
- 线性回归等价于单层神经网络。
- 平方损失评估预测值与真实值的差异：$\text{loss} = \frac{1}{2}(y-\hat{y})^2$。
- 完整代码：
  - [线性回归基础优化算法.pdf](attachments/线性回归基础优化算法.pdf)
  - [线性回归框架算法.pdf](attachments/线性回归框架算法.pdf)
