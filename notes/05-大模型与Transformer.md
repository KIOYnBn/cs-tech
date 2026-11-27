---
createdAt: 2026-11-27T07:54:49.337Z
updatedAt: 2026-11-27T07:55:07.060Z
lastViewedAt: 2026-11-27T07:54:49.337Z
field: 大模型/深度学习
---
# 大模型与 Transformer

> 从感知机到 Transformer 的模型演进与完整实现。通用模块说明见 [模型模块](04-模型模块.md)。

## 1. 发展脉络

**深度学习**：指通过增加隐藏层个数来提高模型能力的方式。

### 1.1 感知机

- 任务类型：二分类。
- 计算：

$$
o = \sigma(\langle w, x\rangle + b),\quad
\sigma(x) = \begin{cases} 1,\quad \text{if } x>0 \\ 0/-1,\quad \text{otherwise} \end{cases}
$$

- 效果：产生线性分割面，因此不能处理 XOR 问题。

### 1.2 多层感知机（MLP）

- 结构：添加隐藏层。

![多层感知机结构图](attachments/多层感知机结构图.png)

$$
h = W_1x + b_1,\quad o = W_2^T h + b_2
$$

**激活函数**：将线性变为非线性（把上式 h 代入 o，结果仍为线性公式）。常见函数：

| 函数 | 公式 | 输出范围 |
| --- | --- | --- |
| sigmoid | $\dfrac{1}{1+\exp(-x)}$ | (0, 1) |
| tanh | $\dfrac{1-\exp(-2x)}{1+\exp(-2x)}$ | (−1, 1) |
| relu | $\max(x, 0)$ | $[0, \infty)$ |

常用激活函数为 ReLU。

```python
net = nn.Sequential(nn.Flatten(), nn.Linear(784, 256), nn.ReLU(), nn.Linear(256, 10))

def init_weights(m):
    if type(m) == nn.Linear:
        nn.init.normal_(m.weight, std=0.1)

net.apply(init_weights)
loss = nn.CrossEntropyLoss()
trainer = torch.optim.SGD(net.parameters(), lr=lr)
```

### 1.3 监督学习中的模型类型

1. 决策树：gradient boosting、随机森林
2. 线性模型
3. 核方法
4. 神经网络：多层感知机、卷积神经网络、循环神经网络、Transformers

## 2. Transformer 结构

### 2.1 视觉 Transformer（ViT）

![视觉Transformer结构流程图](attachments/视觉Transformer结构流程图.png)

| 组件 | 作用 |
| --- | --- |
| Patch Embedding | 将图像以高维矩阵表示并分割成块，以多维「向量」形式（类似序列）输入 encoder |
| Position Embedding | 提供图像块的空间位置信息；方法包括可学习的位置编码、正弦位置编码、固定位置编码、相对位置编码 |
| Encoder | 提取整合图像特征 |
| Classification Token | 可学习向量，用于整合图像信息；随机初始化后与位置编码相加，最终只有 class token 的输出用于类别预测 |

### 2.2 词嵌入（Word Embedding）

- 作用：数据预处理步骤，把计算机不能识别的内容转化为矩阵/向量。
- 方法：word2vec、GloVe、FastText（原文写作 Fastatext）、ELMo、BERT。
- 当前最优秀的经典方法是 BERT（谷歌基于 Transformer 提出）。

### 2.3 缓解过拟合

Transformer 解决拟合问题的方法：增加训练数据量、使用正则化技术、选择更合适的模型、进行特征选择和降维、采用交叉验证等。

## 3. 完整 Transformer 模型代码（药物–蛋白相互作用预测）

> 该代码为自包含示例，其中的 `SelfAttention` 与 [模型模块](04-模型模块.md#1-自注意力机制selfattention) 中的规范实现等价，区别是此处只返回输出 `x`（不返回注意力权重），且用 `@` 代替 `torch.matmul`；`contiguous()` 使元素内存分布均匀、提高计算效率。

```python
# -*- coding: utf-8 -*-
import torch
import torch.nn as nn
import torch.optim as optim
import torch.nn.functional as F
import math
import numpy as np
from sklearn.metrics import roc_auc_score, precision_score, recall_score

class SelfAttention(nn.Module):
    def __init__(self, hid_dim, n_heads, dropout, device):
        super().__init__()
        self.hid_dim = hid_dim
        self.n_heads = n_heads
        assert hid_dim % n_heads == 0
        self.w_q = nn.Linear(hid_dim, hid_dim)
        self.w_k = nn.Linear(hid_dim, hid_dim)
        self.w_v = nn.Linear(hid_dim, hid_dim)
        self.fc = nn.Linear(hid_dim, hid_dim)
        self.do = nn.Dropout(dropout)
        self.scale = torch.sqrt(torch.FloatTensor([hid_dim // n_heads])).to(device)

    def forward(self, query, key, value, mask=None):
        bsz = query.shape[0]  # batch_size
        Q = self.w_q(query)
        K = self.w_k(key)
        V = self.w_v(value)
        # [batch size, n heads, sent len, hid dim // n heads]
        Q = Q.view(bsz, -1, self.n_heads, self.hid_dim // self.n_heads).permute(0, 2, 1, 3)
        K = K.view(bsz, -1, self.n_heads, self.hid_dim // self.n_heads).permute(0, 2, 1, 3)
        V = V.view(bsz, -1, self.n_heads, self.hid_dim // self.n_heads).permute(0, 2, 1, 3)
        energy = torch.matmul(Q, K.permute(0, 1, 3, 2)) / self.scale
        # matmul 是矩阵间的内积，可以用 @ 代替
        if mask is not None:
            energy = energy.masked_fill(mask == 0, -1e10)
        attention = self.do(F.softmax(energy, dim=-1))
        x = torch.matmul(attention, V)
        x = x.permute(0, 2, 1, 3).contiguous()
        x = x.view(bsz, -1, self.n_heads * (self.hid_dim // self.n_heads))
        return self.fc(x)

class Encoder(nn.Module):
    """protein feature extraction."""
    def __init__(self, protein_dim, hid_dim, n_layers, kernel_size, dropout, device):
        super().__init__()
        assert kernel_size % 2 == 1, "Kernel size must be odd (for now)"
        self.input_dim = protein_dim
        self.hid_dim = hid_dim
        self.kernel_size = kernel_size
        self.dropout = dropout
        self.n_layers = n_layers
        self.device = device
        self.scale = torch.sqrt(torch.FloatTensor([0.5])).to(device)
        self.convs = nn.ModuleList([
            nn.Conv1d(hid_dim, 2 * hid_dim, kernel_size, padding=(kernel_size - 1) // 2)
            for _ in range(self.n_layers)])
        self.dropout = nn.Dropout(dropout)
        self.fc = nn.Linear(self.input_dim, self.hid_dim)

    def forward(self, protein):
        # protein = [batch size, protein len, protein_dim]
        conv_input = self.fc(protein).permute(0, 2, 1)
        for i, conv in enumerate(self.convs):
            conved = conv(self.dropout(conv_input))       # [batch size, 2*hid dim, protein len]
            conved = F.glu(conved, dim=1)                 # [batch size, hid dim, protein len]
            conved = (conved + conv_input) * self.scale   # residual / highway
            conv_input = conved
        return conved.permute(0, 2, 1)                    # [batch size, protein len, hid dim]

class PositionwiseFeedforward(nn.Module):
    def __init__(self, hid_dim, pf_dim, dropout):
        super().__init__()
        self.fc_1 = nn.Conv1d(hid_dim, pf_dim, 1)
        self.fc_2 = nn.Conv1d(pf_dim, hid_dim, 1)
        self.do = nn.Dropout(dropout)

    def forward(self, x):
        x = x.permute(0, 2, 1)
        x = self.do(F.relu(self.fc_1(x)))
        x = self.fc_2(x)
        return x.permute(0, 2, 1)

class DecoderLayer(nn.Module):
    def __init__(self, hid_dim, n_heads, pf_dim, self_attention, positionwise_feedforward, dropout, device):
        super().__init__()
        self.ln = nn.LayerNorm(hid_dim)
        self.sa = self_attention(hid_dim, n_heads, dropout, device)
        self.ea = self_attention(hid_dim, n_heads, dropout, device)
        self.pf = positionwise_feedforward(hid_dim, pf_dim, dropout)
        self.do = nn.Dropout(dropout)

    def forward(self, trg, src, trg_mask=None, src_mask=None):
        # trg = [batch_size, compound len, atom_dim]
        # src = [batch_size, protein len, hid_dim]  (encoder output)
        trg = self.ln(trg + self.do(self.sa(trg, trg, trg, trg_mask)))
        trg = self.ln(trg + self.do(self.ea(trg, src, src, src_mask)))
        trg = self.ln(trg + self.do(self.pf(trg)))
        return trg

class Decoder(nn.Module):
    """compound feature extraction."""
    def __init__(self, atom_dim, hid_dim, n_layers, n_heads, pf_dim, decoder_layer,
                 self_attention, positionwise_feedforward, dropout, device):
        super().__init__()
        self.ln = nn.LayerNorm(hid_dim)
        self.output_dim = atom_dim
        self.hid_dim = hid_dim
        self.n_layers = n_layers
        self.n_heads = n_heads
        self.pf_dim = pf_dim
        self.decoder_layer = decoder_layer
        self.self_attention = self_attention
        self.positionwise_feedforward = positionwise_feedforward
        self.dropout = dropout
        self.device = device
        self.sa = self_attention(hid_dim, n_heads, dropout, device)
        self.layers = nn.ModuleList([
            decoder_layer(hid_dim, n_heads, pf_dim, self_attention,
                          positionwise_feedforward, dropout, device)
            for _ in range(n_layers)])
        self.ft = nn.Linear(atom_dim, hid_dim)
        self.do = nn.Dropout(dropout)
        self.fc_1 = nn.Linear(hid_dim, 256)
        self.fc_2 = nn.Linear(256, 2)

    def forward(self, trg, src, trg_mask=None, src_mask=None):
        trg = self.ft(trg)  # [batch size, compound len, hid dim]
        for layer in self.layers:
            trg = layer(trg, src)

        # Use norm to determine which atom is significant
        norm = torch.norm(trg, dim=2)          # [batch size, compound len]
        norm = F.softmax(norm, dim=1)
        trg = torch.squeeze(trg, dim=0)
        norm = torch.squeeze(norm, dim=0)
        sum_feature = torch.zeros((self.hid_dim)).to(self.device)
        for i in range(norm.shape[0]):
            sum_feature += trg[i,] * norm[i]
        sum_feature = sum_feature.unsqueeze(dim=0)
        label = F.relu(self.fc_1(sum_feature))
        label = self.fc_2(label)
        return label, norm, trg, sum_feature

class Predictor(nn.Module):
    def __init__(self, encoder, decoder, device, atom_dim=34):
        super().__init__()
        self.encoder = encoder
        self.decoder = decoder
        self.device = device
        self.weight = nn.Parameter(torch.FloatTensor(atom_dim, atom_dim))
        self.init_weight()

    def init_weight(self):
        stdv = 1. / math.sqrt(self.weight.size(1))
        self.weight.data.uniform_(-stdv, stdv)

    def gcn(self, input, adj):
        # input = [num_node, atom_dim], adj = [num_node, num_node]
        support = torch.mm(input, self.weight)
        return torch.mm(adj, support)

    def forward(self, compound, adj, protein):
        compound = self.gcn(compound, adj)
        compound = torch.unsqueeze(compound, dim=0)
        protein = torch.unsqueeze(protein, dim=0)
        enc_src = self.encoder(protein)                 # [batch, protein len, hid dim]
        out, norm, trg, sum_feature = self.decoder(compound, enc_src)
        return out, norm, trg, sum_feature

    def __call__(self, data, train=True):
        inputs, correct_interaction = data[:-1], data[-1]
        compound, adj, protein = inputs
        Loss = nn.CrossEntropyLoss()
        if train:
            predicted_interaction, norm = self.forward(compound, adj, protein)
            loss = Loss(predicted_interaction, correct_interaction)
            return loss
        else:
            predicted_interaction, norm, trg, sum_feature = self.forward(compound, adj, protein)
            correct_labels = correct_interaction.to('cpu').data.numpy().item()
            ys = F.softmax(predicted_interaction, 1).to('cpu').data.numpy()
            predicted_labels = np.argmax(ys)
            predicted_scores = ys[0, 1]
            return predicted_scores, norm, trg, sum_feature
```

## 4. Conv1D+GLU 编码器

蛋白质特征提取的卷积编码器，规范实现（含类型标注）：

```python
class Encoder(nn.Module):
    """Convolutional encoder for protein feature extraction"""
    def __init__(self, protein_dim: int, hid_dim: int, n_layers: int,
                 kernel_size: int, dropout: float, device: torch.device):
        super().__init__()
        # 便于通过 padding 保持序列长度不变
        assert kernel_size % 2 == 1, "Kernel size must be odd"
        self.input_dim = protein_dim
        self.hid_dim = hid_dim
        self.kernel_size = kernel_size
        self.n_layers = n_layers
        self.device = device
        self.scale = torch.sqrt(torch.FloatTensor([0.5])).to(device)
        self.convs = nn.ModuleList([
            nn.Conv1d(hid_dim, 2 * hid_dim, kernel_size, padding=(kernel_size - 1) // 2)
            for _ in range(n_layers)])
        self.dropout = nn.Dropout(dropout)
        self.fc = nn.Linear(protein_dim, hid_dim)
        self.ln = nn.LayerNorm(hid_dim)

    def forward(self, protein: torch.Tensor) -> torch.Tensor:
        """
        protein: [batch_size, seq_len, protein_dim]
        returns: [batch_size, seq_len, hid_dim]
        """
        conv_input = self.fc(protein).permute(0, 2, 1)
        for conv in self.convs:
            conved = conv(self.dropout(conv_input))
            conved = F.glu(conved, dim=-1)
            conved = (conved + conv_input) * self.scale
            conv_input = conved
        return self.ln(conved.permute(0, 2, 1))
```

**与第 3 节完整模型中的版本对比**：

| 差异点 | 本节规范版 | 完整模型版 |
| --- | --- | --- |
| GLU 维度 | `dim=-1` | `dim=1` |
| LayerNorm | forward 末尾有 `self.ln` | 无 |
| 残差缩放 | 相同（`1/√2`） | 相同 |
| 参数顺序 | 一致 | 一致 |

> 原文另记有 `for conv in convs`（缺少 `self.`）与 GLU 维度不一致的问题，此处保留两种写法供对照，以规范版为推荐。
