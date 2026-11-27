---
createdAt: 2026-11-27T07:01:39.803Z
updatedAt: 2026-11-27T07:02:43.348Z
lastViewedAt: 2026-11-27T07:02:18.303Z
field: 大模型/tensorflow
---
# tensorflow配置

# 《彻底搞定 TensorFlow-GPU 环境（史上最全）》笔记

> 来源：博客园 she35，发布于 2020-04-30
> 原文链接：https://www.cnblogs.com/sunshe35/articles/12808780.html

## 核心思路

GPU 环境的配置本质都是处理好三层关系：

```
TensorFlow-GPU 版本  ↔  CUDA Toolkit 版本  ↔  cuDNN 版本  ↔  NVIDIA 驱动版本
```

其中 **NVIDIA 驱动是前向兼容的**：只需满足 CUDA 要求的**最低驱动版本**即可，版本更高无所谓。

## 方案一：conda 一键安装（推荐）

优点：conda 已把 tensorflow、cudatoolkit、cudnn 的版本绑定好，只需关注驱动是否匹配。

1. 安装（建议配置国内镜像；Miniconda 装完约 7G）：

   ```bash
   conda install tensorflow-gpu=2.1.0   # 会自动装上 cudatoolkit=10.1.* 及对应 cudnn
   ```

2. 确认驱动满足要求：
   - 最简单：保持驱动为最新，官网下载 https://www.geforce.cn/drivers
   - 查最低要求：CUDA 10.1 要求驱动 **≥ 418.96**（参考 CUDA Toolkit Release Notes）
   - 用命令查看：`nvidia-smi`（在 Jupyter Notebook 中需写成 `!nvidia-smi`）
   - 输出的 `CUDA Version` 表示驱动最高支持的 CUDA 版本，需 ≥ 实际使用的 cudatoolkit 版本

   版本匹配官方表：https://www.tensorflow.org/install/source_windows

## 方案二：把 conda 的 CUDA 环境移植出来

适用场景：已用 conda 装好 tensorflow-gpu，想在**其他 Python 环境**中复用（前提是驱动已达标）。

- **直接用现有 Anaconda 目录**：把 `D:\Anaconda3\Library\bin` 加入系统 PATH，然后：

  ```bash
  pip install tensorflow-gpu==2.1.0
  ```

- **移植到其他目录**：把以下两个文件夹的全部内容拷贝到 `D:\cudatoolkit\v10.1`：

  ```
  D:\Anaconda3\pkgs\cudatoolkit-*\Library
  D:\Anaconda3\pkgs\cudnn-*\Library
  ```

  再把 `D:\cudatoolkit\v10.1\bin` 加入 PATH，同样执行 `pip install tensorflow-gpu==2.1.0`。

## 方案三：从官网手动安装

1. **下载**
   - CUDA Toolkit（文章选 10.1 update2）：https://developer.nvidia.com/cuda-toolkit-archive
   - cuDNN（需注册，版本须与 CUDA 匹配）：https://developer.nvidia.com/rdp/cudnn-download

2. **安装**
   - CUDA 安装时只勾选 **Development** 和 **Runtime** 两个模块即可
   - ⚠️CUDA 安装包自带驱动：若本机驱动更新，**不要勾选驱动**，否则安装失败；若本机驱动较旧，可按需勾选
   - cuDNN：解压后把里面的文件全部合并进 CUDA 安装目录（如 `D:\cudatoolkit`）

3. **配置环境变量**：PATH 加入 `D:\cudatoolkit\bin`

4. **安装 tensorflow-gpu**：

   ```bash
   pip install tensorflow-gpu==2.1.0
   ```

5. 驱动检查同方案一。

## 验证安装

```bash
conda list cud        # 应看到 cudatoolkit 10.1.243、cudnn 7.6.5
conda list tensor     # 应看到 tensorflow-gpu 2.1.0 等
nvidia-smi            # 查看驱动版本与其支持的 CUDA Version
```

```python
import tensorflow as tf
tf.config.list_physical_devices()
# 输出同时包含 CPU:0 和 GPU:0 即表示 GPU 可用
```

## 避坑要点

| 注意点 | 说明 |
|---|---|
| 原文命令笔误 | 文中写成了 `tensorlfow-gpu`，正确拼写是 `tensorflow-gpu` |
| 驱动兼容性 | 驱动只需满足最低版本（CUDA 10.1 → ≥418.96），越高越好，无需与 CUDA 版本数字完全一致 |
| PATH 生效 | 添加环境变量后需重启终端/内核 |
| 时效性 | 文章基于 2020 年的 TF 2.1 + CUDA 10.1，当前新版本 CUDA/TF 组合需重新核对官方兼容表 |

**速查搭配（文章示例）**：TensorFlow-GPU 2.1.0 + CUDA 10.1 + cuDNN 7.6.5 + 驱动 445.87（支持 CUDA 11.0）✅