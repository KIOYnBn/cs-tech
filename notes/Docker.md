---
createdAt: 2026-11-27T08:37:58.905Z
updatedAt: 2026-11-27T08:39:51.282Z
lastViewedAt: 2026-11-27T08:38:34.828Z
---
# Docker


## 0. 速查导航

| 我想做的事 | 直接跳到 |
|---|---|
| 快速了解 Docker 是什么 | [1. Docker 速览](#1-docker-速览p2p3) |
| Linux 上从零安装 Docker | [2. Linux 安装与配置](#2-linux-安装与配置p3p11) |
| Windows 上从零安装 Docker | [3. Windows 安装与配置](#3-windows-安装与配置p11p16) |
| 镜像拉取失败 / 网络不通 | [4. 故障排查速查](#4-故障排查速查p10p17) |
| 只想复制命令 | [5. 命令速查索引](#5-命令速查索引) |
| 安装后核对是否配好 | [6. 安装后核对清单](#6-安装后核对清单) |

---

## 1. Docker 速览

Docker 是一个开源的容器化平台，用于开发、部署和运行应用程序。它通过容器技术将应用及其依赖打包到一个可移植的标准化单元中，确保应用在不同环境中高效运行。

### 1.1 核心特点

| 特点 | 说明 |
|---|---|
| 轻量级 | 容器共享宿主机的操作系统内核，相比传统虚拟机更节省资源 |
| 可移植性 | 容器可在任何支持 Docker 的环境中运行，确保一致性 |
| 隔离性 | 每个容器独立运行，互不干扰，提高安全性 |
| 快速部署 | 秒级启动和停止，适合动态扩展和微服务架构 |
| 版本控制 | 支持镜像分层存储和版本管理，便于回滚和更新 |

### 1.2 主要应用场景

| 场景 | 说明 |
|---|---|
| 微服务架构 | 将单体应用拆分为多个独立容器，提高可维护性 |
| 持续集成/持续部署（CI/CD） | 自动化构建、测试和发布流程 |
| 开发环境标准化 | 避免"在我机器上能运行"的问题，统一开发、测试和生产环境 |
| 云原生应用 | 与 Kubernetes 等编排工具结合，实现弹性伸缩和高可用 |
| 快速实验与测试 | 快速创建一次性环境，测试新技术或配置 |

### 1.3 优势

- 资源高效：相比虚拟机，容器占用更少 CPU 和内存
- 跨平台支持：可在 Linux、Windows 和 macOS 上运行
- 丰富的生态系统：提供 Docker Hub 等镜像仓库，方便共享和获取预构建镜像
- 简化运维：通过 Dockerfile 定义环境，减少手动配置错误

---

## 2. Linux 安装与配置

> 建议顺序：检查系统 → 卸载旧版 → 装依赖 → 配软件源 → 装引擎 → 启动服务 → 配镜像加速 →（可选）权限/内核/存储/防火墙优化。

### 2.1 安装前准备

#### 2.1.1 系统要求检查

内核版本需 **3.10+**。

```bash
# Ubuntu：检查内核版本与系统版本
uname -r
lsb_release -a          # 输出示例：Ubuntu 22.04 LTS

# CentOS：检查内核版本与系统版本
uname -r                # 输出示例：3.10.0-1160.el7.x86_64
cat /etc/redhat-release # 输出示例：CentOS Linux release 7.9.2009 (Core)
```

#### 2.1.2 卸载旧版本 Docker

如果之前装过 Docker，先卸载旧版本。

```bash
# Ubuntu
sudo apt-get remove docker docker-engine docker.io containerd runc

# CentOS
sudo yum remove -y docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-engine
```

#### 2.1.3 安装基础依赖

```bash
# Ubuntu
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg lsb-release

# CentOS
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

### 2.2 安装流程

#### 2.2.1 配置国内镜像源（阿里云）

```bash
# Ubuntu：添加阿里云 GPG 密钥
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg \
  | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Ubuntu：设置仓库
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

# CentOS：设置阿里云 Docker 仓库
sudo yum-config-manager --add-repo \
  http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
sudo yum makecache fast
```

> 说明：这一步配置的是 **Docker 软件包下载源**，加速安装；后面 2.2.4 配置的是 **容器镜像仓库加速器**，加速 `docker pull`，两者不要混淆。

#### 2.2.2 安装 Docker 引擎

```bash
# Ubuntu
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# CentOS：安装最新版本
sudo yum install -y docker-ce docker-ce-cli containerd.io
```

#### 2.2.3 启动 Docker 服务

```bash
sudo systemctl start docker      # 启动服务
sudo systemctl enable docker     # 设置开机自启
sudo systemctl status docker     # 验证服务状态，应显示 active (running)
```

#### 2.2.4 配置镜像加速器

**方案一：临时加速（单次有效）**

```bash
# 通用格式：sudo docker pull [镜像源地址]/镜像名:版本号
sudo docker pull docker.m.daocloud.io/hello-world:latest
```

**方案二：永久配置（推荐）**

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": [
    "https://docker.m.daocloud.io",
    "https://docker.imgdb.de",
    "https://docker-0.unsee.tech",
    "https://docker.hlmirror.com"
  ]
}
EOF

# 应用配置（必须执行）
sudo systemctl daemon-reload
sudo systemctl restart docker
```

**验证配置**

```bash
sudo docker pull hello-world
docker info | grep Mirrors -A 3   # 能看到上面配置的镜像地址即成功
```

#### 2.2.5 用户权限配置：免 sudo 运行 Docker

```bash
sudo groupadd docker              # 添加 docker 用户组
sudo usermod -aG docker $USER     # 将当前用户加入 docker 组
newgrp docker                     # 激活组权限变更
docker run hello-world            # 验证：输出 Hello from Docker! 即成功
```

#### 2.2.6 内核参数优化（可选）

```bash
sudo tee /etc/sysctl.d/docker.conf <<-'EOF'
net.ipv4.ip_forward=1
net.bridge.bridge-nf-call-iptables=1
net.bridge.bridge-nf-call-ip6tables=1
vm.max_map_count=262144
fs.may_detach_mounts=1
EOF
sudo sysctl --system
```

#### 2.2.7 存储驱动配置（可选）

```bash
# 1) 先检查当前驱动，推荐 overlay2
docker info | grep "Storage Driver"

# 2) 如需切换为 overlay2：停服 → 备份数据 → 重建目录 → 写配置 → 启服
sudo systemctl stop docker
sudo mv /var/lib/docker /var/lib/docker.bak
sudo mkdir -p /var/lib/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
EOF
sudo systemctl start docker
```

> ⚠️ 若 `/etc/docker/daemon.json` 中已有 `registry-mirrors`，请把 `storage-driver` 等字段**合并进同一个 JSON**，不要直接覆盖，否则镜像加速会失效。

#### 2.2.8 防火墙配置（可选）

```bash
# 开放 Docker 端口（2375 为管理端口，按需开放 80/443/3306 等）
sudo firewall-cmd --zone=public --add-port=2375/tcp --permanent
sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
sudo firewall-cmd --zone=public --add-port=443/tcp --permanent
sudo firewall-cmd --zone=public --add-port=3306/tcp --permanent
sudo firewall-cmd --reload

# Docker 与 FirewallD 集成
sudo firewall-cmd --permanent --zone=trusted --add-interface=docker0
sudo firewall-cmd --permanent --zone=trusted --add-port=2376/tcp
sudo firewall-cmd --reload
```

### 2.3 Linux 故障排查

| 问题 | 处理命令 |
|---|---|
| 容器无法访问外网 | `sudo firewall-cmd --zone=public --add-masquerade --permanent && sudo firewall-cmd --reload && sudo systemctl restart docker` |
| 存储空间不足 | 查看：`docker system df`；清理：`docker system prune -a -f --volumes` |
| 镜像拉取失败（DNS） | 查看 `sudo cat /etc/resolv.conf`；必要时 `sudo tee /etc/resolv.conf <<< "nameserver 8.8.8.8"` |
| 镜像拉取失败（镜像源） | 临时使用其他镜像源：`sudo docker pull docker.m.daocloud.io/hello-world:latest`，或按 2.2.4 做永久配置 |
| 查 Docker 服务日志 | `journalctl -u docker.service --since "2025-08-01" --no-pager` |
| 查容器日志 | `docker logs -f [容器ID] --tail 100` |

> ⚠️ `docker system prune -a -f --volumes` 会删除未被使用的镜像、容器和数据卷，**执行前确认重要数据已备份**。

---

## 3. Windows 安装与配置

### 3.1 安装前准备

**硬件与系统要求**

- 操作系统：Windows 10/11（专业版、企业版或家庭版支持 WSL 2）
- 内存：至少 4GB RAM
- 磁盘空间：至少 20GB 可用空间
- 虚拟化：BIOS/UEFI 中启用虚拟化
  - 验证：任务管理器 → 性能 → CPU → 虚拟化状态为「已启用」

**启用 Hyper-V 和 WSL 2**

Docker Desktop 依赖 Hyper-V 和 WSL 2。控制面板 → 程序 → 启用或关闭 Windows 功能，勾选：

- Hyper-V
- Windows Subsystem for Linux（适用于 Linux 的 Windows 子系统）
- 虚拟机平台

完成后重启电脑。

**安装 WSL 2**

```powershell
wsl --status                       # 检查 WSL 版本
wsl.exe --install                  # 安装 WSL
wsl --set-default-version 2        # 设置默认版本为 WSL 2
```

### 3.2 下载与安装 Docker Desktop

1. **下载**：官网 `docker.com/products/docker-desktop`，选择 Docker Desktop for Windows。
2. **安装**：双击安装包进入向导。
   - 勾选 `Use WSL 2 instead of Hyper-V`（推荐）
   - 勾选 `Add Docker Desktop to PATH`
   - 完成后点击 `Close and restart` 重启电脑
3. **验证安装**：
   - 启动 Docker Desktop，系统托盘出现小鲸鱼图标且显示 "Docker Desktop running"
   - 检查版本：

```powershell
docker --version
docker-compose --version
```

   - 测试运行：

```powershell
docker run hello-world
# 输出 Hello from Docker! 表示安装成功
```

### 3.3 配置 Docker

#### 3.3.1 配置镜像加速器（推荐）

Docker 默认从 Docker Hub 拉取镜像，国内用户可配置镜像源提速。

步骤：打开 Docker Desktop → `Settings` → `Docker Engine` → 编辑 `daemon.json`，加入：

```json
"registry-mirrors": [
  "https://docker.m.daocloud.io",
  "https://hub-mirror.c.163.com"
]
```

点击 `Apply` 应用。

#### 3.3.2 配置 WSL 2 集成

- Docker Desktop → `Settings` → `Resources` → `WSL Integration`
- 启用默认 WSL 发行版（如 Ubuntu）
- 可按需添加其他发行版（如 Debian、Kali）
- 点击 `Apply & Restart`

### 3.4 Windows 常见问题

| 错误提示 | 原因 | 解决方法 |
|---|---|---|
| `WSL 2 requires virtualization to be enabled in the BIOS` | 虚拟化未开启 | 进入 BIOS（开机按 Del/F2/F12）开启虚拟化（Intel VT-x / AMD-V）；任务管理器 → 性能 → CPU 确认「已启用」 |
| `Cannot connect to the Docker daemon at unix:///var/run/docker.sock` | Hyper-V 与其他虚拟机软件冲突 | 关闭其他虚拟机软件（如 VirtualBox）；以管理员身份运行 PowerShell：`bcdedit /set hypervisorlaunchtype off`，然后 `Restart-Computer -Force` |
| Docker Desktop 无法启动 | WSL 状态异常 | 执行 `wsl --shutdown`；必要时重新安装 WSL 2 内核 |
| `Error response from daemon: Get ... Client.Timeout exceeded while awaiting headers` | 拉取镜像超时 | 在 Docker Engine 配置镜像加速器；必要时加 DNS |

DNS 配置示例（写入 Docker Engine 的 JSON 配置）：

```json
"dns": ["8.8.8.8", "114.114.114.114"]
```

---

## 4. 故障排查速查

按现象找方案：

| 现象 | 优先检查 | 处理 |
|---|---|---|
| `docker pull` 卡住/超时 | 网络 / DNS / 镜像源 | 配 `registry-mirrors`；检查 `/etc/resolv.conf`；Windows 端加 `dns` 配置 |
| 容器无外网 | 防火墙 NAT | Linux：`firewall-cmd --zone=public --add-masquerade --permanent` |
| 磁盘爆满 | 无用镜像/容器/卷 | `docker system df` → `docker system prune -a -f --volumes`（先备份） |
| Docker 服务起不来 | 服务状态 / 日志 | `systemctl status docker`、`journalctl -u docker.service --no-pager` |
| Windows 报 WSL 2 虚拟化错误 | BIOS 虚拟化 | BIOS 开启 VT-x/AMD-V |
| Windows 报 daemon 无法连接 | Hyper-V 冲突 | 关闭其他虚拟机软件；`bcdedit /set hypervisorlaunchtype off` |
| Docker Desktop 启动失败 | WSL 卡死 | `wsl --shutdown`，必要时重装 WSL 2 内核 |
| 每次都要 sudo | 用户不在 docker 组 | `sudo usermod -aG docker $USER && newgrp docker` |

---

## 5. 命令速查索引

### 系统与服务

```bash
uname -r                                   # 查看内核版本
lsb_release -a                             # 查看 Ubuntu 系统版本
cat /etc/redhat-release                    # 查看 CentOS 系统版本
sudo systemctl start docker                # 启动 Docker
sudo systemctl enable docker               # 开机自启
sudo systemctl status docker               # 查看服务状态
sudo systemctl daemon-reload               # 重载配置
sudo systemctl restart docker              # 重启 Docker
```

### 镜像与容器

```bash
sudo docker pull [镜像源]/镜像名:版本号      # 拉取镜像（临时指定镜像源）
docker run hello-world                     # 测试运行
docker run -it ubuntu bash                 # 交互式启动 Ubuntu 容器
docker images                              # 列出镜像
docker info | grep Mirrors -A 3            # 查看镜像加速配置
docker info | grep "Storage Driver"        # 查看存储驱动
docker logs -f [容器ID] --tail 100         # 跟踪容器日志
docker system df                           # 查看磁盘占用
docker system prune -a -f --volumes        # 清理无用资源（谨慎）
```

### 日志与网络

```bash
journalctl -u docker.service --since "2025-08-01" --no-pager   # 服务日志
sudo cat /etc/resolv.conf                                       # 查看 DNS
sudo tee /etc/resolv.conf <<< "nameserver 8.8.8.8"              # 设置 DNS
sudo firewall-cmd --zone=public --add-masquerade --permanent    # 允许容器访问外网
sudo firewall-cmd --reload                                      # 重载防火墙
```

### Windows / PowerShell

```powershell
wsl --status
wsl.exe --install
wsl --set-default-version 2
wsl --shutdown
docker --version
docker-compose --version
docker run hello-world
bcdedit /set hypervisorlaunchtype off
Restart-Computer -Force
```

---

## 6. 安装后核对清单

**Linux**

- [ ] `docker --version` 能输出版本号
- [ ] `systemctl status docker` 显示 `active (running)`
- [ ] `docker run hello-world` 输出 `Hello from Docker!`
- [ ] `docker info | grep Mirrors -A 3` 能看到配置的镜像加速地址
- [ ] （按需）`newgrp docker` 后无需 sudo 即可运行 `docker run hello-world`
- [ ] （按需）`docker info | grep "Storage Driver"` 为 `overlay2`

**Windows**

- [ ] 托盘小鲸鱼显示 Docker Desktop running
- [ ] `docker --version` / `docker-compose --version` 正常
- [ ] `docker run hello-world` 成功
- [ ] Docker Engine 中已添加 `registry-mirrors`
- [ ] Settings → Resources → WSL Integration 已启用目标发行版

---

## 7. 学习延伸

原文末尾推荐：李程远《容器技术核心实践》（"换成实战高手课"）。想系统学习 Docker，可继续掌握：镜像与容器生命周期管理、Dockerfile 编写、数据卷与网络、Compose 编排，再过渡到 Kubernetes。

> 备注：本笔记基于原始 PDF 整理，原文中的部分命令输出截图（如 yum 安装日志）未逐字抄录，仅保留关键命令与结论；涉及删除数据的清理命令已加警示。
