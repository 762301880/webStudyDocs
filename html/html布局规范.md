## HTML 网页骨架（基础模板，直接复制就能用）

````html
<!DOCTYPE html>
<!-- 文档声明：告诉浏览器这是HTML5文档 -->
<html lang="zh-CN">
<!-- lang="zh-CN" 网页语言：中文 -->
<head>
    <!-- 头部：网页看不见的配置信息 -->
    <meta charset="UTF-8">
    <!-- 字符编码，防止中文乱码，必须写 -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- 移动端适配：手机打开网页不会缩小 -->
    <title>我的第一个网页</title>
    <!-- 浏览器标签上显示的标题 -->
    <style>
        /* 这里写CSS样式，美化页面 */
    </style>
</head>
<body>
    <!-- 主体：页面上所有看得见的内容都写在这里 -->
    <header>网页头部：logo、导航栏</header>
    <main>网页主要内容区域</main>
    <footer>网页底部：版权信息</footer>
</body>
</html>
````

> 记忆口诀：<!DOCTYPE> → <html> → <head> → <body>
> head 里面放配置；body 里面放页面内容。

常用语义标签（推荐用来搭建页面骨架，比单纯 div 好）

| 标签        | 作用                                     |
| ----------- | ---------------------------------------- |
| `<header>`  | 页面头部 / 模块头部                      |
| `<nav>`     | 导航菜单                                 |
| `<main>`    | 页面主体内容（一个页面只能有 1 个 main） |
| `<section>` | 一块内容区域，比如产品介绍板块           |
| `<article>` | 文章、帖子类独立内容                     |
| `<aside>`   | 侧边栏                                   |
| `<footer>`  | 页面底部                                 |

如果只是一块通用容器，就用 `<div>`（无语义盒子，最常用）

## id 和 class 是什么？

简单一句话：

- **class：类名，给元素起 “类别名字”，多个元素可以共用同一个 class，CSS/JS 都常用**

- **id：唯一编号，整个页面只能出现 1 次，不能重复，一般 JS 获取元素用，不推荐用来写 CSS 样式**

  ✅ 同一个标签可以写**多个 class**，空格隔开；

  ❌ id 整个页面只能有一个叫 `banner` 的，不能重复。

  > CSS 选择器：
  >
  > `.box` 选中 class="box"
  >
  > `#banner` 选中 id="banner"

## class /id 命名规范（前端通用，小白通用）

 推荐：短横线命名法（kebab-case，前端最流行）

单词全部小写，多个单词之间用 **减号 `-`** 分隔，**不要空格、不要驼峰、不要下划线**

```html
<!-- 好例子 -->
<header class="page-header"></header>
<nav class="main-nav"></nav>
<section class="product-list"></section>
<div class="product-item"></div>
<div class="product-item-title"></div>
```

### 常用命名参考

页面结构类

- `page-header` 页面头部
- `page-footer` 页面底部
- `main-wrap` 主体容器
- `container` 内容容器（居中盒子）
- `sidebar` 侧边栏

模块组件类

- `nav-list` 导航列表
- `nav-item` 导航单项
- `banner` 轮播横幅
- `card-list` 卡片列表
- `card-item` 单个卡片
- `btn` 按钮
- `btn-primary` 主按钮

状态类（描述状态）

- `active` 激活 / 选中
- `hide` 隐藏
- `disabled` 禁用

❌ 不要这么写

1. 不要中文命名：`class="头部"` ❌
2. 不要大写：`class="PageHeader"`（JS 驼峰可以，html class 不推荐）❌
3. 不要拼音：`class="daohang"` ❌
4. 不要无意义名字：`class="aaa bbb ccc"` ❌ 以后自己看不懂
5. id 不要重复！`<div id="box">` 页面只能有一个

### id 命名建议

id 尽量少用，只有需要 JS 单独获取这个唯一元素的时候再加，命名同样短横线：

```html
<div id="submit-btn"></div>
```

### 完整的综合示例（带骨架 + class）

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>网页骨架示例</title>
    <style>
        /* 用class写样式 */
        .page-header {
            background: #333;
            color: #fff;
            padding: 20px;
        }
        .main-nav a {
            color: white;
            margin: 0 10px;
        }
        .container {
            width: 1000px;
            margin: 0 auto;
        }
        .product-list {
            display: flex;
            gap:20px;
        }
        .product-item {
            border:1px solid #eee;
            padding:10px;
        }
        .page-footer {
            text-align:center;
            margin-top:30px;
            padding:20px;
            background:#f5f5f5;
        }
    </style>
</head>
<body>
    <!-- 页面头部 -->
    <header class="page-header">
        <div class="container">
            <nav class="main-nav">
                <a href="#">首页</a>
                <a href="#">产品</a>
                <a href="#">关于我们</a>
            </nav>
        </div>
    </header>

    <!-- 主体内容 -->
    <main class="container">
        <section class="product-list">
            <div class="product-item">
                <h3 class="product-title">商品1</h3>
                <p class="product-desc">商品描述</p>
            </div>
            <div class="product-item">
                <h3 class="product-title">商品2</h3>
                <p class="product-desc">商品描述</p>
            </div>
        </section>
    </main>

    <!-- 页面底部 -->
    <footer class="page-footer">
        <p>版权所有 © 2026</p>
    </footer>
</body>
</html>
```

