---
createdAt: 2026-11-27T07:06:06.450Z
updatedAt: 2026-11-27T07:07:55.474Z
lastViewedAt: 2026-11-27T07:07:41.575Z
field: Linux
---
# Linux 程序 离线运行

> 这里的"离线运行"指：让程序脱离当前终端会话，在后台持续运行。即使关闭终端或断开 SSH，程序也不会中断，特别适合模型训练、爬虫、数据处理等耗时任务。

## 一、启动程序：nohup 后台运行

核心命令：nohup

```bash
nohup python run.py > run.log 2>&1 &
```

各部分含义：

| 部分 | 作用 |
|---|---|
| `nohup` | 忽略挂断信号（SIGHUP），终端关闭后进程不会被杀掉 |
| `python run.py` | 实际要运行的程序（图中处于 conda 的 tensorflow2 环境） |
| `> run.log` | 标准输出重定向到 `run.log`（不加则默认写入 `nohup.out`） |
| `2>&1` | 把标准错误合并到标准输出，**报错信息也会写进 run.log** |
| `&` | 后台执行，终端立即返回，可以关掉窗口 |

命令执行后终端输出：

```
[1] 23045
```

`[1]` 是后台作业编号，`23045` 是进程 PID，后续管理进程要用它。

💡 **建议加 `-u`**：`nohup python -u run.py > run.log 2>&1 &`。Python 默认有输出缓冲，不加 `-u` 日志可能不会实时写入文件。

## 二、查看进程状态

```bash
pgrep -af "run.py"     # 推荐：-f 匹配完整命令行，-a 显示 PID 和命令行
ps aux | grep run.py   # 或者
```

图中输出 `23045 python run.py`，说明进程正在运行。**断开 SSH 重新登录后，就是用这条命令找回后台程序。**

## 三、实时查看日志

```bash
tail -f ~/pycharmproject/protobert/run.log
```

- `-f`：持续跟踪文件末尾新增内容
- 按 `Ctrl + C` 退出跟踪，**不会影响程序运行**
- 只看最后 100 行：`tail -n 100 run.log`

## 四、资源占用分析

```bash
top -p 23045    # 只监控指定 PID
htop            # 更友好的交互式工具（需安装：sudo apt install htop）
```

重点关注 `%CPU`、`%MEM`、`RES`（实际占用内存）、`TIME+`（累计 CPU 时间），按 `q` 退出。

## 五、终止进程

```bash
kill 23045        # 正常终止：发送 SIGTERM，程序有机会保存现场
kill -9 23045     # 强制终止：立即杀死，可能丢失数据或残留资源
```

**先试 `kill`，确认无效再用 `kill -9`。**

## 六、完整实操流程

```bash
# 1. 进入项目目录，激活环境（如 conda activate tensorflow2）
cd ~/pycharmproject/protobert

# 2. 后台启动，日志写入 run.log（记下输出的 PID）
nohup python -u run.py > run.log 2>&1 &

# 3. 确认进程在运行
pgrep -af "run.py"

# 4. 实时盯日志（Ctrl+C 退出查看，程序继续跑）
tail -f ~/pycharmproject/protobert/run.log

# 5. 查看资源占用
top -p 23045

# 6. 任务结束后停止进程
kill 23045
```