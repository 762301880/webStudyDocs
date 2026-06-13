## [逻辑示例  vue2](https://www.runoob.com/try/try.php?filename=vue3-bc)

> 快捷键创建div  `div#hello-vue.demo` 
>
> [资料参考](https://www.runoob.com/vue3/vue3-syntax.html)

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/vue/3.0.5/vue.global.js"></script>
    <title>Document</title>
</head>
<body>
<div id="hello-vue" class="demo">
    {{message}}
</div>

<script>
    const HelloVueApp = {
        data() {
            return {
                message: "hello vue"
            }
        }
    }
    Vue.createApp(HelloVueApp).mount('#hello-vue')
</script>

</body>
</html>
```

## 逻辑解释

### 最终效果

你这段代码最终页面显示：

```
hello vue
```

就这么简单

### HTML：

```html
<div id="hello-vue" class="demo">
    {{message}}
</div>
```

这里有两个重点：

1. id="hello-vue"

这是给 Vue 找位置用的。

相当于：

> “Vue，你等会控制这个 div。”

类似：

```js
document.getElementById("hello-vue")
```

2. {{message}}

这个叫：模板语法（插值表达式）

意思是：

> 把 message 变量显示出来

类似：

```
console.log(message)
```

但 Vue 会自动放进 HTML。

### Vue 部分

核心：

```js
const HelloVueApp = {
    data() {
        return {
            message: "hello vue"
        }
    }
}
```

> 为什么 return？
>  为什么 data 是函数？
>  为什么里面还是对象？

其实你可以这样理解：

data 就是“数据仓库”

Vue 要知道：

> 页面有哪些变量？

所以：

```
message: "hello vue"
```

就是定义一个变量。

等价于：

```js
let message = "hello vue"
```

只是 Vue 要统一管理。

**为什么是 data() 函数？**

这是 Vue 的规定。

Vue 会主动执行：

```java
data()
```

拿到里面 return 的对象。

相当于 Vue 内部偷偷干了：

```javascript
const result = data()

console.log(result.message)
```

所以真正关键的是：

```javascript
return {
    message: "hello vue"
}
```

Vue 拿到了：

```javascript
{
   message: "hello vue"
}
```

于是：

```javascript
{{message}}
```

就能找到这个变量。

### [最后一行](https://www.runoob.com/vue3/vue3-syntax.html)

```javascript
Vue.createApp(HelloVueApp).mount('#hello-vue')
```

这是 Vue 真正启动。

拆开理解：

#### 第一步

```
Vue.createApp(HelloVueApp)
```

意思：

> 创建一个 Vue 应用

把配置传进去。

配置就是：

```
{
   data() {
      return {
         message: "hello vue"
      }
   }
}
```

#### 第二步

```
.mount('#hello-vue')
```

意思：

> 把 Vue 挂载到 id=hello-vue 的 div 上

Vue 开始接管这个区域。

Vue 接管后干了什么？

Vue 会发现：

```
{{message}}
```

然后去数据里找

```html
message
```

找到：

```
"hello vue"
```

于是替换成：

```
hello vue
```

最终浏览器看到：

```html
<div id="hello-vue">
   hello vue
</div>
```

### 总结

Vue 做的事情本质上是：

```
数据  →  自动更新到页面
```

只改数据：

```javascript
message = "hello vue"
```

页面自动变。

这就是 Vue 最大的核心。