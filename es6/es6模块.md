## [文档参考](https://www.runoob.com/w3cnote/es6-module.html)

ES6 模块化（ES Modules，简称 ESM）是 JavaScript 官方标准的模块系统。

它解决了以前 JavaScript 的几个大问题：

- 所有变量都在一个文件里，容易冲突
- 代码太大不好维护
- 不方便复用
- 多人协作容易乱

所以 ES6 提供了：

- `export` → 导出
- `import` → 导入

你可以把一个 `.js` 文件理解成：

> 一个独立的小仓库（模块）

### 一、为什么需要模块化？

以前：

```js
// a.js
var name = '张三'

// b.js
var name = '李四'
```

两个文件都叫 `name`。

最后可能互相覆盖。

模块化后：

```js
// a.js
export const name = '张三'

// b.js
export const name = '李四'
```

互不影响。

### 二、ES6 模块最核心的两个东西

| 功能 | 关键字 |
| ---- | ------ |
| 导出 | export |
| 导入 | import |

### 三、最基础模块化

#### 1. 导出（export）

math.js

```js
// 导出一个变量
export const pi = 3.14

// 导出一个函数
export function add(a, b) {
    return a + b
}
```

#### 2. 导入（import）

app.js

```js
// 导入时需要用 {}
import { pi, add } from './math.js'

console.log(pi)
console.log(add(10, 20))
```

运行效果

```js
3.14
30
```

### 四、为什么导入时要加 {} ？

这是最容易懵的地方。

核心：

加 {} 是：

“按名字导入”也叫：

命名导出（Named Export）

例如：

math.js

```js
export const name = '刘德华'

export function say() {
    console.log('你好')
}
```

这里导出了：

- name
- say

它们都有名字。

所以导入必须：

```js
import { name, say } from './math.js'
```

意思：

> 我要拿指定名字的东西。

### 五、不加 {} 导入是什么？

这是：

默认导出（Default Export）

使用：

```js
export default
```

示例

user.js

```js
// 默认导出
export default {
    name: '张三',
    age: 20
}
```

app.js

```js
// 不需要 {}
import user from './user.js'

console.log(user)
```

运行效果

```js
app.js
// 不需要 {}
import user from './user.js'

console.log(user)
```

