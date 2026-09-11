## px与rpx的区别

### 结论

**rpx** 小程序与uniapp专用

CSS px、rpx 区别 + 英文含义 + 代码示例

我给你用**最简单、最直白**的方式讲清楚，新手一看就懂。

### 一、先记英文全称 + 中文意思

| 单位 |       英文全称       |         中文含义         |
| :--: | :------------------: | :----------------------: |
|  px  |      **pixel**       |  像素（屏幕最小显示点）  |
| rpx  | **responsive pixel** | 响应式像素（自适应屏幕） |

### 二、核心区别（最重要）

#### px（固定像素）

- **固定大小**，在任何屏幕上都一样大
- 不会自动适配手机、平板、电脑
- 适合：边框、固定大小的图标、不需要自适应的元素

####  rpx（自适应像素）

- **会自动缩放**，适配所有手机屏幕
- 是**微信小程序 /  uni-app** 专用单位
- 规则：**屏幕宽度永远 = 750 rpx**
- 适合：页面布局、文字、宽度、高度（移动端开发）

### 三、代码示例

#### 1. px 示例（固定大小）

```css
/* 不管什么手机，盒子永远 200px 宽，文字永远 16px */
.box {
  width: 200px;
  height: 100px;
  font-size: 16px;
  border: 1px solid #000;
}
```

#### 2. rpx 示例（自适应，小程序 /uni-app）

```css
/* 盒子占屏幕一半宽度，所有手机都一样比例 */
.box {
  width: 375rpx; /* 屏幕一半 */
  height: 100rpx;
  font-size: 32rpx; /* 等于 16px */
  border: 2rpx solid #000; /* 等于 1px */
}
```

### 四、一句话总结

- **px**：固定不变，电脑 / 网页常用
- **rpx**：自动适配，**小程序 /uni-app 移动端专用**

### 总结

1. **px = pixel 像素**（固定）
2. **rpx = responsive pixel 响应式像素**（自适应）
3. `750rpx = 屏幕总宽度`
4. 小程序用 **rpx**，普通网页用 **px**

---

## :hover(伪类)  是什么意思

`:hover` 是 **CSS 伪类**，作用：**当鼠标悬浮（悬停）在这个元素上的时候，才应用里面写的样式**。

> 伪类不是真实的 class 类名，前面带冒号 `:`，用来描述元素的**特定状态**。
>
> 解释  **伪类 `:`**：描述**元素的状态**（`:hover` 鼠标悬浮、`:active`点击），**元素本身真实存在**

**简单例子**

```css
a {
  text-decoration: none;
  color: #333;
}
/* 鼠标放上去的时候触发 */
a:hover {
  color: #409eff; /* 悬浮变蓝色 */
}
```

```html
<a href="#">点我测试</a>
```

效果：

- 默认：文字灰色，无下划线
- ✅ 鼠标移上去：文字变成蓝色
- 鼠标移开：恢复原样

---

## ::(双冒号,伪元素)

 伪元素 `::`**：创建**不在 HTML 里的虚拟元素**，相当于凭空生成一个盒子，插入到目标元素内部，**DOM 里看不见这个标签**

> 规范写法是双冒号 `::`，用来和伪类 `:` 区分。旧浏览器支持单冒号 `:`，现在推荐写 `::`

###  什么时候优先用伪元素？

> 原则：**只用来做装饰，不是页面内容**（箭头、分割线、圆点、背景遮罩、装饰边框）

1. 保持 HTML 结构干净

   HTML 只放「有意义的内容」：标题、文字、按钮、图片。

   装饰性的小东西（圆点、箭头）属于样式层，交给 CSS 管，不要污染 HTML。

> 语义化：浏览器、爬虫读 HTML 的时候，`<span class="dot">` 没有任何含义，只是个视觉装饰。

1. **减少 DOM 节点数量**

   页面大量装饰元素时，少一堆无用 span，DOM 树更轻。虽然小页面感知不到，但大型官网 / 页面会有影响。

2. **动态批量生成装饰**

   比如列表里每一项前面都有圆点。用伪元素，**只需要写一次 CSS，所有列表项自动带上圆点**。

   如果用 span，你就得每一条`<li>`都手动加`<span>`，改需求的时候要全部改一遍。

**什么情况下用伪元素案例**

```css
/* 一行CSS，所有li自动加前面圆点 */
li::before {
  content:"";
  width:6px;height:6px;background:blue;border-radius:50%;
  display:inline-block;margin-right:6px;
}
```

```html
<ul>
  <li>第一项</li>
  <li>第二项</li>
  <li>第三项</li>
</ul>
```



### 最常用的 4 个伪元素

1. `::before`：在目标元素**内部最前面**插入虚拟内容
2. `::after`：在目标元素**内部最后面**插入虚拟内容（**开发最常用！**）
3. `::first-line`：选中元素第一行文字
4. `::first-letter`：选中第一个字符

### **案例**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>伪元素 ::before 演示</title>
    <style>
        .title {
            font-size: 26px;
            padding-left: 28px; /* 留出位置给前面的圆点 */
            position: relative;
            cursor: pointer;
            color: #333;
        }

        /* ✅ ::before 伪元素：在文字【前面】生成一个圆形 */
        .title::before {
            content: ""; /* 空内容，用来画盒子，不用文字 */
            width: 16px;
            height: 16px;
            background-color: #3b82f6;
            border-radius: 50%; /* 变成圆形 */
            position: absolute;
            left: 0;
            top: 50%;
            transform: translateY(-50%);
            transition: all 0.3s ease; /* 动画过渡 */
        }

        /* 鼠标悬浮时，修改伪元素样式 */
        .title:hover::before {
            background-color: #ef4444;
            width: 22px;
            height: 22px;
        }
    </style>
</head>
<body>
    <h2 class="title">我是标题，前面的圆点是伪元素生成的</h2>
</body>
</html>
```




