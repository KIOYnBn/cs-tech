---
createdAt: 2026-11-27T03:20:57.706Z
updatedAt: 2026-11-27T06:08:06.124Z
lastViewedAt: 2026-11-27T03:27:14.799Z
field: web开发前端/HTML
---
# HTML-03-表单与表格


## 一、表单

### 1. `<form>`：外部包装器

| 属性 | 说明 |
| --- | --- |
| `action` | 提交到要处理的页面路径 |
| `method` | 表单数据传输的方法 |

### 2. `<fieldset>`：表单分组

- 属性 `disabled`：禁用表单元素。
- `<legend>`：为表单每个分组命名。

```html
<fieldset disabled>
  <legend>高级选项（未激活）</legend>
  <!-- 表单 -->
</fieldset>
```

### 3. `<button>`：按钮

- 属性 `type`：
  - `submit`：提交。
  - `reset`：重置输入状态。
  - `button`：默认不执行任何操作，需要 JavaScript。

### 4. `<input>`：输入数据

#### 通用属性

| 属性 | 说明 |
| --- | --- |
| `type` | 输入类型 |
| `name` | 数据项名称，以键值对形式发送 |
| `id` | 标识 id，用于 `label` |
| `required` | 要求提交表单前必须输入 |
| `placeholder` | 输入提示 |

#### type：文本输入类

| 类型 | 说明 | 示例 |
| --- | --- | --- |
| `text` | 单行文本输入框，用户名、短文本 | `<input type="text" name="username" placeholder="请输入用户名" />` |
| `password` | 输入内容显示为圆点或星号 | `<input type="password" name="pwd" placeholder="密码" />` |
| `email` | 邮箱输入框，提交时验证格式 | `<input type="email" name="email" required />` |
| `url` | URL 输入框，验证是否符合网址格式 | `<input type="url" name="website" />` |
| `tel` | 电话号码输入框，使用 `pattern` 自定义格式 | `<input type="tel" name="phone" pattern="[0-9]{3}-[0-9]{8}" />` |
| `search` | 搜索框 | `<input type="search" name="q" placeholder="搜索内容" />` |
| `number` | 数字输入框，支持限制范围和步长 | `<input type="number" name="age" min="18" max="99" />` |

#### type：选择类

##### radio：单选按钮

```html
<fieldset>
  <legend>Choose hotel room type:</legend>
  <div>
    <input
      type="radio"
      id="hotelChoice1"
      name="hotel"
      value="economy"
      checked />
    <!-- checked：默认选择 -->
    <label for="hotelChoice1">Economy</label>

    <input
      type="radio"
      id="hotelChoice2"
      name="hotel"
      value="superior" />
    <label for="hotelChoice2">Superior</label>

    <input
      type="radio"
      id="hotelChoice3"
      name="hotel"
      value="penthouse"
      disabled />
    <!-- disable：禁用表单控件 -->
    <label for="hotelChoice3">Penthouse</label>
  </div>
</fieldset>
```

##### checkbox：复选框

```html
<fieldset>
  <legend>Choose classes to attend:</legend>
  <div>
    <input type="checkbox" id="yoga" name="yoga" />
    <label for="yoga">Yoga</label>

    <input type="checkbox" id="coffee" name="coffee" />
    <label for="coffee">Coffee roasting</label>
  </div>
</fieldset>
```

##### select / option：下拉菜单

```html
<label for="transport">How are you getting here:</label>
<select name="transport" id="transport">
  <option value="">--Please choose an option--</option>
  <option value="plane">Plane</option>
  <option value="bike">Bike</option>
</select>
```

##### file：文件上传

```html
<input type="file" name="doc" accept=".pdf,.docx" />
```

##### color：颜色选择器，返回十六进制

```html
<input type="color" name="theme" />
```

#### type：功能类

| 类型 | 说明 | 示例 |
| --- | --- | --- |
| `submit` | 提交 | `<input type="submit" value="submit" />` |
| `reset` | 重置按钮 | `<input type="reset" value="reset" />` |
| `button` | 类似按钮 | `<input type="button" value="button" onclick="alert()" />` |
| `image` | 提交图片 | `<input type="image" src="submit.png" alt="submit" />` |
| `range` | 滑动条，选择数值范围 | `<input type="range" name="volume" min="0" max="100" />` |

#### type：时间选择类

| 类型 | 说明 | 示例 |
| --- | --- | --- |
| `date` | 日期选择器 | `<input type="date" name="birthday" />` |
| `time` | 时间选择器 | `<input type="time" name="time" />` |
| `month` | 月份选择器 | `<input type="month" name="month" />` |
| `week` | 周选择器 | `<input type="week" name="week" />` |
| `datetime-local` | 本地时间 | `<input type="datetime-local" name="meeting" />` |

### 5. `<textarea>`：多行文本输入字段

```html
<label for="comments">Any other comments?</label>
<textarea id="comments" name="comments" rows="5" cols="33"></textarea>
```

### 6. `<label>`：标识标签

- 属性 `for` 关联控件 `id`；也可以把控件直接包在 `label` 内。

```html
<label for="name">Name (required):</label>
<input type="text" name="name" id="name" required />

<!-- or -->

<label>
  Name (required):
  <input type="text" name="name" required />
</label>
```

---

## 二、表格

### 1. 元素

- 表格可以嵌套表格。

| 元素 | 作用 |
| --- | --- |
| `<td>` | 单元格 |
| `<tr>` | 行分隔 |
| `<th>` | 标题行/表头单元格 |
| `<caption>` | 标题 |
| `<col>` / `<colgroup>` | 格式设置 |
| `<thead>` | 表头 |
| `<tfoot>` | 表尾 |
| `<tbody>` | 主要内容 |
| `scope` | 定义表头类型——行表头或列表头 |

- `colspan` / `rowspan` 用于跨列/跨行；`scope="colgroup"`、`scope="rowgroup"` 标明表头作用域。

```html
<thead>
  <tr>
    <th colspan="3" scope="colgroup">衣物</th>
    <th rowspan="2" scope="rowgroup">金额</th>
  </tr>
</thead>
```

### 2. 完整示例

```html
<head>
  <style>
    tbody {
      font-size: 90%;
      font-style: italic;
    }
    tfoot {
      font-weight: bold;
    }
  </style>
</head>

<body>
  <table>
    <colgroup>
      <col />
      <col style="background-color: yellow" />
    </colgroup>
    <caption>我的数据</caption>
    <thead>
      <tr>
        <th>数据1</th>
        <th>数据2</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>加尔各答</td>
        <td>橙汁</td>
      </tr>
    </tbody>
    <tfoot>
      <tr>
        <td>机器人</td>
        <td>爵士乐</td>
      </tr>
    </tfoot>
  </table>
</body>
```
