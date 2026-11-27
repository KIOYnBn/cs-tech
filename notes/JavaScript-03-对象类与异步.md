---
createdAt: 2026-11-27T03:21:07.928Z
updatedAt: 2026-11-27T06:08:41.703Z
lastViewedAt: 2026-11-27T03:27:27.213Z
field: web开发前端/JavaScript
---
# JavaScript-03-对象类与异步

## 一、对象

### 1. 创建

```javascript
const person = {
  name: ["Bob", "Smith"],
  age: 32,
  bio: function () {
    console.log(`${this.name[0]} ${this.name[1]} 现在 ${this.age} 岁了。`);
  },
  introduceSelf: function () {
    console.log(`你好！我是 ${this.name[0]}。`);
  },
};
```

### 2. 属性访问

| 方式 | 示例 | 说明 |
| --- | --- | --- |
| 点表示法 | `person.age` | 常用 |
| 括号表示法 | `person["age"]` | 创建新属性时，成员名称可以使用变量 |

```javascript
person[myDataName] = myDataValue;
```

### 3. 构造函数与 `this`

- `this` 表示对象，用于批量创建对象。

```javascript
function Person(name) {
  this.name = name;
  this.introduceSelf = function () {
    console.log(`你好！我是 ${this.name}。`);
  };
}
```

---

## 二、类与原型

### 1. 原型

#### 为对象分配原型：`Object.create()`

```javascript
const personPrototype = {
  greet() {
    console.log("hello!");
  },
};

const carl = Object.create(personPrototype);
carl.greet(); // hello!
```

#### 为对象分配方法：`Object.assign()`

```javascript
const personPrototype = {
  greet() {
    console.log(`hello, my name is ${this.name}!`);
  },
};

function Person(name) {
  this.name = name;
}

Object.assign(Person.prototype, personPrototype);
// or
// Person.prototype.greet = personPrototype.greet;
```

#### 检查某属性是否是对象自己的属性：`Object.hasOwn()`

```javascript
const irma = new Person("Irma");

console.log(Object.hasOwn(irma, "name")); // true
console.log(Object.hasOwn(irma, "greet")); // false
```

### 2. 创建类

```javascript
class Person {
  name;

  constructor(name) {
    this.name = name;
  }

  introduceSelf() {
    console.log(`Hi! I'm ${this.name}`);
  }
}

const giles = new Person("Giles");
giles.introduceSelf(); // Hi! I'm Giles
```

### 3. 类的继承：`extends`

```javascript
class Professor extends Person {
  teaches;

  constructor(name, teaches) {
    super(name);
    this.teaches = teaches;
  }

  introduceSelf() {
    console.log(
      `My name is ${this.name}, and I will be your ${this.teaches} professor.`,
    );
  }

  grade(paper) {
    const grade = Math.floor(Math.random() * (5 - 1) + 1);
    console.log(grade);
  }
}
```

### 4. 类的封装

#### 私有属性（`#`）

```javascript
class Student extends Person {
  #year;

  constructor(name, year) {
    super(name);
    this.#year = year;
  }

  introduceSelf() {
    console.log(`Hi! I'm ${this.name}, and I'm in year ${this.#year}.`);
  }

  canStudyArchery() {
    return this.#year > 1;
  }
}
```

#### 私有方法

```javascript
class Example {
  somePublicMethod() {
    this.#somePrivateMethod();
  }

  #somePrivateMethod() {
    console.log("You called me?");
  }
}

const myExample = new Example();

myExample.somePublicMethod(); // 'You called me?'
myExample.#somePrivateMethod(); // SyntaxError
```

---

## 三、异步 JavaScript

- 异步编程：类似于 Python 中的多线程。

### 1. Promise

- 由异步函数返回的对象。

| 术语 | 说明 |
| --- | --- |
| `pending` | 待定 |
| `fulfilled` | 成功 |
| `rejected` | 失败 |

#### 合并使用 Promise

| 方法 | 说明 |
| --- | --- |
| `Promise.all()` | 所有 promise 同时实现时开启下一步 |
| `Promise.any()` | 任意一个 promise 实现时开启下一步 |

```javascript
Promise.all([fetchPromise1, fetchPromise2]).then(...);
```

#### Promise 构造器

```javascript
// resolve、reject 由 Promise 内部提供
var promise = new Promise(function (resolve, reject) {
  // executor
});
```

### 2. 构建异步函数：`async` / `await`

```javascript
async function fetchProducts() {
  try {
    const response = await fetch(
      "https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json",
    );
    if (!response.ok) {
      throw new Error(`HTTP 请求错误：${response.status}`);
    }
    const data = await response.json();
    return data;
  } catch (error) {
    console.error(`无法获取产品列表：${error}`);
  }
}

const promise = fetchProducts();
promise.then((data) => console.log(data[0].name));
```

### 3. 从服务器获取数据

| 方法 | 说明 |
| --- | --- |
| `fetch(url)` | 返回一个 Promise 对象 |
| `then()` | 构建 Promise 链 |
| `catch()` | 捕获错误 |

---

## 四、Worker

- 书写脚本较为麻烦，直接观看 [MDN 教程](https://developer.mozilla.org/zh-CN/docs/Learn_web_development/Extensions/Async_JS/Introducing_workers)。

---

## 五、JSON

- JSON 中只使用**双引号**。

### 1. 从网页获取 JSON 数据

```javascript
async function populate() {
  const requestURL =
    "https://mdn.github.io/learning-area/javascript/oojs/json/superheroes.json";
  const request = new Request(requestURL);

  const response = await fetch(request);
  const superHeroes = await response.json();

  populateHeader(superHeroes);
  populateHeroes(superHeroes);
}
```

### 2. JSON 对象与字符串类型转换

#### 字符串转 JSON：`JSON.parse()`

```javascript
async function populate() {
  const requestURL =
    "https://mdn.github.io/learning-area/javascript/oojs/json/superheroes.json";
  const request = new Request(requestURL);

  const response = await fetch(request);
  const superHeroesText = await response.text();

  const superHeroes = JSON.parse(superHeroesText);
  populateHeader(superHeroes);
  populateHeroes(superHeroes);
}
```

#### JSON 转字符串：`JSON.stringify()`

```javascript
let myObj = { name: "Chris", age: 38 };
myObj;
let myString = JSON.stringify(myObj);
myString;
```
