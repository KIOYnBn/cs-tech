---
createdAt: 2026-11-27T03:20:57.834Z
updatedAt: 2026-11-27T06:07:58.947Z
lastViewedAt: 2026-11-27T03:27:22.432Z
field: web开发前端/CSS
---
# CSS-02-盒模型与属性值

## 一、盒模型

### 1. 类型

| 类型 | 说明 |
| --- | --- |
| 标准盒模型 | `width` 和 `height` 仅定义内容区域的尺寸 |
| 替代盒模型 | `width` 和 `height` 定义整个盒的大小 |

- 默认使用标准盒。
- 替代盒使用 `box-sizing` 设置：

```css
.box {
  box-sizing: border-box;
}
```

### 2. 属性

| 属性 | 说明 |
| --- | --- |
| `width` / `height` | 宽高 |
| `padding` | 内边距，含 `padding-top`、`padding-right`、`padding-bottom`、`padding-left` |
| `border` | 边框，含 `border-top/right/bottom/left` 与 `border-width`、`border-style`、`border-color` |
| `margin` | 外边距，含 `margin-top`、`margin-right`、`margin-bottom`、`margin-left` |
| `border-radius` | 圆角 |

```css
/* 四个边框都设置 */
.box {
  border: 1px solid black;
}

.box {
  border-top: 1px solid black;
}

.box {
  border-width: 1px;
  border-style: solid;
  border-color: black;
}
```

```css
/* 盒子四个角都有 10px 的圆角半径 */
.box {
  border-radius: 10px;
}

/* 右上角水平半径 1em，垂直半径 10% */
.box {
  border-top-right-radius: 1em 10%;
}
```

```css
.box {
  width: 350px;
  height: 150px;
  margin: 10px;
  padding: 25px;
  border: 5px solid black;
}
```

### 3. 背景

| 属性 | 说明 |
| --- | --- |
| `background-color` | 颜色 |
| `background-image` | 图像，`url(...)` |
| `background-repeat` | `no-repeat` 不重复、`repeat-x` 水平重复、`repeat-y` 垂直重复、`repeat` 双向重复 |
| `background-size` | 尺寸；关键词 `cover` 覆盖盒子（部分超出）、`contain` 保持在盒子内（出现空隙） |
| `background-position` | 定位 |
| `background-attachment` | `scroll` 随页面滚动、`fixed` 背景固定、`local` 固定在设置元素上 |

```css
.a {
  background-image: url(...);
}

.box {
  background-size: 80px 10em;
}
```

```css
.box {
  background-position: 0 0;
}
.box {
  background-position: top center;
}
.box {
  background-position: 20px 10%;
}
.box {
  background-position: 20px top;
}
.box {
  background-position: top 20px right 10px;
}
/* 盒子右移 40 像素 */
.box {
  background-position: right 40px;
}
.box {
  background-position: 120px 1em;
}
```

```css
.gradient {
  background-image: linear-gradient(
    90deg,
    rgb(119 0 255 / 39%),
    rgb(0 212 255 / 100%)
  );
}
```

### 4. 媒体与表格元素

#### 图片大小控制：`object-fit`

| 值 | 说明 |
| --- | --- |
| `fill` | 拉伸图像填满 |
| `contain` | 图像缩放到放入盒子中 |
| `cover` | 充满盒子，不拉伸，裁剪 |

```css
.contain {
  object-fit: contain;
}
```

#### 合并表格边框：`border-collapse`

```css
table {
  border-collapse: collapse;
}
```

---

## 二、函数与变换

### 1. `calc()`：数学运算

```css
.box {
  width: calc(90% - 30px);
}
```

### 2. `min()`、`max()`、`clamp()`

- 用于取中间值/范围约束。

### 3. `transform` 属性的值

| 函数 | 说明 |
| --- | --- |
| `rotate()` | 旋转 |
| `translate()` | 水平或垂直移动 |
| `scale()` | 放大或缩小 |

```css
.box {
  margin: 30px;
  width: 100px;
  height: 100px;
  background-color: rebeccapurple;
  transform: rotate(0.8turn);
}
```

---

## 三、常见属性和值类型

### 1. 长度

- 最常用：`px`——像素，`1px = 0.2646mm`。

#### 相对长度单位

| 类别 | 单位 | 说明 |
| --- | --- | --- |
| 字体大小 | `em` | 相对本元素字体大小 |
| 字体大小 | `rem` | 相对根元素字体大小 |
| 宽度 | `px` | 固定大小 |
| 宽度 | `vw` | 相对视口宽度，百分比 |
| 高度 | `vh` | 视口高度 |
| 行高 | `lh` | 相对元素行高 |
| 行高 | `rlh` | 相对根元素行高 |

```css
.vm {
  width: 10vw;
}
.px {
  width: 200px;
}
```

### 2. 百分比

```css
.percent {
  width: 40%;
}
```

### 3. 数字

- 不透明度：`0` ~ `1.0`，`1.0` 表示完全不透明（原笔记：0-1.0）。

### 4. 颜色

| 类型 | 说明 |
| --- | --- |
| 颜色关键词 | 如 `green` |
| 十六进制 RGB | 如 `#ff0000` |
| `rgb()` | 三个值，由 `/` 分割的第四个参数表示透明度；值为十进制或百分数，透明度 0~1 |
| `hsl()` | 色相 0~360、白度 0~100%、黑度 0~100% |
| `hwb()` | 色相、饱和度 0~100%、亮度 0~100%，`/` 后第四个元素为透明度 |
| `lch()` | 包含色相组件的函数 |

```css
.one {
  background-color: rgb(2 121 139 / 0.5);
}
```

---

## 四、尺寸设置

```css
.box {
  border: 5px solid darkblue;
  min-height: 100px;
  width: 200px;
}
```

---

## 五、文本设置

| 属性 | 说明/值 |
| --- | --- |
| `color` | 文字颜色 |
| `font-family` | 字体，如 `arial, sans-serif` |
| `@font-face` | 在线字体设置，具体较麻烦，暂忽略 |
| `font-size` | 字体大小，如 `1.4rem` |
| `font-style` | `normal` 关闭斜体；`italic` 有斜体则用斜体，否则 `oblique` 模拟；`oblique` 模拟斜体 |
| `font-weight` | `normal`、`bold`、`lighter`、`bolder` |
| `text-transform` | `uppercase`、`lowercase`、`capitalize`、`full-width` |
| `text-decoration` | `underline`、`overline`、`line-through` |
| `text-shadow` | 文字阴影 |
| `text-align` | `left`、`right`、`center`、`justify`（所有文本行宽度相同） |
| `line-height` | 行高 |
| `letter-spacing` | 字母间距 |
| `word-spacing` | 单词间距 |

```css
p {
  color: red;
  font-family: arial, sans-serif;
  font-size: 1.4rem;
}

text-shadow: 4px 4px 5px red;
```

---

## 六、列表设置

| 属性 | 说明 |
| --- | --- |
| `list-style-type` | 项目符号类型 |
| `list-style-position` | 项目符号在列表内还是外：`outside` / `inside` |
| `list-style-image` | 自定义项目符号 |

```css
ol {
  list-style-type: upper-roman;
}

ul {
  list-style-image: url(star.svg);
}
```

计数相关（HTML 属性）：

```html
<ol start="4">
  <li>...</li>
</ol>

<ol start="4" reversed>
  <li>...</li>
</ol>

<ol>
  <li value="2">...</li>
</ol>
```

- `start`：起始值。
- `reversed`：反向计数。
- `value`：为特定项设值。
