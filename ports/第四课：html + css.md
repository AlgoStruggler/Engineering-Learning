# 第四课：HTML + CSS

> `HTML` 决定页面“有什么”，`CSS` 决定页面“长什么样”；两者分离，才能可维护、可协作、可迭代

## 一、HTML

### 1. 为什么需要 HTML

`HTML` = 超文本标记语言，描述网页的“结构”。

浏览器需要知道页面上有什么：标题、段落、图片、链接、表单。
`HTML` 用“标签”描述这些元素的含义。

类比：

| 角色         | 对应                 |
| :----------- | :------------------- |
| `HTML`       | 建筑的骨架、房间划分 |
| `CSS`        | 装修、颜色、家具摆放 |
| `JavaScript` | 水电、电梯、交互     |

搜索引擎、屏幕阅读器、浏览器都依赖 `HTML` 理解页面。
写的不是“给用户看的文本”，而是“给机器读的结构”。

### 2. 文档结构

```html
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>页面标题</title>
  </head>
  <body>
    <!-- 页面内容 -->
  </body>
</html>
```

| 部分                     | 作用                    |
| :----------------------- | :---------------------- |
| `<!DOCTYPE html>`        | 告诉浏览器这是 `HTML5`  |
| `<html lang>`            | 根元素，`lang` 声明语言 |
| `<head>`                 | 元信息，不显示在页面上  |
| `<meta charset>`         | 字符编码                |
| `<meta name="viewport">` | 移动端适配              |
| `<title>`                | 标签页标题              |
| `<body>`                 | 页面可见内容            |

真实场景：

- 没有 `<!DOCTYPE html>`，浏览器可能进入怪异模式（`quirks mode`），盒模型计算方式都会变。
- 没有 `<meta charset="UTF-8">`，中文可能乱码。
- 没有 `<meta name="viewport">`，移动端会按 $980px$ 宽渲染再缩放，字很小。

------

### 3. 常用标签

#### 结构类

| 标签        | 语义 |
| :---------- | :--- |
| `<header>`  | 页头 |
| `<nav>`     | 导航 |
| `<main>`    | 主体 |
| `<section>` | 区块 |
| `<article>` | 文章 |
| `<aside>`   | 侧边 |
| `<footer>`  | 页脚 |

#### 文本类

| 标签            | 语义         |
| :-------------- | :----------- |
| `<h1>` ~ `<h6>` | 标题，按层级 |
| `<p>`           | 段落         |
| `<a>`           | 链接         |
| `<strong>`      | 强调（重要） |
| `<em>`          | 强调（语气） |
| `<code>`        | 代码         |
| `<pre>`         | 预格式化     |

#### 表单类

| 标签         | 作用                  |
| :----------- | :-------------------- |
| `<form>`     | 表单容器              |
| `<input>`    | 输入框，类型靠 `type` |
| `<textarea>` | 多行文本              |
| `<select>`   | 下拉框                |
| `<button>`   | 按钮                  |
| `<label>`    | 标签，关联输入框      |

#### 媒体类

| 标签      | 作用 |
| :-------- | :--- |
| `<img>`   | 图片 |
| `<video>` | 视频 |
| `<audio>` | 音频 |

### 4. 语义化

语义化 = 用正确的标签描述内容的含义。

**对比：**

```html
<!-- 不语义化 -->
<div class="header">...</div>
<div class="nav">...</div>
<div class="main">...</div>

<!-- 语义化 -->
<header>...</header>
<nav>...</nav>
<main>...</main>
```

**好处：**

- 可读性高
- 无障碍友好
- `SEO` 友好
- 浏览器和工具能更好理解

> 一句话总结：`div` 是“没有含义的盒子”，语义化标签是“有含义的盒子”。

### 5. 表单

```html
<form action="/api/login" method="POST">
  <label for="username">用户名</label>
  <input type="text" id="username" name="username" required />

  <label for="password">密码</label>
  <input type="password" id="password" name="password" required />

  <button type="submit">登录</button>
</form>
```

关键点：

- `<label for>` 和 `<input id>` 对应，点击 `label` 能聚焦输入框
- `name` 是提交时的字段名
- `type` 决定输入类型和校验
- `required` 表示必填

常见 `type`：

| type       | 作用           |
| :--------- | :------------- |
| `text`     | 文本           |
| `password` | 密码           |
| `email`    | 邮箱，自动校验 |
| `number`   | 数字           |
| `date`     | 日期           |
| `checkbox` | 复选框         |
| `radio`    | 单选           |
| `file`     | 文件           |
| `submit`   | 提交按钮       |

**真实场景：**

```html
<label for="username">用户名</label>
<input type="text" id="username" name="username" required />
```

浏览器里看到：

- 点击“用户名”三个字，光标会跳进输入框
- 不填直接点提交，浏览器会弹出“请填写此字段”

`DevTools` 里看到：

- `Elements` 面板选中 `<label>`，右侧 `Styles` 下方有 `for="username"`
- 选中 `<input>`，有 `id="username"`

### 6. DOM

`DOM` = 浏览器把 `HTML` 解析成的树形结构。

```html
<body>
  <h1>标题</h1>
  <p>段落</p>
</body>
```

**解析成：**

```text
body
├── h1
│   └── "标题"
└── p
    └── "段落"
```

`JS` 可以通过 `DOM API` 操作这棵树：

```js
document.querySelector('h1').textContent = '新标题';
```

真实场景：

- 第三课讲的“从输入 `URL` 到页面显示”，其中一步就是“解析 `HTML` 成 `DOM` ”
- 你在 `DevTools Elements` 面板看到的层级，就是 `DOM` 树
- `JS` 能改页面，就是因为能操作 `DOM`

> 一句话总结：`HTML` 是源码，`DOM` 是浏览器解析后的树；`JS` 操作的是 `DOM` ，不是 `HTML` 文件。

## 二、CSS

`CSS` = 层叠样式表，描述网页的“样式”。

### 1. 为什么需要 CSS

- `HTML` 只管结构，不管好看
- `CSS` 决定颜色、字体、间距、布局、动画
- 内容和样式分离，便于维护

类比：

| 角色       | 对应                       |
| :--------- | :------------------------- |
| `HTML`     | 毛坯房                     |
| `CSS`      | 装修                       |
| 内联样式   | 每个房间单独刷漆，难统一   |
| 外链 `CSS` | 统一装修方案，改一处全生效 |

### 2. 引入方式

```html
<!-- 外链，推荐 -->
<link rel="stylesheet" href="style.css" />

<!-- 内嵌 -->
<style>
  h1 { color: red; }
</style>

<!-- 行内，不推荐 -->
<h1 style="color: red;">标题</h1>
```

真实场景：

- 外链：改 `style.css`，所有页面生效
- 内嵌：只影响当前页面
- 行内：只影响当前元素，且优先级最高，难覆盖

### 3. 选择器

| 选择器 | 例子            | 作用               |
| :----- | :-------------- | :----------------- |
| 元素   | `p`             | 所有 `p`           |
| 类     | `.btn`          | `class="btn"`      |
| `ID`   | `#header`       | `id="header"`      |
| 属性   | `[type="text"]` | 指定属性           |
| 后代   | `nav a`         | `nav` 里的 `a`     |
| 子代   | `ul > li`       | `ul` 的直接子 `li` |
| 伪类   | `a:hover`       | 鼠标悬停           |
| 伪元素 | `p::before`     | `p` 前面插入内容   |

**优先级（从高到低）：**

1. 行内样式
2. `ID` 选择器
3. 类、属性、伪类
4. 元素、伪元素

**真实场景：**

```css
.btn { color: blue; }
#submit-btn { color: red; }
```

```html
<button id="submit-btn" class="btn">提交</button>
```

浏览器里看到：按钮是红色。
因为 `ID` 选择器优先级高于类选择器。

> 一句话总结：优先级不是“谁后写谁赢”，而是“谁更具体谁赢”。

### 4. 盒模型

每个元素都是一个盒子，从内到外：

```text
+-----------------------------+
|         margin              |
|  +-----------------------+  |
|  |       border          |  |
|  |  +-----------------+  |  |
|  |  |    padding      |  |  |
|  |  |  +-----------+  |  |  |
|  |  |  |  content  |  |  |  |
|  |  |  +-----------+  |  |  |
|  |  +-----------------+  |  |
|  +-----------------------+  |
+-----------------------------+
```

| 层        | 作用   |
| :-------- | :----- |
| `content` | 内容   |
| `padding` | 内边距 |
| `border`  | 边框   |
| `margin`  | 外边距 |

**`box-sizing` ：**

```css
.box {
  box-sizing: border-box; /* width 包含 padding 和 border */
}
```

**推荐全局：**

```css
* {
  box-sizing: border-box;
}
```

真实场景：

```css
.box {
  width: 200px;
  padding: 20px;
  border: 5px solid black;
}
```

默认 `box-sizing: content-box` 时：

- `content` 宽 = $200px$
- 总宽 = $200 + 20×2 + 5×2 = 250px$

改成 `border-box` 后：

- 总宽 = $200px$
- `content` 宽 = $200 - 20×2 - 5×2 = 150px$

`DevTools` 里看到：

- `Elements` → 选中元素
- 右侧 `Styles` 下方有盒模型图
- 能直接看到 `content` 、`padding` 、`border` 、`margin` 的值

> 一句话总结：`content-box` 的 `width` 只管内容，`border-box` 的 `width` 管到边框。

### 5. 布局

#### `Flex`：一维布局，适合行或列

```css
.container {
  display: flex;
  justify-content: space-between; /* 主轴对齐 */
  align-items: center;            /* 交叉轴对齐 */
  gap: 16px;
}
```

#### `Grid`：二维布局，适合行列

```css
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
}
```

> 一句话总结：一维用 Flex，二维用 Grid。

真实场景：

- 导航栏：`Flex` ，横向排列，两端对齐
- 卡片列表：`Grid` ，三列自适应

------

### 6. 响应式

响应式 = 页面能根据屏幕大小自适应。

三种手段：

1. 弹性布局：`Flex` 、`Grid` 、百分比
2. 媒体查询：

```css
@media (max-width: 768px) {
  .container { flex-direction: column; }
}
```

1. 相对单位：`rem`、`em`、`vw`、`vh`、`%`

移动优先：

```css
/* 默认移动端样式 */
.container { flex-direction: column; }

/* 大于 768px 时 */
@media (min-width: 768px) {
  .container { flex-direction: row; }
}
```

真实场景：

- 手机：表单占满宽度，纵向排列
- 桌面：表单固定 $400px$ ，居中

> 一句话总结：移动优先 = 先写小屏样式，再用 `min-width` 加桌面样式。

------

### 7. 常见属性

| 属性                 | 作用          |
| :------------------- | :------------ |
| `color`              | 文字颜色      |
| `background`         | 背景          |
| `font-size`          | 字号          |
| `font-weight`        | 字重          |
| `margin` / `padding` | 外边距/内边距 |
| `border`             | 边框          |
| `width` / `height`   | 宽高          |
| `display`            | 显示方式      |
| `position`           | 定位          |
| `flex` / `grid`      | 布局          |
| `transition`         | 过渡          |
| `transform`          | 变形          |
| `opacity`            | 透明度        |

------

### 8. 单位

| 单位        | 说明                   |
| :---------- | :--------------------- |
| `px`        | 像素，绝对单位         |
| `%`         | 相对父元素             |
| `rem`       | 相对根元素 `font-size` |
| `em`        | 相对父元素 `font-size` |
| `vw` / `vh` | 视口宽/高的百分比      |
| `fr`        | Grid 里的比例单位      |

真实场景：

- `rem` 适合做全局缩放
- `vw` 适合做全屏宽度
- `fr` 适合做 `Grid` 列宽

------

## 三、真实场景

### 场景 1：为什么点击 `label` 能聚焦输入框

```html
<label for="username">用户名</label>
<input type="text" id="username" name="username" />
```

`<label for="username">` 和 `<input id="username">` 通过 `for` 和 `id` 关联。
点 `label` 等于点 `input`。

浏览器里看到：

- 点击“用户名”三个字，输入框获得焦点

`DevTools` 里看到：

- `label` 的 `for` 属性 = `input` 的 `id` 属性

------

### 场景 2：为什么 `padding` 会撑大盒子

默认 `box-sizing: content-box`，`width` 只算 `content`。
加 `padding` 和 `border` 后总宽度变大。

```css
.box {
  width: 200px;
  padding: 20px;
  border: 5px solid black;
}
```

总宽 = $200 + 20×2 + 5×2 = 250px$ 。

用 `border-box` 解决：

```css
.box {
  box-sizing: border-box;
  width: 200px;
  padding: 20px;
  border: 5px solid black;
}
```

总宽 = $200px$ 。

`DevTools` 里看到：

- 盒模型图里，`content` 宽从 $200$ 变成 $150$
- 总宽保持 $200$

------

### 场景 3：为什么移动端字体很小

缺少：

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

没有它，移动浏览器会按 $980px$ 宽渲染再缩放。

浏览器里看到：

- 加上 `viewport` ：字正常大小
- 去掉 `viewport` ：字很小，页面缩得很小

## 四、小练习

### 1. 写一个响应式登录表单页面

**需求：**

* 把 `HTML` 和 `CSS` 串起来，写一个真实可用的页面
* 页面包含：标题、用户名输入、密码输入、登录按钮、记住我复选框
* 用语义化标签：`<main>`、`<form>`、`<label>`、`<input>`、`<button>`
* 每个输入都有对应的 `<label for>`
* 用 `CSS` 美化：居中、间距、圆角、按钮 `hover`
* 响应式：宽度小于 $768px$ 时表单占满宽度，大于时固定 $400px$ 居中
* 打开页面，看到一个居中的登录表单
* 缩小浏览器窗口，布局自适应
* 点击 `label` 能聚焦对应输入框
* 按钮 `hover` 有变化

**步骤：**

```bash
mkdir -p ~/Engineering-Learning/practice/html-css-practice # 创建 html & css 的练习文件夹
cd ~/Engineering-Learning/practice/html-css-practice 	   # 进入 html & css 的练习文件夹
touch index.html										   # 创建 index.html 文件
touch style.css											   # 创建 style.css 文件
nano index.html											   # 编辑 index.html 文件
nano style.css											   # 编辑 style.css 文件
```

在 `index.html` 中写入以下代码：

```html
<!DOCTYPE html>
<!-- 声明文档类型为 HTML5，告诉浏览器用标准模式渲染页面 -->

<html lang="zh-CN">
<!-- 整个 HTML 文档的根元素 -->
<!-- lang="zh-CN" 声明页面语言是简体中文，利于搜索引擎和无障碍阅读器 -->

<head>
  <!-- head 里放的是"给浏览器看的"信息，不会显示在页面上 -->

  <meta charset="UTF-8">
  <!-- 声明字符编码为 UTF-8，防止中文乱码 -->

  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <!-- 响应式必备：让页面宽度等于设备宽度，初始缩放比例为 1，手机上不会被缩得很小 -->

  <title>登录</title>
  <!-- 浏览器标签页上显示的标题 -->

  <link rel="stylesheet" href="style.css">
  <!-- 关联外部 CSS 文件 -->
  <!-- rel="stylesheet" 表示这是一个样式表 -->
  <!-- href="style.css" 指向同目录下的 style.css -->
</head>

<body>
  <!-- body 里的内容会真正显示在页面上 -->

  <main class="login-container">
    <!-- <main> 是语义化标签，表示页面的主要内容区 -->
    <!-- 一个页面通常只用一个 <main> -->
    <!-- class="login-container" 给它起个类名，方便 CSS 选中它（当前 CSS 没用到，作为外层容器） -->

    <form class="login-form" action="#" method="post">
      <!-- <form> 表单容器，把相关的输入项包在一起 -->
      <!-- action="#" 表示提交到当前页面（这里只是练习，# 是占位符，实际不提交） -->
      <!-- method="post" 表示用 POST 方式提交数据（练习中不会真的提交） -->
      <!-- class="login-form" 给 CSS 用，是页面上那张"卡片" -->

      <h1>登录</h1>
      <!-- 一级标题，表单的标题文字 -->

      <div class="form-group">
        <!-- 一个 div 分组，把"用户名"的 label 和 input 包在一起 -->
        <!-- 好处：方便 CSS 统一控制这一组的间距（.form-group { margin-bottom: 20px }） -->

        <label for="username">用户名</label>
        <!-- <label> 是输入框的文字标签 -->
        <!-- for="username" 是关键：它的值必须等于对应 input 的 id -->
        <!-- 这样点击"用户名"三个字，浏览器会自动聚焦到那个输入框（无障碍体验） -->

        <input type="text" id="username" name="username" placeholder="请输入用户名" required>
        <!-- <input> 输入框，单标签，不需要闭合 -->
        <!-- type="text" 表示普通文本输入 -->
        <!-- id="username" 和上面 label 的 for 对应，绑定关系 -->
        <!-- name="username" 是提交表单时数据的键名（后端接收用） -->
        <!-- placeholder="请输入用户名" 是输入框里的灰色提示文字，输入后消失 -->
        <!-- required 表示必填，不填提交时浏览器会拦截并提示 -->
      </div>

      <div class="form-group">
        <!-- 第二个分组：密码 -->

        <label for="password">密码</label>
        <!-- for="password" 对应下面 input 的 id="password" -->

        <input type="password" id="password" name="password" placeholder="请输入密码" required>
        <!-- type="password" 表示密码框，输入内容显示为圆点 ●●● -->
        <!-- 其余属性含义同上 -->
      </div>

      <div class="form-group checkbox-group">
        <!-- 这个 div 同时有 form-group 和 checkbox-group 两个类 -->
        <!-- form-group 负责下边距，checkbox-group 负责让复选框和文字横向排列 -->

        <input type="checkbox" id="remember" name="remember">
        <!-- type="checkbox" 复选框，可以勾选/取消 -->
        <!-- id="remember" 供 label 关联 -->

        <label for="remember">记住我</label>
        <!-- for="remember" 对应上面的复选框 -->
        <!-- 点击"记住我"文字，就能勾选/取消复选框 -->
      </div>

      <button type="submit">登录</button>
      <!-- <button> 按钮 -->
      <!-- type="submit" 表示点击后提交表单（会触发表单的提交行为） -->
      <!-- 如果不写 type，按钮在表单内默认就是 submit，但显式写出更清晰 -->
    </form>
  </main>
</body>

</html>
```

在 `style.css` 中写入以下代码

```css
/* 重置默认样式，让计算更可控 */
* {
  /* 通配选择器 * ，作用于页面上所有元素 */
  margin: 0;
  /* 清除浏览器给元素加的默认外边距（如 body、h1、p 都有默认 margin） */
  padding: 0;
  /* 清除默认内边距 */
  box-sizing: border-box;
  /* 关键：让 width 包含 padding 和 border */
  /* 好处：设 width:100% 时元素不会因 padding 而溢出父容器 */
}

/* 让内容在屏幕中垂直水平居中 */
body {
  font-family: -apple-system, "PingFang SC", "Microsoft YaHei", sans-serif;
  /* 字体优先级：苹果系统字体 → 苹方 → 微软雅黑 → 系统默认无衬线字体 */
  /* 浏览器会从左往右找第一个可用的字体 */

  background: linear-gradient(135deg, #667eea, #764ba2);
  /* 背景为线性渐变 */
  /* 135deg 是渐变方向（左上到右下） */
  /* #667eea（蓝紫）渐变到 #764ba2（深紫） */

  min-height: 100vh;          /* 占满整个视口高度 */
  /* vh = viewport height，100vh 等于屏幕高度的 100% */
  /* 用 min-height 而不是 height，内容多时还能撑高 */
  /* 这是让 flex 垂直居中生效的前提 */

  display: flex;              /* 用 flex 居中 */
  /* 把 body 变成弹性容器，子元素（表单）才能用 flex 居中 */

  align-items: center;        /* 垂直居中 */
  /* 交叉轴居中，让表单在垂直方向居中 */

  justify-content: center;    /* 水平居中 */
  /* 主轴居中，让表单在水平方向居中 */

  padding: 20px;
  /* body 内边距 20px，防止窗口很小时表单贴边 */
}

/* 表单卡片 */
.login-form {
  background: #fff;
  /* 白色背景，让表单从渐变背景中"浮"出来 */

  width: 400px;               /* 大于 768px 时固定 400px */
  /* 默认宽度固定 400px */

  max-width: 100%;            /* 小于 768px 时占满可用宽度 */
  /* 最大宽度不超过父容器 */
  /* 窗口小于 400px 时，表单会自动缩小到容器宽度 */

  padding: 40px 32px;
  /* 内边距：上下 40px，左右 32px */
  /* 两值写法：第一个上下，第二个左右 */

  border-radius: 12px;
  /* 圆角 12px，让卡片更柔和 */

  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.15);
  /* 阴影：x偏移0，y偏移10px，模糊30px，颜色黑色透明度15% */
  /* 营造卡片"浮起"的立体感 */
}

/* 标题 */
.login-form h1 {
  text-align: center;
  /* 文字水平居中 */

  margin-bottom: 28px;
  /* 标题下方留 28px，和表单内容拉开距离 */

  font-size: 24px;
  /* 字号 24 像素 */

  color: #333;
  /* 深灰色文字，比纯黑柔和 */
}

/* 每个输入项的分组 */
.form-group {
  margin-bottom: 20px;
  /* 每组之间留 20px 间距 */
  /* 用户名、密码、记住我三组都会应用 */
}

/* label 样式 */
.form-group label {
  display: block;
  /* 让 label 变成块级元素，独占一行 */
  /* 默认 label 是行内元素，会和 input 挤在一行 */

  margin-bottom: 8px;
  /* label 和下面输入框之间留 8px */

  font-size: 14px;
  /* 字号 14 像素，比输入内容略小 */

  color: #555;
  /* 中灰色文字 */
}

/* 输入框样式 */
.form-group input[type="text"],
.form-group input[type="password"] {
  /* 属性选择器：只选中 type 为 text 或 password 的 input */
  /* 逗号表示两个选择器共用同一套样式 */

  width: 100%;
  /* 宽度占满父容器（配合 border-box 不会溢出） */

  padding: 12px 14px;
  /* 内边距：上下 12px，左右 14px */
  /* 让输入的文字不贴着边框 */

  border: 1px solid #ddd;
  /* 边框：1px 实线，浅灰色 */

  border-radius: 8px;
  /* 输入框圆角 8px */

  font-size: 15px;
  /* 输入文字字号 15px */

  transition: border-color 0.2s, box-shadow 0.2s;
  /* 过渡动画：边框颜色和阴影变化时用 0.2 秒平滑过渡 */
  /* 让 focus 高亮不会突然跳变，而是渐变 */
}

/* 输入框获得焦点时高亮 */
.form-group input[type="text"]:focus,
.form-group input[type="password"]:focus {
  /* :focus 伪类，元素被点击/聚焦时生效 */

  outline: none;
  /* 去掉浏览器默认的聚焦外轮廓（那个蓝色/黑色框） */
  /* 因为下面我们用自定义的边框和阴影代替 */

  border-color: #667eea;
  /* 边框变成主题蓝紫色 */

  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.2);
  /* 外发光：x0 y0 模糊0 扩展3px，半透明蓝紫色 */
  /* 形成一圈柔和的"聚焦光环"效果 */
}

/* 记住我这一行：复选框和文字横向排列 */
.checkbox-group {
  display: flex;
  /* 变成弹性容器，让复选框和 label 横排 */

  align-items: center;
  /* 垂直居中对齐（复选框和文字中心对齐） */

  gap: 8px;
  /* 弹性子元素之间的间距 8px */
  /* 比用 margin 更简洁 */
}

.checkbox-group label {
  margin-bottom: 0;           /* 覆盖上面的 margin */
  /* 上面 .form-group label 设了 margin-bottom:8px */
  /* 这里重置为 0，因为复选框后面不需要下方间距 */

  cursor: pointer;
  /* 鼠标悬停时显示手型，提示可点击 */
}

.checkbox-group input[type="checkbox"] {
  width: 16px;
  /* 复选框宽度 16px */

  height: 16px;
  /* 复选框高度 16px */

  cursor: pointer;
  /* 鼠标悬停显示手型 */
}

/* 按钮样式 */
button {
  width: 100%;
  /* 按钮占满表单宽度 */

  padding: 13px;
  /* 内边距四边都是 13px，让按钮有足够高度 */

  background: #667eea;
  /* 背景主题蓝紫色 */

  color: #fff;
  /* 文字白色 */

  border: none;
  /* 去掉默认边框 */

  border-radius: 8px;
  /* 圆角 8px，和输入框保持一致 */

  font-size: 16px;
  /* 按钮文字 16px，稍大一点更醒目 */

  cursor: pointer;
  /* 鼠标悬停显示手型 */

  transition: background 0.2s, transform 0.1s;
  /* 过渡：背景色 0.2s，缩放 0.1s */
}

/* 按钮 hover 变化 */
button:hover {
  background: #5568d3;
  /* 悬停时背景变成更深的蓝紫色 */
  /* 这是练习验收点之一 */
}

/* 按钮按下时的反馈 */
button:active {
  /* :active 伪类，鼠标按下时生效 */

  transform: scale(0.98);
  /* 缩小到 98%，产生"按下"的物理感 */
}

/* 响应式：小于 768px 时 */
@media (max-width: 768px) {
  /* 媒体查询：当视口宽度 ≤ 768px 时，下面的样式生效 */
  /* 这就是手机/平板端的适配 */

  .login-form {
    width: 100%;              /* 占满宽度 */
    /* 小屏时表单不再固定 400px，而是占满可用宽度 */

    padding: 28px 20px;
    /* 小屏时内边距略小，给内容更多空间 */
  }
}
```

起本地服务

```bash
python3 -m http.server 8000
```

### 2. 用 DevTools 检查盒模型

**需求**：

- 打开练习 1 的页面
- `F12` → `Elements` → 选中一个元素
- 在右侧 `Styles` 下方找盒模型图
- 能说出 `content`、`padding`、`border`、`margin` 的值
- 能解释 `box-sizing` 的影响

- 能指着一个元素，说出四层值
- 能解释 `content-box` 和 `border-box` 的区别

**步骤：**

1. 浏览器打开你的登录页面（`http://localhost:8000`）
2. 按 **`F12`**（或右键 → 检查）
3. 切到 **`Elements`**（元素）面板
4. 点左上角的"箭头"图标（或按 `Ctrl + Shift + C`），然后**点页面上的输入框**

选中后，右侧面板会显示该元素的 `HTML` 和 `CSS` 。

在右侧 **`Styles`**（样式）面板的**最下方**，有一个彩色方框图，就是盒模型。

它由内到外四层：

```text
┌─────────────────────────────────────┐
│              margin（橙色）           │
│   ┌─────────────────────────────┐   │
│   │        border（黄色）         │   │
│   │   ┌─────────────────────┐   │   │
│   │   │   padding（绿色）     │   │   │
│   │   │   ┌─────────────┐   │   │   │
│   │   │   │  content    │   │   │   │
│   │   │   │  （蓝色）    │   │   │   │
│   │   │   └─────────────┘   │   │   │
│   │   └─────────────────────┘   │   │
│   └─────────────────────────────┘   │
└─────────────────────────────────────┘
```

| 颜色 | 层        | 含义                            |
| :--- | :-------- | :------------------------------ |
| 蓝色 | `content` | 内容区（文字/图片实际占的地方） |
| 绿色 | `padding` | 内边距（内容到边框的距离）      |
| 黄色 | `border`  | 边框                            |
| 橙色 | `margin`  | 外边距（元素到其他元素的距离）  |

**如果某层没有值，图上就显示 `-`**（比如输入框没设 `margin` ，橙色区就是 `-`）。

**对照 `CSS` ：** 

拿练习 1 里的输入框举例：

```css
.form-group input[type="text"] {
  width: 100%;
  padding: 12px 14px;     /* ← 上下 12px，左右 14px */
  border: 1px solid #ddd; /* ← 边框 1px */
  border-radius: 8px;
  font-size: 15px;
}
```

在 `DevTools` 里选中输入框，你会看到：

- **`padding`**：`12px`（上）`14px`（右）`12px`（下）`14px`（左）
- **`border`**：`1px` 四条边
- **`margin`**：`-`（没设置）
- **`content`**：显示的宽高值

**验证 `box-sizing`的影响：**

这是本练习的核心。你的 CSS 开头有这一行：

```css
* {
  box-sizing: border-box;
}
```

**现在做个小实验：**

1. 选中输入框，看盒模型里 `content` 的宽度
2. 在右侧 `Styles` 面板找到 `box-sizing: border-box` 那一行（可能显示为继承或直接写）
3. **点掉前面的勾**（取消勾选），立刻观察变化

你会看到 **`content` 的宽度变大了**。

**为什么？**

| 模式                  | `width: 100%` 的含义                                         |
| :-------------------- | :----------------------------------------------------------- |
| `content-box`（默认） | `width` 只算**内容区**，`padding 和 border` **额外加上去** → 元素实际更宽 |
| `border-box`          | `width` **包含** `content` + `padding` + `border` → 元素实际就是设定的宽度 |

**举例**：假设父容器宽 300px，输入框设 `width: 100%` + `padding: 14px` + `border: 1px`：

- `content-box`：实际宽度 = $300 + 14×2 + 1×2 = 330px$ → **溢出父容器**
- `border-box`：实际宽度 = **300px**，内容区被压缩到 $300 − 28 − 2 = 270px$ → 刚好放下

这就是为什么现代 `CSS` 都推荐 `box-sizing: border-box`，布局不会因为 `padding` 而溢出。
