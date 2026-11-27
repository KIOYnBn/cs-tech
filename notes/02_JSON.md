---
createdAt: 2026-11-27T06:30:11.094Z
updatedAt: 2026-11-27T06:30:20.257Z
lastViewedAt: 2026-11-27T06:30:11.094Z
field: Python/文件与数据
---
# JSON

本篇合并原 `json.md` 与 `未命名.md` 中的 JSON 条目。

## 方法速查

| 方法 | 作用 | 适用场景 |
| --- | --- | --- |
| `json.dumps()` | 将 Python 对象转为 JSON 字符串 | 内存中处理 JSON 数据 |
| `json.dump()` | 将 Python 对象写入文件（JSON 格式） | 保存数据到 JSON 文件 |
| `json.loads()` | 将 JSON 字符串转为 Python 对象 | 解析接口返回的 JSON 字符串 |
| `json.load()` | 从文件读取 JSON 数据并转为 Python 对象 | 读取 JSON 文件 |

## 作用

方便内容传输。

## 常用函数

```python
json.dumps(obj)   # 将 python 数据类型转化为 json 类型
json.loads(s)     # 将 json 类型转化为 python 类型
json.dump(obj, file)  # 同 dumps，并将结果放在 file 中
json.load(file)   # 同 loads，从 file 中读取数据
```

带参数示例：

```python
variate = json.dumps(lst, ensure_ascii=False, indent=4)
```

- `ensure_ascii=False`：正常显示中文（原笔记写作 `ensure.ascii`）
- `indent=4`（原笔记写作 `ident`）：增加数据缩进，排版
