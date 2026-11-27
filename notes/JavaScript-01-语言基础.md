---
createdAt: 2026-11-27T03:21:07.735Z
updatedAt: 2026-11-27T03:26:54.825Z
lastViewedAt: 2026-11-27T03:28:05.085Z
field: web开发前端
---
# JavaScript-01-语言基础

## 一、概述与应用

- **功能**：实现交互作用。
- **应用方式**（内部/外部脚本）已统一整理在 [01-HTML-01](HTML-01-文档基础与通用规则.md#在-html-中引入-css-与-javascript)。

---

## 二、注释

```javascript
// 这是一个单行注释

/* 
  这是一条多行注释
*/
```

---

## 三、变量

### 1. 声明：`let`

```javascript
let yourName;
let myName = "Chris";
```

### 2. 命名规则

- 不能以数字开头；可用字母、下划线 `_` 或 `$` 开头。
- 小写驼峰命名法：多个单词组合时，首个单词首字母小写，其余单词首字母大写。

### 3. 常量：`const`

```javascript
const guesses = document.querySelector(".guesses");
```

---

## 四、变量类型

### 1. String

#### 多行

- 在字符串中加入 `\n`。

```javascript
let lines = "this is the first line. \n This is the second line.";
```

#### 表达式嵌入：`${}`

```javascript
const one = "me";
console.log(`${one}`);
// 注意：单引号/双引号不会引起表达式嵌入，只有反引号 ` 才可以
```

#### 方法属性

| 方法/属性 | 说明 |
| --- | --- |
| `length` | 长度 |
| `[index]` | 按索引取字符 |
| `slice(start, end)` | 切片，不包含结束位置 |
| `indexOf()` | 检索，没有时返回 `-1` |
| `toLowerCase()` / `toUpperCase()` | 大小写 |
| `replace()` | 替换 |

```javascript
let browserType = "mozilla";
browserType.length;

browserType[0];
browserType[browserType.length - 1];
browserType.slice(0, 3); // 0 到 3，不包括最后一个位置
browserType.slice(2); // 2 后的所有字符

browserType.indexOf("zilla"); // 没有时返回 -1

let radData = "MY name is Mud";
radData.toLowerCase();
radData.toUpperCase();

browserType.replace("moz", "van");
```

### 2. Number

- `toFixed()`：小数点位数。

```javascript
// Final result should be 4633.33
let result = 7 + 13 / 9 + 7;
let result2 = (100 / 2) * 6;

result *= result2;

const finalResult = result.toFixed(2);
```

### 3. Boolean

- 布尔类型，值为 `true` / `false`。

### 4. Array

| 方法 | 说明 |
| --- | --- |
| `split()` | 字符串转数组 |
| `join()` / `toString()` | 数组转字符串 |
| `push()` / `pop()` | 末尾添加/删除 |
| `unshift()` / `shift()` | 起始位置添加/删除 |
| `forEach()` | 遍历 |
| `filter()` | 过滤 |
| `splice()` | 按索引删除/替换 |

```javascript
let myArray = myData.split(",");
let myNewString = myArray.join(",");
// or
myNewString = myArray.toString();

// 默认删除最后一项
myArray.pop();
myArray.push("Bradford", "brighton");
```

```javascript
myArray.forEach((element, index) => {
  const newElement = `${element} (${index})`;
  myArray[index] = newElement;
});
```

```javascript
function startsWithE(bird) {
  return bird.startsWith("E");
}
const eBirds = birds.filter(startsWithE);
```

```javascript
const birds = ["Parrots", "Falcons", "Eagles", "Emus", "Caracaras", "Egrets"];
const eaglesIndex = birds.indexOf("Eagles");
birds.splice(eaglesIndex, 1);
```

---

## 五、运算符

| 运算符 | 说明 |
| --- | --- |
| `===` | 等于 |
| `!==` | 不等于 |
| `>` | 大于 |
| `<` | 小于 |
| `&&` | 与 |
| `||` | 或 |

---

## 六、语句

### 1. 条件语句

```javascript
if (guessCount === 1) {
  guesses.textContent = "Previous guesses:";
}

if (condition1) {
} else if (condition2) {
} else {
}
```

#### Switch

```javascript
switch (expression) {
  case choice1:
    break;
  case choice2:
    break;
  default:
    break;
}
```

```javascript
let response;
switch (true) {
  case score < 0 || score > 100:
    response = "This is not possible, an error has occurred.";
    break;
  case score < 20:
    response = "That was a terrible score — total fail!";
    break;
  case score < 40:
    response = "You know some things, but it's a pretty bad score. Needs improvement.";
    break;
  case score < 70:
    response = "You did a passable job, not bad!";
    break;
  case score < 90:
    response = "That's a great score, you really know your stuff.";
    break;
  case score <= 100: // 隐含 score >= 90（前序条件已过滤）
    response = "What an amazing score! Did you cheat? Are you for real?";
    break;
  default:
    response = "Invalid score input"; // 处理 NaN 等异常值
}
```

#### 三元运算符

- `condition ? 运行此代码 : 否则运行此代码`

```javascript
const greeting = isBirthday ? "小王生日快乐" : "小王早上好";
```

### 2. 循环语句

#### 标准 for 循环（类似 C 语言）

```javascript
for (initializer; condition; final-expression) {
}

for (var i = 0; i < cats.length; i++) {
  info += cats[i] + ",";
}
```

#### while / do while

```javascript
while (condition) {
}

do {
} while (condition);

do {
  info += cats[i];
} while (i < cats.length);
```

#### for...of

```javascript
const fruits = ["apples", "bananas"];
for (const fruit of fruits) {
  console.log(fruit);
}
```

---

## 七、函数

### 1. 常规函数

```javascript
function checkGuess() {
  alert("I am a placeholder");
}
```

### 2. 匿名函数

```javascript
(function () {
  alert("你好");
})();

textBox.addEventListener("keydown", function (event) {
  console.log(`you pressed '${event.key}'`);
});
```

### 3. 箭头函数

- 用 `(event) =>` 代替 `function(event)`。

```javascript
textBox.addEventListener("keydown", (event) => {
  console.log(`you pressed '${event.key}'`);
});
```

---

## 八、正则表达式

```javascript
let text = ":inserta: and :insertb:";
text = text
  .replace(/:inserta:/g, "Apple")
  .replace(/:insertb:/g, "Banana");
// 结果："Apple and Banana"
```

---

## 九、常见库

| 方法 | 说明 |
| --- | --- |
| `Math.floor` | 向下取整 |
| `Math.random` | 产生 `[0, 1)` 的随机数 |
