---
createdAt: 2026-11-27T03:20:57.767Z
updatedAt: 2026-11-27T03:27:37.171Z
lastViewedAt: 2026-11-27T03:27:33.298Z
field: web开发前端
---
# CSS-01-基础选择器与层叠


## 一、CSS 概述

- **功能**：界面优化。

### 练习目标（原笔记「测试部分的能力要求」）

| 挑战 | 涉及知识点 |
| --- | --- |
| 基本的 CSS 理解 | CSS 外部导入、`line-height` |
| 创建精美的信纸 | 多个图片背景、盒子阴影 `filter: drop-shadow`、`box-shadow` |
| 一个漂亮的盒子 | `width`、`height`、`display`、横向/纵向居中 `align-items` / `justify-content`、`color`、对角线渐变 `to bottom right`、多盒阴影 `x y radius color` |

> CSS 的三种应用方式（外部/内部/内联）已统一整理在 [01-HTML-01](HTML-01-文档基础与通用规则.md#在-html-中引入-css-与-javascript)。

### 注释

```css
/* 注释内容 */
```

---

## 二、选择器

选择器用于定义元素的属性值。

### 1. 元素/类选择器

```css
h1 {
  color: red;
  font-size: 2.5em;
}
```

- 多选择器同时定位：逗号分隔。

```css
p,
li {
  color: green;
}
```

- 自定义类：

```css
.special {
  color: orange;
  font-weight: bold;
}

h1.light {
  color: orange;
  font-weight: bold;
}
```

```html
<ul>
  <li class="special">Item two</li>
</ul>

<h1 class="light">class light</h1>
```

- 多类选择器（空格表示「且」）：

```css
.notebox.warning {
  border-color: orange;
  font-weight: bold;
}
```

```html
<!-- 空格表示且 -->
<div class="notebox warning">oooo</div>
```

### 2. ID 选择器

```css
#one {
  background-color: yellow;
}

h1#heading {
  color: rebeccapurple;
}
```

```html
<h1 id="heading">ID selector</h1>
<p id="one">oooo</p>
```

### 3. 通用选择器 `*`

```css
* {
  margin: 0;
}
```

### 4. 选择器组合

| 组合 | 写法 | 说明 |
| --- | --- | --- |
| 后代选择器 | 空格 | `li em` 影响 `li` 中的 `em`，不影响 `li` |
| 子代选择器 | `>` | `ul > li` 仅直接子元素 |
| 相邻选择器 | `+` | `h1 + ul + p` 严格按顺序出现 |
| 通用兄弟选择器 | `~` | `h1 ~ p` 选中 `h1` 后的所有 `p` |

```css
/* 影响 li 中的 em，不影响 li */
li em {
  color: rebeccapurple;
}

ul > li {
  border-top: 5px solid red;
}

/* 影响 h1 后的 ul 后的 p，即严格按照顺序出现的 p */
h1 + ul + p {
  border-left: 3px solid;
}

/* 选中 h1 后的所有 p */
h1 ~ p {
  font-weight: bold;
  background-color: #333;
  color: #fff;
  padding: 0.5em;
}
```

### 5. 属性选择器

| 写法 | 匹配规则 |
| --- | --- |
| `li[class]` | 匹配带有 `class` 属性的元素 |
| `li[class="a"]` | `class` 属性值只为 `a` |
| `li[class~="a"]` | `class` 属性值中有 `a`（空格分隔的词） |
| `li[class^="a"]` | `class` 属性以 `a` 开头 |
| `li[class$="a"]` | `class` 属性以 `a` 结尾 |
| `li[class*="a"]` | `class` 属性含有 `a` |
| `a[href$="pdf." i]` | 忽略大小写 |

```css
li[class] {
  font-size: 120%;
}

li[class="a"] {
  background-color: yellow;
}

li[class~="a"] {
  color: red;
}

li[class^="a"] {
  font-size: 120%;
}

li[class$="a"] {
  background-color: yellow;
}

li[class*="a"] {
  color: red;
}

a[href$="pdf." i] {
  background: url("pdf-icon.png");
}
```

### 6. 伪类与伪元素

伪类和伪元素用于选择**特定状态下**的类或元素。

#### 伪类：一个冒号 `:`

**简单伪类**

- `:first-child`、`:last-child`、`:invalid`

```css
article p:first-child {
  font-size: 120%;
  font-weight: bold;
}
```

```html
<!-- 只会影响第一个 p -->
<article>
  <p>fdasojfsdojfjasofjoisajfiosajdfo;</p>
  <p>oajfoasjfoisajfoisajf</p>
</article>
```

**用户行为伪类（与用户交互）**

| 伪类 | 说明 |
| --- | --- |
| `:link` | 有目的地的链接 |
| `:visited` | 已访问过 |
| `:hover` | 指针挪到元素上激活 |
| `:focus` | 键盘选定元素时激活 |
| `:active` | 点击（激活） |

```css
a:link,
a:visited {
  color: rebeccapurple;
  font-weight: bold;
}
a:hover {
  color: hotpink;
}
```

相关属性：`color` 文字颜色、`cursor` 鼠标光标样式、`outline` 文字轮廓。

#### 伪元素：两个冒号 `::`

- `::first-line`：首行。
- `::before` / `::after`：内容添加。

```css
/* article 后的 p 的第一行 */
article p::first-line {
  font-size: 120%;
  font-weight: bold;
}

/* 内容添加 */
.box::before {
  content: "这显示在内容之前";
}
```

---

## 三、冲突规则

### 1. 层叠

- 当两条**同级别**规则应用到同一元素时，**后写的规则**发挥作用。

### 2. 优先级

| 选择器组成 | 分值 |
| --- | --- |
| 元素 / 伪元素 | 1 分 |
| 类 | 10 分 |
| ID | 100 分 |

![CSS 选择器优先级对照表](attachments/css-selector-specificity-comparison.png)

图片内容转写：

| 选择器 | ID | 类 | 元素 | 优先级 |
| --- | --- | --- | --- | --- |
| `h1` | 0 | 0 | 1 | 0-0-1 |
| `h1 + p::first-letter` | 0 | 0 | 3 | 0-0-3 |
| `li > a[href*="en-US"] > .inline-warning` | 0 | 2 | 2 | 0-2-2 |
| `#identifier` | 1 | 0 | 0 | 1-0-0 |
| `button:not(#mainBtn, .cta)` | 1 | 0 | 1 | 1-0-1 |

### 3. 继承

- 一般**文本属性继承**，**格式属性不继承**。

| 关键字 | 作用 |
| --- | --- |
| `inherit` | 开启继承 |
| `initial` | 属性值设置为初始值 |
| `revert` | 设置为浏览器默认样式 |
| `revert-layer` | 设置为上一个层叠层建立的值 |
| `unset` | 重置为自然值 |
