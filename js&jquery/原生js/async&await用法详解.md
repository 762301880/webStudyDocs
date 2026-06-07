## 资料

| 名称         | 地址                                                         |
| ------------ | ------------------------------------------------------------ |
| **`async `** | [link](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function) |



## 先搞懂核心：为什么要有 async/await？

网络请求（`get/post`）是**异步操作**：

代码不会等接口返回结果，会直接往下跑。

如果不用 `async/await`，就得用回调、Promise，写起来乱、难读。

`async + await` 就是**让异步代码，写起来像同步代码一样简单**。

## 两个关键字单独解释

### 1. `async` 写在 **函数前面**

语法：

```js
async function 函数名() { ... }
// 箭头函数也一样
const 函数名 = async () => { ... }
```

作用：

- 标记**这个函数里面，可以使用 `await`**
- 被 `async` 修饰的函数，**永远返回 Promise**

> 记住规则：**只要函数里想用 await，函数头部必须加 async**

### 2. `await` 写在 **异步操作前面**

语法：

```js
const 结果 = await 异步操作(请求/定时器/Promise)
```

作用：

- **暂停当前函数执行**，一直等到「异步操作完成、拿到结果」
- 拿到结果后，再继续往下走代码
- **await 只能出现在被 async 修饰的函数内部**（外面用会报错）

## 逐行解析代码

### 示例 1：GET 请求函数

```js
// 1. 函数前面加 async → 内部允许用 await
const getData = async () => {
  // 2. await：等待 get 接口请求完成，拿到返回值再赋值给 res
  const res = await get('/api/list', { page: 1, size: 10 })
  // 3. 接口返回数据后，才会执行这行打印
  console.log('结果', res)
}
```

执行顺序：

1. 调用 `getData()`
2. 进入函数 → 发起网络请求
3. **停在这里等待接口返回**
4. 拿到数据赋值给 `res`
5. 执行 `console.log`

### 示例 2：POST 请求（逻辑完全一样）

```js
const postData = async () => {
  // 等待登录接口请求结束
  const res = await post('/api/login', { username: 'xxx', pwd: 'xxx' })
  // 拿到登录结果再打印
  console.log('登录结果', res)
}
```

### 示例 3：关闭 loading 的请求

```js
const noLoadingReq = async () => {
  // 同样等待接口完成，只是多传了配置项
  await get('/api/info', {}, { showLoading: false })
}
```

这里**没接收返回值**，只单纯等待请求完成。

## 最容易踩的 3 个坑（必看）

### 坑 1：函数里用了 await，但函数没加 async → 直接报错

❌ 错误：

```js
const getData = () => { // 少了 async
  const res = await get('/api/list') // 报错！
}
```

✅ 正确：**函数头必须加 async**

```js
const getData = async () => {
  const res = await get('/api/list')
}
```

------

### 坑 2：await 不能用在函数**外面**

❌ 错误（全局直接写 await）：

```js
const res = await get('/api/list') // 报错
```

✅ 正确：只能放在 `async 函数` 里，再调用函数

```js
const getData = async () => {
  const res = await get('/api/list')
}
getData() // 调用执行
```

------

### 坑 3：多个请求顺序执行 / 并行执行

#### 1）顺序执行（一个走完再走下一个）

```js
const test = async () => {
  // 先执行第一个请求，完成再走第二个
  let a = await get('/api/1')
  let b = await get('/api/2')
}
```

#### 2）并行执行（同时发请求，速度更快）

```js
const test = async () => {
  let p1 = get('/api/1')
  let p2 = get('/api/2')
  // 两个请求同时发起，再一起等结果
  let [a, b] = await Promise.all([p1, p2])
}
```

##  结合 uni-app 实际使用（点击触发请求）

页面里一般是**按钮点击调用**，完整可运行示例：

```vue
<template>
  <view>
    <button @click="getData">发起GET请求</button>
    <button @click="postData">发起POST请求</button>
  </view>
</template>

<script setup>
import { get, post } from '@/utils/request'

// 异步函数
const getData = async () => {
  const res = await get('/api/list', { page: 1, size: 10 })
  console.log('接口数据：', res)
}

const postData = async () => {
  const res = await post('/api/login', { username: 'xxx', pwd: 'xxx' })
  console.log('登录结果：', res)
}
</script>
```

点击按钮 → 执行函数 → `await` 等待接口 → 打印数据，流程非常清晰。

## 原生js代码理解示例

> Promise 意思就是异步函数

```js
// 定义一个返回 Promise 的异步函数
function delay(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

// async 函数内部才能用 await
async function asyncFunc() {
  console.log('开始');
  
  // 等待 Promise 完成，写法像同步代码一样
  await delay(2000);
  
  console.log('2秒后执行');
}

asyncFunc();
```

执行结果两秒钟后执行结果`2秒后执行`

## 总结

1. **`async`**：加在**函数头部**，开门，允许里面用 `await`
2. **`await`**：加在**异步请求前面**，原地等待请求完成
3. 组合规则：**有 await 的地方，外层函数一定要有 async**



