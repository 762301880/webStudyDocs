## 资料

| 名称            | 地址                                                         |
| --------------- | ------------------------------------------------------------ |
| Promise官方文档 | [link](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) |



## 原生代码示例

### Promise.js

```js
// 1. 创建 Promise 对象（定义一个“承诺”）
let myPromise = new Promise((resolve, reject) => {
    console.log("Promise 内部开始执行了！"); // 你会看到这句话马上执行

    // 异步操作：等2秒
    setTimeout(() => {
        // 成功 → 返回结果
        resolve("拿到数据啦！");

        // 失败 → 返回错误
        // reject("出错了！");
    }, 2000);
});

// 2. 执行 Promise：用 .then() 接收结果
myPromise.then((result) => {
    console.log(result); // 2秒后输出：拿到数据啦！
}).catch((err) => {
    console.log(err);   // 如果失败会走这里
});
```

**示例:改为用 await 执行（更简单）**

```js
let myPromise = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve("拿到数据啦！");
    }, 2000);
});

// 必须放在 async 函数里才能用 await
async function run() {
    let result = await myPromise; // 等待 Promise 完成
    console.log(result);
}

run(); // 调用函数，Promise 才会执行
```

## 疑问uniapp封装的请求类为什么要用**Promise包裹**

**请求类代码**

```js
//以下代码为什么要用pomise   // utils/request.js

// 获取环境标识
const env = import.meta.env.VITE_APP_ENV

// 定义 baseUrl（在外面定义，if 里只赋值）
let baseUrl

// 判断环境
if (env === 'local') {
  // 本地环境
  baseUrl='https://api.xxx.com'
} else {
  // 生产环境
  baseUrl = 'https://api.xxx.com'
}


const timeout = 10000 // 超时 10 秒

// 统一请求函数
const request = (options) => {
  // 1. 解构参数
  const { url, data = {}, method = 'GET', showLoading = true, header = {} } = options

  // 2. 全局 Loading
  if (showLoading) {
    uni.showLoading({
      title: '加载中...',
      mask: true
    })
  }

  // 3. 拼接请求头（携带 Token）
  const token = uni.getStorageSync('token')
  const headers = {
    'content-type': 'application/json;charset=UTF-8',
    ...header
  }
  if (token) {
    headers.Authorization = `Bearer ${token}`
  }

  // 4. 返回 Promise 方便 async/await 使用
  return new Promise((resolve, reject) => {
    uni.request({
      url: baseURL + url,
      data,
      method,
      header: headers,
      timeout,

      // 响应成功（网络正常）
      success: (res) => {
        uni.hideLoading()
        const { statusCode, data: resData } = res

        // 状态码判断
        if (statusCode === 200) {
          // 业务码根据你们后端约定修改（示例：code=200 成功）
          if (resData.code === 200) {
            resolve(resData)
          } else if (resData.code === 401) {
            // Token 失效，跳转登录
            uni.showToast({ title: '登录已过期，请重新登录', icon: 'none' })
            uni.removeStorageSync('token')
            setTimeout(() => {
              uni.reLaunch({ url: '/pages/login/login' })
            }, 1200)
            reject(resData)
          } else {
            // 后端返回错误提示
            uni.showToast({ title: resData.msg || '请求失败', icon: 'none' })
            reject(resData)
          }
        } else {
          uni.showToast({ title: `服务器错误 ${statusCode}`, icon: 'none' })
          reject(res)
        }
      },

      // 网络请求失败
      fail: (err) => {
        uni.hideLoading()
        uni.showToast({ title: '网络异常，请检查网络', icon: 'none' })
        reject(err)
      },

      // 请求结束
      complete: () => {
        // 可做收尾逻辑
      }
    })
  })
}

// 封装常用方法：get / post / put / delete
export const get = (url, data, opts = {}) => {
  return request({ url, data, method: 'GET', ...opts })
}

export const post = (url, data, opts = {}) => {
  return request({ url, data, method: 'POST', ...opts })
}

export const put = (url, data, opts = {}) => {
  return request({ url, data, method: 'PUT', ...opts })
}

export const del = (url, data, opts = {}) => {
  return request({ url, data, method: 'DELETE', ...opts })
}

export default request
```

### 一、先搞懂核心前提

`uni.request` 本身是**回调形式的异步函数**：

它不会立刻拿到接口结果，而是等网络请求走完，才会触发 `success` / `fail` 回调。

原生用法长这样（不用 Promise）：

```js
// 原生 uni.request 写法
uni.request({
  url: "xxx",
  success: (res) => {
    // 成功结果只能写在这个函数里面
    console.log(res)
  },
  fail: (err) => {
    // 失败也只能写在这里
    console.log(err)
  }
})
```

问题来了：

如果我在**页面里调用这个请求**，想**拿到接口返回的数据**，原生回调会非常难用。

而 **Promise 就是为了把「回调写法」改成「正常调用写法」**。

### 二、为什么你这段代码要 return Promise？

一句话结论：

**把 uni.request 这种「回调异步」，包装成支持 `.then()` 和 `async/await` 的标准异步写法，方便页面统一调用。**

## 场景对比：不用 Promise VS 用 Promise

#### 1）如果不包 Promise（纯原生回调）

你的 `request` 函数没法把数据**返回出去**。

```js
// 错误示范：不包 Promise
const request = (options) => {
  uni.request({
    // ...省略配置
    success: (res) => {
      // 数据只存在回调里，外面拿不到！
      return res.data 
    }
  })
}

// 页面调用
let result = request({url: "/api/list"})
console.log(result) // 永远是 undefined！
```

原因：

`uni.request` 是异步的，函数执行完了，请求还没结束，**数据还没产生**，所以拿不到返回值

#### 2）包上 Promise 之后（你代码的做法）

```js
const request = (options) => {
  // 返回一个 Promise 对象
  return new Promise((resolve, reject) => {
    uni.request({
      success: (res) => {
        // 请求成功 → 把数据丢给 resolve
        resolve(res.data) 
      },
      fail: (err) => {
        // 请求失败 → 把错误丢给 reject
        reject(err)
      }
    })
  })
}
```

现在**页面就能正常接收结果**了，两种主流调用方式：

##### 方式 1：.then () 写法（传统 Promise）

```js
// 页面中使用
import { get } from "@/utils/request"

get("/api/list", {})
.then(res => {
  // res 就是 resolve 传出来的接口数据
  console.log("拿到接口数据", res)
})
.catch(err => {
  // 网络/业务错误，走这里
  console.log("请求失败", err)
})
```

##### 方式 2：async /await（现在最常用，同步写法）

这也是**封装 Promise 最大的意义**！

`async/await` 语法**必须依赖 Promise** 才能使用。

```js
// 页面中使用
import { get } from "@/utils/request"

// 必须写 async
async function getData() {
  try {
    // 像调用普通函数一样，直接拿到结果
    let res = await get("/api/list", {})
    console.log("接口数据：", res)
  } catch (err) {
    // 捕获所有错误
    console.log("请求出错：", err)
  }
}

getData()
```

> 小白重点记：
>
> `await` 能像**同步代码**一样写异步请求，代码可读性爆炸提升，而 **await 只认识 Promise**。
>
> 你这段封装，本质就是：**给 uni.request 套一层外壳，让它支持 await**。

### 逐行拆解你代码里的 Promise 逻辑

只看核心片段，其他 loading、token 都是业务逻辑：

```js
// 整个 request 函数最终返回 Promise
return new Promise((resolve, reject) => {

  // 执行原生异步请求
  uni.request({
    // ... 头部、url、loading 等配置

    // 网络请求成功（有响应）
    success: (res) => {
      uni.hideLoading()

      // 后端业务判断：状态码正常、业务码正常
      if (statusCode === 200 && resData.code === 200) {
        // ✅ 一切正常：调用 resolve，把数据抛到外层
        resolve(resData) 
      } 
      // 登录过期 / 后端报错
      else {
        // ❌ 业务失败：调用 reject，把错误抛到外层
        reject(resData)
      }
    },

    // 网络直接失败（断网、接口不存在）
    fail: (err) => {
      uni.hideLoading()
      reject(err) // ❌ 网络失败，也走 reject
    }
  })
})
```

### 两个关键函数作用（死记这两句）

1. **resolve (值)**

   代表：**异步任务圆满完成**

   外面调用时，数据会进到 `.then()` 或者 `await` 的返回值里。

2. **reject (错误信息)**

   代表：**异步任务失败**

   外面调用时，错误会进到 `.catch()` 或者 `try/catch` 的 `catch` 里。

### 结合业务：为什么要统一封装？

1. 统一处理 loading

   

   所有接口不用每个页面都写 

   ```
   showLoading / hideLoading
   ```

2. 统一携带 token不用每个接口手动加请求头。

3. 统一错误处理401 登录过期、服务器报错、网络异常，全部在这一层处理。

4. 对外暴露统一格式页面只需要关心：

   成功拿数据，失败弹提示。

而实现这一切的前提：**用 Promise 把回调请求包装起来**。

### 最简总结（小白背诵版）

1. `uni.request` 是**回调式异步**，数据拿不出来，不能用 `await`。

2. `new Promise` 就是给它套一层「中转壳子」。

3. 请求成功 → 执行 `resolve(数据)`，外面 `.then` / `await` 接收。

4. 请求失败 → 执行 `reject(错误)`，外面 `.catch` / `catch` 捕获。

5. 你这套封装的

   最终目的：

   让全项目所有接口，都能用 

   ```
   async/await
   ```

    优雅调用，统一管理请求逻辑。

##  代码极简演示（单独测试理解）

> **promise.js**

```js
// 模拟 uni.request（异步回调）
function mockRequest(cb) {
    setTimeout(() => {
        cb.success({ code: 200, data: "我是接口返回的数据" })
    }, 1000)
}

// 封装成 Promise
function myRequest() {
    return new Promise((resolve, reject) => {
        mockRequest({
            success: (res) => {
                resolve(res) // 成功传出
            },
            fail: (err) => {
                reject(err) // 失败传出
            }
        })
    })
}

// 方式1：then 调用
myRequest().then(res => {
    console.log("then 获取数据：", res)
})

// 方式2：await 调用（最常用）
async function test() {
    let res = await myRequest()
    console.log("await 获取数据：", res)
}
test()
```

运行后就能看到：**异步数据成功被拿到**。