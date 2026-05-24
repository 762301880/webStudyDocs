## 资料

| 名称        | 地址                                         |
| ----------- | -------------------------------------------- |
| axios中文网 | [link](https://www.axios-http.cn/docs/intro) |

## 统一封装请求

> 为什么要统一封装**request.js**

### 参考

> https://caijiao.org/axios/response/global-error.html

### axios 为什么要统一封装请求（request.js）

简单说：**不封装 axios，项目会乱、难维护、易出错；统一封装成 request.js，是前端项目的「标准最佳实践」，能让所有接口请求统一管理、统一处理。**

下面用最通俗、最实用的角度，讲清楚**为什么必须封装**、**封装能解决什么问题**。

#### 一、不封装 axios 会遇到的 5 大痛点

如果你在每个页面都直接写：

```js
axios.get('/api/user')
axios.post('/api/login')
```

会出现这些问题：

1. **baseURL 重复写**

   开发 / 测试 / 生产环境地址不一样，每个请求都要改，极其麻烦。

2. **请求头（token）重复加**

​    几乎所有接口都要带 token，每个请求都写一遍 `headers: {token}`，代码冗余。

3. **错误处理重复写**

​    网络错误、401、404、500，每个请求都要写 try/catch，代码又臭又长。

4. **加载状态（loading）无法统一控制**

​    每个请求自己控制显示 / 隐藏，容易漏、容易冲突。

5. **后期维护灾难**

​    想统一加个二、统一封装 request.js 的 7 大核心好处

​    只写一次，所有接口自动继承：超时、加个请求日志、加个加密，要改几百个文件。

#### 二、统一封装 request.js 的 7 大核心好处

1. 统一配置基础路径（baseURL）

   只写一次，所有接口自动继承：

```js
const service = axios.create({
  baseURL: '/api', // 统一前缀
  timeout: 5000
})
```

2. 统一添加请求头（token 自动携带）

登录后，所有接口**自动带上 token**，不用手动写：

```js
service.interceptors.request.use(config => {
  const token = localStorage.getItem('token')
  if (token) config.headers.token = token
  return config
})
```

3. 统一处理响应结果

后端返回格式统一处理，前端直接拿数据：

```js
service.interceptors.response.use(res => {
  return res.data // 统一剥壳
})
```

4. 统一处理错误（全局异常捕获）

- 401 自动跳登录
- 403 无权限提示
- 500 服务器错误
- 断网提示
- 所有错误只写一次，所有接口生效

5. 统一加载效果（loading）

请求开始显示 loading，结束自动关闭，不用每个接口写。

6. 统一超时、重试、取消请求

项目统一规则，不用每个请求单独配置。

7. 便于后期扩展

想加：

- 请求加密

- 响应解密

- 接口日志

- 接口缓存

- 请求防抖

  只改一个文件，全部生效

####  三、一句话总结封装的意义

**统一封装 = 统一入口 + 统一配置 + 统一拦截 + 统一处理**

让项目：

- 代码更简洁
- 维护更轻松
- 扩展性更强
- 团队协作更规范

####   四、最常用的标准封装结构（request.js）

> `src\utils\request.js`

**代码**

```js
import axios from 'axios'

// 1. 创建实例
const service = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
  timeout: 10000
})

// 2. 请求拦截器：加token
service.interceptors.request.use(
  config => {
    const token = localStorage.getItem('token')
    if (token) config.headers.token = token
    return config
  }
)

// 3. 响应拦截器：统一处理数据和错误
service.interceptors.response.use(
  res => res.data,
  err => {
    // 统一错误处理
    if (err.response.status === 401) {
      // 跳登录
    }
    return Promise.reject(err)
  }
)

export default service
```

####  五、装后模板中如何使用requist.js

##### 一、先确认你的文件结构（标准

```php
src/
  utils/
    request.js   ← 你封装好的 axios
  api/
    user.js      ← 接口管理（推荐）
  views/
    Home.vue     ← 页面里使用
```

##### 二、第一步：在 api 文件夹里写接口（最规范）

新建 `src/api/user.js`

```js
import request from '@/utils/request'

// 获取用户列表
export function getUserList(params) {
  return request({
    url: '/user/list',
    method: 'get',
    params
  })
}

// 提交表单
export function addUser(data) {
  return request({
    url: '/user/add',
    method: 'post',
    data
  })
}
```

##### 三、第二步：在 Vue 模板（.vue 文件）中使用

1. Vue 3 + script setup 写法（最常用）

   ```vue
<template>
  <div>
    <button @click="getList">获取用户列表</button>
  </div>
</template>

<script setup>
import { getUserList } from '@/api/user'
import { ElMessage } from 'element-plus'

// 调用接口
async function getList() {
  try {
    const res = await getUserList({ page: 1, size: 10 })
    console.log('成功：', res)
    ElMessage.success('获取成功')
  } catch (err) {
    console.log('失败：', err)
  }
}
</script>
   ```

2. Vue 2 写法

```vue
<script>
import { getUserList } from '@/api/user'

export default {
  methods: {
    async getList() {
      const res = await getUserList({ page: 1 })
      console.log(res)
    }
  }
}
</script>
```

### 最关键的好处

1. **不用写 axios、baseURL、token、headers**
2. **不用处理 401、500、超时**
3. **接口统一管理，后期改地址超级方便**
4. **页面代码非常干净**





***

## [async/await用法](https://www.runoob.com/vue3/vue3-ajax-axios.html)

###  简单介绍

`async/await` 是一种“等待 Promise 完成”的语法，让异步代码更像同步代码、更容易维护。

### 最关键区别

你把它们想成：

| 东西  | 作用                 |
| ----- | -------------------- |
| axios | 去干活（发请求）     |
| await | 等干活结束           |
| async | 允许函数里使用 await |

#### 现实世界类比

比如你点外卖：

**axios**

相当于：

> 你下单了

```
axios.get(...)
```

已经开始请求。

**await**

相当于：

> 你坐着等外卖送到

```
await axios.get(...)
```

**async**

相当于：

> “这个房间允许等外卖”

因为 `await` 只能在 `async` 函数里。



### 代码示例

#### 使用async/await请求示例

```js
async function getUser() {
  try {
    const response = await axios.get('/user?ID=12345');
    console.log(response);
  } catch (error) {
    console.error(error);
  }
}
```

#### 不加async/await请求示例

> 不加async/await 代码很凌乱  回调地狱, 只能 **then**输出成功结果  **catch** 输出失败结果

```php
axios.get('/user', {
    params: {
      ID: 12345
    }
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  })
  .finally(function () {
    // 总是执行
  });
```

### 二者区别







***



## 疑问补充

### [`@` 到底是什么意思？](https://cli.vuejs.org/zh/guide/html-and-static-assets.html)

`@` 是 **路径别名**，**代表 `src` 这个文件夹的根目录**。

#### 1. 直观对比一看就懂

项目结构：

```bash
src/
  api/
    user.js
  utils/
    request.js
  views/
    Home.vue
```

在 **Home.vue** 里引入文件：

写法 1：不用 @（麻烦，容易写错）

```bash
import { getUserList } from '../api/user.js'
```

`../` 是返回上一级

目录深了要写 `../../../../`，**非常容易写错**

写法 2：用 @（推荐，简单）

```php
import { getUserList } from '@/api/user.js'
```

**`@` 直接 = src 文件夹**

不管你在哪个文件里，**永远从 src 根目录找文件**

不用数 `../`，**永远不会错**

#### 2. 官方规定（Vue 自带）

在 Vue 项目里：

- **`@` = `src`**（源代码根目录）
- 这是脚手架**自动配置好的**，你不用管原理，**直接用就行**

#### 3. 最常用的正确写法

```js
// 引入 api 接口
import { xxx } from '@/api/user'

// 引入工具
import request from '@/utils/request'

// 引入组件
import Hello from '@/components/Hello'
```

