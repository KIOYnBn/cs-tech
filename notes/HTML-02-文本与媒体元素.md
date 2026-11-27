---
createdAt: 2026-11-27T03:20:57.654Z
updatedAt: 2026-11-27T06:08:53.041Z
lastViewedAt: 2026-11-27T03:26:58.887Z
field: web开发前端/HTML
---
# HTML-02-文本与媒体元素
## 一、文本元素

### 1. 标题：`<h1>` ~ `<h6>`

- `h1` 一级标题每个页面中只使用一次。

```html
<h1>这是一个一级标题</h1>
```

### 2. 段落：`<p>`

```html
<p>My cat is very grumpy</p>
```

### 3. 换行：`<br>`

- 在段落中换行。

```html
<p>
  举头望明月<br />
  低头思故乡
</p>
```

### 4. 水平分割线：`<hr>`

```html
<p>nb</p>
<hr />
<p>更 nb</p>
```

### 5. 斜体：`<em>` 或 `<i>`

```html
<em>context</em>
<i>context</i>
```

### 6. 粗体：`<strong>` 或 `<b>`

```html
<p>My cat is <strong>very</strong> grumpy.</p>
<p>My cat is <b>very</b> grumpy.</p>
```

> 注意：`strong` 元素需在 `p` 元素内正确关闭（原笔记：strong 元素在 p 元素前关闭）。

### 7. 高亮：`<mark>`

```html
<p><mark>这是一段高亮文字</mark></p>
```

### 8. 下划线：`<u>`

```html
<u>这是一个链接</u>
```

### 9. 列表

#### 有序列表：`<ol>` + `<li>`

```html
<ol>
  <li>这是 1</li>
  <li>second</li>
</ol>
```

#### 无序列表：`<ul>` + `<li>`

```html
<ul>
  <li>one</li>
  <li>two</li>
</ul>
```

### 10. 描述/缩进列表：`<dl>` + `<dt>` + `<dd>`

```html
<dl>
  <dt>旁白</dt>
  <dd>戏剧中，为了……</dd>
  <dd>写作中，为了……</dd>
</dl>
```

### 11. 引用

#### 块引用：`<blockquote>`

```html
<blockquote cite="http://...">
  <p>这一段都是引用内容</p>
</blockquote>
```

#### 行内引用：`<q>`

```html
<p>鲁迅说过：<q cite="鲁迅">世上本没有路……</q></p>
```

#### 引用属性元素：`<cite>`

- 用于标记引用来源。

### 12. 缩略语：`<abbr>`

- `title` 属性：鼠标停留时显示，非必要。

```html
<p>我是使用 <abbr>HTML</abbr> 来组织网页文档</p>
<p><abbr title="protein-protein interaction">PPI</abbr> 是我的研究领域</p>
```

### 13. 标记联系方式：`<address>`

```html
<address>
  <p>
    LJY<br />
    鲁迅<br />
  </p>
  <ul>
    <li>Email: ....@mail.imu.edu.cn</li>
    <li>phone number: 130276..</li>
  </ul>
</address>
```

### 14. 上下标：`<sup>`、`<sub>`

```html
<p>25<sup>th</sup></p>
<p>C<sub>30</sub></p>
```

### 15. 标记时间：`<time>`

- 属性：`datetime`。

```html
<time datetime="2025-7-12">2025年7月12日</time>
```

---

## 二、媒体元素

### 1. 图片插入：`<img>`

| 属性 | 说明 |
| --- | --- |
| `src` | 资源链接 |
| `alt` | 备选文本，图片加载错误时显示 |
| `width` | 宽度 |
| `height` | 高度 |
| `title` | 图片标题，一般不用 |

![HTML img 标签的 src 与 alt 属性示例](attachments/html-img-src-alt-example.png)

图片中对应的代码示例：

```html
<img
  src="https://roy-tian.github.io/learning-area/extras/getting-started-web/beginner-html-site/images/firefox-icon.png"
  alt="Firefox 图标" />
```

### 2. 插入图片描述：`<figure>` + `<figcaption>`

```html
<figure>
  <img
    src="images.jpg"
    alt="一个图片"
    width="400"
    height="300" />
  <figcaption>workflow</figcaption>
</figure>
```

### 3. 视频插入：`<video>`

| 属性 | 说明 |
| --- | --- |
| `controls` | 用户可以控制视频/音频播放 |
| `src` | 文件路径 |
| `width` / `height` | 宽高 |
| `autoplay` | 内容自动播放，布尔属性 |
| `loop` | 视频循环，布尔属性 |
| `muted` | 默认关闭声音，布尔属性 |
| `preload` | 缓冲较大文件；`none` 不缓冲，`auto` 页面加载后缓冲，`metadata` 仅缓冲元数据 |
| `poster` | 指向图像的 URL，视频播放前显示 |

```html
<video src="rabbite320.webm" controls>
  <!-- video 中插入的文字可以在视频加载错误时显示 -->
  <p>
    你的浏览器不支持 html 视频，点击<a href="rabbit320.mp4">观看</a>。
  </p>
</video>

<video
  controls
  width="400"
  height="400"
  autoplay
  loop
  muted
  preload="auto"
  poster="poster.png">
  <!-- source 元素用于媒体多资源嵌套，避免单一格式兼容问题 -->
  <source src="rabbit320.mp4" type="video/mp4" />
  <source src="rabbit320.webm" type="video/webm" />
  <p>你的浏览器不支持此视频</p>
</video>
```

### 4. 音频插入：`<audio>`

- 与 `video` 基本相同，但没有 `width`、`height`、`poster` 属性。

```html
<audio controls>
  <source src="viper.mp3" type="audio/mp3" />
  <source src="viper.ogg" type="audio/ogg" />
  <p>
    你的浏览器不支持该音频，点击<a href="viper.mp3">此链接</a>收听
  </p>
</audio>
```

### 5. 音频/视频文本显示：`<track>`

- 文件为 WebVTT 格式，`.vtt` 后缀。

```text
WEBVTT

00:00:02.000 --> 00:00:05.000
第一句字幕文本

00:00:06.000 --> 00:00:10.000
第二句字幕文本
```

- 可以插入 `audio` 或 `video` 中。

| 属性 | 说明 |
| --- | --- |
| `kind` | 轨道类型：`subtitles` 字幕、`captions` 标题（含非语言信息）、`chapters` 章节、`descriptions` 口头描述、`metadata` 元数据（不显示，供 JS 调用） |
| `src` | 文件链接 |
| `srclang` | 轨道语言类型，如 `en` |
| `label` | 用户可见的语言名称，如「简体中文」 |

```html
<video controls>
  <source src="example.mp4" type="video/mp4" />
  <track kind="subtitles" src="subtitles.vtt" srclang="es" label="Spanish" />
</video>
```

### 6. 外部资源插入：`<link>`

- 一般图片文件放在 HTML 文件同一目录下。

```html
<link rel="icon" href="favicon.ico" type="image/x-icon" />
<link rel="apple-touch-icon" sizes="167x167" href="/apple-touch-icon-167x167.png" />
```

### 7. 超链接：`<a>`

#### 链接到文档某一部分

```html
<nav>
  <a href="#chapter1">跳转至章节1</a>
</nav>

<h2 id="chapter1">章节1</h2>
```

#### 发送邮件

```html
<a href="mailto:twb72743075@126.com">向我发送邮箱</a>
```

#### 属性

| 属性 | 说明 |
| --- | --- |
| `href` | 声明 web 超链接，如 `href="https://www.mozilla.org/"` |
| `title` | 为超链接声明额外信息，鼠标悬停时提示 |
| `target` | 指定链接如何呈现；`_blank` 在新标签页显示，未填则当前页显示 |
| `download` | 链接到下载的资源 |

```html
<a href="https://..." download="firefox.exe">下载 firefox</a>
```
