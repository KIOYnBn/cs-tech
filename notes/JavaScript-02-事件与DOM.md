---
createdAt: 2026-11-27T03:21:07.831Z
updatedAt: 2026-11-27T03:26:38.835Z
lastViewedAt: 2026-11-27T03:27:47.932Z
field: web开发前端
---
# JavaScript-02-事件与DOM

## 一、事件

### 1. 事件监听器：`addEventListener`

- 同一个事件可以添加多个监听器。

```javascript
guessSubmit.addEventListener("click", checkGuess);
// checkGuess 是一个函数，作为事件处理器

guessSubmit.removeEventListener("click", checkGuess);
```

### 2. 事件类型

| 事件 | 说明 |
| --- | --- |
| `click` | 点击 |
| `focus` | 聚焦 |
| `blur` | 失焦 |
| `dblclick` | 双击 |
| `mouseover` | 鼠标悬停 |
| `mouseout` | 鼠标移出按钮 |
| `play` | 只对 `video` 等元素有效 |
| `keydown` | 键盘按下 |

### 3. 事件处理器属性

- 比如 `onclick`。
- 事件处理器属性对于同一事件**不能叠加**，会覆盖。

```javascript
btn.onclick = () => {
  // ...
};
```

### 4. 内联事件处理器：不要使用

![JavaScript 内联事件处理器示例](attachments/js-inline-event-handler-example.png)

图片内容转写：

```html
<button onclick="bgChange()">按下我</button>
```

```javascript
function bgChange() {
  const rndCol = `rgb(${random(255)}, ${random(255)}, ${random(255)})`;
  document.body.style.backgroundColor = rndCol;
}
```

### 5. 控制器：`AbortController()`

- 用于与事件相关联，监听/移除事件。

```javascript
const controller = new AbortController();

btn.addEventListener(
  "click",
  () => {
    const rndCol = `rgb(${random(255)}, ${random(255)}, ${random(255)})`;
    document.body.style.backgroundColor = rndCol;
  },
  { signal: controller.signal }, // 向该处理器传递 AbortSignal
);

controller.abort(); // 移除任何/所有与该控制器相关的事件处理器
```

### 6. 事件对象

- `event` / `evt` / `e`：位于事件处理函数内部，自动传递给事件处理函数。

```javascript
const btn = document.querySelector("button");

function random(number) {
  return Math.floor(Math.random() * (number + 1));
}

function bgChange(e) {
  const rndCol = `rgb(${random(255)}, ${random(255)}, ${random(255)})`;
  e.target.style.backgroundColor = rndCol;
  console.log(e);
}

btn.addEventListener("click", bgChange);
```

#### 事件对象属性

| 属性/方法 | 说明 |
| --- | --- |
| `target.getAttribute()` | 获取事件对象的特定属性 |
| `preventDefault()` | 停止事件，比如提交表单时存在空内容 |
| `KeyboardEvent.key` | 特定事件对象有特定属性，表示哪个键被按下 |

```javascript
const buttonBar = document.querySelector(".button-bar");

function setColor(e) {
  buttonBar.style.backgroundColor = e.target.getAttribute("data-color");
}

buttonBar.addEventListener("click", setColor);
```

```javascript
const form = document.querySelector("form");
const fname = document.getElementById("fname");
const lname = document.getElementById("lname");
const para = document.querySelector("p");

form.addEventListener("submit", (e) => {
  if (fname.value === "" || lname.value === "") {
    e.preventDefault();
    para.textContent = "You need to fill in both names!";
  }
});
```

### 7. 事件冒泡

- 指子元素的事件发生，同时会触发父元素的事件。
- **优点**：可以在父元素设置监听器，而不是在子元素中设置。
- **停止冒泡**：`stopPropagation()`。

```javascript
const btn = document.querySelector("button");
const box = document.querySelector("div");
const video = document.querySelector("video");

btn.addEventListener("click", () => box.classList.remove("hidden"));

video.addEventListener("click", (event) => {
  event.stopPropagation();
  video.play();
});

box.addEventListener("click", () => box.classList.add("hidden"));
```

### 8. 事件捕获

- 与事件冒泡类似，但顺序相反：**先父元素事件，后子元素事件**。
- `capture`：默认禁用。

```javascript
document.body.addEventListener("click", handleClick, { capture: true });
```

---

## 二、DOM 脚本

### 1. 获取元素引用

- `querySelector()` / `querySelectorAll()`。

```javascript
// 引用 document 下的第一个 section
const sect = document.querySelector("section");
// 引用 document 下的所有 section
const sectAll = document.querySelectorAll("section");
```

### 2. 创建元素与位置锚定

- `createElement()`、`appendChild()`。
- 文本节点：`createTextNode()`。

```javascript
const para = document.createElement("p");
sect.appendChild(para);

const text = document.createTextNode("....");
para.appendChild(text);
```

### 3. 移动元素

- `appendChild()` 也可用于移动已有元素。

### 4. 删除元素

- `parentNode.removeChild()`。

```javascript
linkPara.parentNode.removeChild(linkPara);
```

### 5. 操作 CSS 样式

#### 操作内联样式：`HTMLElement.style`

```javascript
para.style.backgroundColor = "black";
```

#### 操作外联样式：`Element.setAttribute()`

```javascript
// 需要提前设置了 highlight 类
para.setAttribute("class", "highlight");
```
