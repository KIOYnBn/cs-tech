---
createdAt: 2026-11-27T06:31:26.701Z
updatedAt: 2026-11-27T06:31:56.747Z
lastViewedAt: 2026-11-27T06:31:26.701Z
field: Python/第三方库/matplotlib
---
# Artist

本篇整理自原 `matplotlib turorial/Artist.md`。

## 常见的 Artist 及其创建方法

| Artist | Axes 辅助方法 |
| --- | --- |
| Annotation | `annotate` |
| rectangle | `bar` |
| lline2d/rectangle | `errorbar` |
| polygon | `fill` |
| rectangle | `hist` |
| AxesImage | `imshow` |
| line2d | `plot` |
| polycollection | `scatter` |
| 文本 | `text` |

## 更改 Artist 属性

- `Artist.set`
- 示例

```python
fig, ax = plt.subplots(figsize=(4, 2.5))
x = np.arange(0, 13, 0.2)
y = np.sin(x)
lines = ax.plot(x, y, '-', label="example", linewidth=0.2, color="blue")
line[0].set(color="green", linewidth=2)
```

## 查看 Artist 完整属性

`matplotlib.artist.getp`（原笔记写作 `matplolib.artist.getp`）

## 更改 Artist 数据

- `set_data`
- 示例

```python
fig, ax = plt.subplots(figsize=(4, 2.5))
x = np.arange(0, 13, 0.2)
y = np.sin(x)
lines = ax.plot(x, y, '-', label="example")
lines[0].set_data([x, np.cos(x)])   # 原笔记写作 set_ddata
```

## 手动添加 Artist

- 某些 Artist 没有 axes 辅助方法，需要手动添加
- `axes.Axes.add_artist`
- 示例

```python
import matplotlib.patches as mpatches

fig, ax = plt.subplots(figsize=(4, 2.5))
circle = mpatches.Circle((0.5, 0.5), 0.25, ec="none")
ax.add_artist(circle)
```

## 移除 Artist

- `remove`
