---
createdAt: 2026-11-27T03:21:07.649Z
updatedAt: 2026-11-27T06:07:40.938Z
lastViewedAt: 2026-11-27T03:27:38.345Z
field: web开发前端/CSS
---
# CSS-03-布局


## 一、布局概览

- **正常布局流**：默认，按从上到下依次排列。
- **参考链接**：[display（飞书文档）](https://qqeggtfhkm7.feishu.cn/docx/DUm1dvl0Dod7QcxSK3JcMSTEnBc#share-WdMRdRwWUo49VUxcCBKcy6Yuneg)

---

## 二、按元素类型划分的盒子

`display` 属性设置盒子类型。

| 类型 | 写法 | 说明 |
| --- | --- | --- |
| 区块盒子 | `display: block` | 换行；应用于 `<div>`、`<p>` 等 |
| 行内盒子 | `display: inline` | 应用于 `<span>`、`<a>` 等；没有 width/height，边距设置不会推开其他盒子 |
| 行内块盒子 | `display: inline-block` | 设置完整 width、height 和边距 |

---

## 三、弹性盒子 Flex

弹性盒子不需要手动设置 `width` 和 `height`。

- 弹性区块盒子：`display: flex`
- 弹性行内盒子：`display: inline-flex`

### 1. `flex-direction`：排列方向

- 默认：`row`。

### 2. 对齐属性对照

#### `align-items` 与 `align-content`

| 对比项 | `align-items` | `align-content` |
| --- | --- | --- |
| 作用对象 | 容器（父元素） | 容器 |
| 作用内容 | 控制子项在交叉轴（垂直方向）上的对齐方式 | 控制多行/多列整体在交叉轴上的分布方式 |
| 取值 | `start` 顶部对齐；`end` 底部对齐；`center` 居中；`stretch` 拉伸填满容器 | `flex-start` 所有行靠顶部；`flex-end` 所有行靠底部；`center` 所有行整体居中；`space-between` 行与行均匀分布、首尾无间距；`space-around` 行与行均匀分布、首尾有半间距 |
| 使用场景 | flex / grid | 多行/多列的 flex / grid |

#### `justify-content` 与 `justify-items`

| 对比项 | `justify-content` | `justify-items` |
| --- | --- | --- |
| 作用对象 | 容器 | 容器子项（grid） |
| 作用内容 | 主轴上子项整体如何分布 | 子项在自身单元格内主轴上的对齐方式 |
| 取值 | `flex-start` 靠头部；`flex-end` 靠尾部；`center` 居中；`space-between` 两端对齐、间距均分；`space-around` 间距均分、首尾半间距；`space-evenly` 所有间距完全相等 | `start` 单元格内靠头部；`end` 靠尾部；`center` 居中；`stretch` 拉伸填满单元格 |
| 场景 | flex / grid | grid |

### 3. `flex`：动态尺寸

```css
/* 每项占用空间相同 */
article {
  flex: 1;
}

/* 或者：justify-content: space-evenly; */

/* 假如总共有 3 个元素，第三个占总共的 1/2 */
article:nth-of-type(3) {
  flex: 2;
}
```

### 4. 水平或垂直居中

```css
div {
  display: flex;
  align-items: center;
  justify-content: space-around;
}
```

### 5. `order`：具体项排序

- `order` 默认是 `0`。

```css
button:first-child {
  order: 1;
}
```

---

## 四、Grid 布局

### 1. 属性

| 属性 | 说明 |
| --- | --- |
| `display: grid` | 启用网格 |
| `grid-template-rows` | 定义行轨道 |
| `grid-template-columns` | 定义列轨道 |
| `grid-gap` / `gap` | 间距 |
| `grid-column` / `grid-row` | 指定特定元素位置 |
| `grid-areas` / `grid-template-areas` | 命名区域 |

### 2. 命名区域示例

```css
.grid {
  display: grid;
  gap: 20px;
  grid-template-columns: 1fr 2fr;
  grid-template-areas:
    "aa aa"
    "bb cc"
    ". dd";
}

.one {
  grid-area: aa;
}
.two {
  grid-area: bb;
}
.three {
  grid-area: cc;
}
.four {
  grid-area: dd;
}
```

### 3. 放置示例

```css
.wrapper {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-rows: 100px 100px;
  gap: 10px;
}

.box1 {
  grid-column: 2 / 4;
  grid-row: 1;
}
```

---

## 五、浮动布局

### 1. `float`

| 值 | 说明 |
| --- | --- |
| `left` | 左浮动 |
| `right` | 右浮动 |
| `none` | 不浮动 |
| `inherit` | 继承 |

```css
.box {
  float: left;
  width: 150px;
  height: 150px;
  margin-right: 30px;
}
```

### 2. 清除浮动：`clear`

- 在要清除浮动影响的位置加入 `clear`。

```css
.wrapper {
  clear: left;
}
```

---

## 六、定位

### 1. Z 轴：`z-index`

- 元素重叠时的覆盖顺序。

```css
z-index: 1;
```

### 2. 定位类型

| 类型 | 说明 |
| --- | --- |
| 静态定位 | 默认 |
| 相对定位 `relative` | 将元素在正常位置上进行移动 |
| 绝对定位 `absolute` | 将元素放置在单独的图层 |
| 固定定位 `fixed` | 根据浏览器视口进行固定 |
| 粘性定位 `sticky` | 当元素相对视口位置到达某个值时，像固定定位一样 |

```css
/* 将元素向右下推 */
.positioned {
  position: relative;
  top: 30px;
  left: 30px;
}
.positioned {
  position: absolute;
  top: 30px;
  left: 30px;
}
```

#### 粘性定位应用：滚动布局

```css
dt {
  background-color: black;
  color: white;
  padding: 10px;
  position: sticky;
  top: 0;
  left: 0;
  margin: 1em 0;
}
```

### 3. 表格布局

- `table-row`。

---

## 七、多列布局

| 属性 | 说明 |
| --- | --- |
| `column-count` | 多少列 |
| `column-width` | 每列宽度 |

---

## 八、处理内容溢出：`overflow`

### 1. 属性

- `overflow-x`、`overflow-y`。

### 2. 值

| 值 | 说明 |
| --- | --- |
| `visible` | 默认 |
| `hidden` | 溢出内容隐藏 |
| `scroll` | 滚动 |
| `auto` | 自动调整 |

### 3. 示例

```css
.box {
  overflow: hidden;
}
.box {
  overflow: scroll;
}
.box {
  overflow-y: scroll;
}
.box {
  overflow: auto;
}
.box {
  overflow: hidden scroll;
}
```
