---
createdAt: 2026-11-27T03:20:57.607Z
updatedAt: 2026-11-27T06:08:46.632Z
lastViewedAt: 2026-11-27T03:27:10.012Z
field: web开发前端/HTML
---
# HTML-01-文档基础与通用规则

## 一、HTML 概述

- **功能**：搭建网页框架。
- **注意事项**：
  1. HTML 标签不区分大小写，一般推荐大写。
  2. HTML 中多个连续空格只识别为一个空格。

---

## 二、属性

属性是附加在标签内的**键值对**。

### 1. 基础规则

- 属性与元素名称、属性与属性之间用**空格**分割。
- 每个属性值通过引号引起来。
- 写法：`属性名="属性值"`。

### 2. 注意事项

1. 有些网页中属性值没有被引号包裹，有时被允许，但容易出错，**不建议**。
2. 单引号或双引号都可以，但不要二者同时混用；引号可以嵌套在不同引号中。

### 3. 示例

```html
<p class="editor-note">My cat is very strong</p>
```

### 4. 布尔属性

- 是一种没有值的属性，或者说值与名称相同；当然也可以显式写上值。

```html
<input type="text" disabled />
<input type="text" disabled="disabled" />
```

- `disabled` 禁止用户输入。

---

## 三、页面框架

```html
<!doctype html>
<html lang="zh-CN">
  <head>
    <meta charset="utf-8" />
    <title>我的测试站点</title>
  </head>
  <body>
    <p>这是我的 2 页面</p>
  </body>
</html>
```

| 元素 | 作用 |
| --- | --- |
| `<!doctype html>` | 文档类型声明 |
| `<html>` | 包裹页面所有内容，`lang` 指定主语言 |
| `<head>` | 包含不在页面显示的内容，如搜索关键词、页面描述、CSS 样式 |
| `<meta>` | 元数据，可包含 `base`、`link`、`script`、`style`、`title` 等 |
| `<title>` | 页面标题 |
| `<body>` | 页面显示内容 |

---

## 四、特殊符号的书写

以 `&` 开始，以分号 `;` 结束。

| 原义字符 | 等价字符引用 |
| --- | --- |
| `<` | `&lt;` |
| `>` | `&gt;` |
| `"` | `&quot;` |
| `'` | `&apos;` |
| `&` | `&amp;` |

---

## 五、注释

- 使用 `<!--` 和 `-->` 包裹。

```html
<!-- <p>我在注释里面！</p> -->
```

---

## 六、head 元素中的内容

### 1. 元数据

- `<meta>` 元素：描述数据的数据。

### 2. 字符编码

- `charset`：`utf-8`；`ISO-8859-1` 为拉丁字母表字符集。

### 3. name 与 content

```html
<meta name="author" content="LJY" />
<meta
  name="description"
  content="This is the first html file made by me" />
```

---

## 七、为文档设置主语言

- 使用 `lang` 属性。

```html
<html lang="zh-CN">
```

- 也可以将不同分段设置为不同语言：

```html
<p>Japanese example: <span lang="ja">ご飯が熱い。</span>.</p>
```

---

## 八、结构元素：span 与 div

### 1. 内联容器 `<span>`

- 默认不换行。
- 无法设置宽高，适合文本级修饰，如高亮、变色。
- 用于段内操作，应用 CSS、JavaScript。

```html
<p><span style="font-size: 32px; margin: 21px 0; display: block">这是一段文字</span></p>
```

- `margin` 控制元素间距，负值缩小空白；`margin: 21px 0` 表示上下 21px、左右 0。
- 如果对整个段落应用，可以直接在 `p` 元素中设置。

```html
<h1 style="font-size: 32px; margin: 21px 0">这个一个一级标题</h1>
```

### 2. 块级容器 `<div>`

- 默认换行。
- 划分文档独立区块，可设置宽度、高度、边距等，控制布局尺寸和位置。

```html
<div class="header">网站标题</div>
```

---

## 九、区块化（语义化布局）

| 元素 | 作用 |
| --- | --- |
| `<header>` | 页眉 |
| `<nav>` | 导航栏 |
| `<main>` | 主内容，位于 `<body>` 中 |
| `<article>` | 包围的内容为一篇文章 |
| `<section>` | 组织页面分块，可将一篇 `article` 分到不同 `section` |
| `<aside>` | 侧边栏，通常嵌套在 `<main>` 中，包含术语条目、作者简介、相关链接等间接信息 |
| `<footer>` | 页脚 |

```html
<!doctype html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>二次元俱乐部</title>
    <link
      href="https://fonts.googleapis.com/css?family=Open+Sans+Condensed:300|Sonsie+One"
      rel="stylesheet" />
  </head>
  <body>
    <header>
      <h1>聆听电子天籁之音</h1>
    </header>
    <nav>
      <ul>
        <li><a href="#">主页</a></li>
      </ul>
      <form>
        <input type="search" name="q" placeholder="要搜索的内容" />
        <input type="submit" value="搜索" />
      </form>
    </nav>
    <main>
      <article></article>
      <aside>
        <h2>相关链接</h2>
        <ul>
          <li><a href="#">这是一个超链接</a></li>
        </ul>
      </aside>
    </main>
    <footer>
      <p>2050 某某保留所有权利</p>
    </footer>
  </body>
</html>
```

---

## 十、在 HTML 中引入 CSS 与 JavaScript

> 本节合并原笔记中「CSS 和 JavaScript 的应用」及 CSS 部分「应用」的内容，统一说明引入方式。

### 1. CSS

#### 外部样式表

- `<link>` 位于文档头部，包含 `rel`、`href` 两个属性。
  - `rel`：表明这是文档样式表。
  - `href`：样式表文件路径。

```html
<link rel="stylesheet" href="my-css-file.css" />
```

#### 内部样式表

```html
<head>
  <style>
    p {
      color: purple;
    }
  </style>
</head>
```

#### 内联样式

```html
<span style="color: purple; font-weight: bold">span element</span>
```

### 2. JavaScript

#### 内部脚本

- 在 `</body>` 之前添加 `script`。

```html
<script>
  // 在此编写 javascript
</script>
```

#### 外部脚本

- 后缀为 `.js`，在 `</head>` 之前添加 `script`。
- 属性：
  - `src`：JavaScript 文件路径。
  - `defer`：告诉浏览器解析完成 HTML 后再加载 JavaScript。

```html
<script type="module" src="script.js"></script>

<!-- 如果失败，使用下列代码 -->
<script defer src="script.js"></script>
```
