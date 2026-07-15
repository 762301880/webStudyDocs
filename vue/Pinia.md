## 资料

| 名称           | 地址                                                    |
| -------------- | ------------------------------------------------------- |
| pinia 官网     | [link](https://pinia.vuejs.org/zh/getting-started.html) |
| 菜鸟教程-pinia | [link](https://www.runoob.com/vue3/vue3-pinia.html)     |

## 简介

### pinia是干嘛的

Pinia = Vue 官方新一代**全局状态管理工具**（替代旧版 Vuex）

作用：**存全局共享数据**

比如：登录用户信息、购物车、全局弹窗状态、主题色、多页面共用的列表数据

## 为什么要用 Pinia

### 1. 不用 Pinia 的痛点（原生 Vue 传参有多麻烦）

假设页面 A 要把用户信息传给页面 C：

页面 A → 组件 B → 页面 C

1. 父子组件传值：`props` 层层透传，层级多了代码巨乱（prop 钻地狱）
2. 跨无关联组件传值：原生 `provide/inject` 调试困难、类型不友好
3. 数据分散在各个页面，修改数据每个页面都要改，难以统一管理

举个场景：登录后用户名，首页、个人中心、购物车都要显示

不用状态库：每个页面接口单独请求一遍，浪费网络；修改昵称所有页面不会同步更新

用 Pinia：登录一次存入全局，所有页面直接读取，一处修改全局自动更新

### 2. Pinia 对比旧版 Vuex 的巨大优势（为什么现在都用 Pinia）

1. 极简 API，没有复杂概念

   Vuex 强制区分：state /getters/mutations /actions，修改数据必须走 mutations，代码啰嗦

   Pinia：直接修改 state，不用 mutations，新手零学习成本

2. **完美支持 TS 类型**，自动推导类型，写代码有提示不报错

3. 模块化天然拆分

   不用 Vuex 的 modules 嵌套，每个业务单独新建一个 store 文件，比如 userStore、cartStore，结构清晰

4. **无需根仓库注册**，想用哪个仓库直接导入即可

5. **开发工具强大**：浏览器 Vue 调试面板，能看到数据变化、记录操作、一键回滚数据

6. **同时支持 Vue2 + Vue3**，Vue3 项目官方推荐优先 Pinia

7. 去掉冗余 `commit/dispatch` 语法，代码量减半

### 3. 总结一句话：Pinia 的核心价值

**统一管理全局共享数据，跨组件 / 跨页面数据同步，简化传参逻辑，代码更好维护、更好调试**

## 安装

### 安装

```php
# pnpm（推荐）
pnpm add pinia

# npm
npm install pinia

# yarn
yarn add pinia
```

### 全局注册 main.js/main.ts

```js
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'

// 创建 pinia 实例
const pinia = createPinia()
const app = createApp(App)

app.use(pinia)
app.mount('#app')
```

## 实战完整示例：用户仓库 user.js

### 步骤 1：新建仓库文件

src 下新建文件夹 `stores`，新建 `user.js`

```js
// stores/user.js
// 1. 引入定义仓库的函数 defineStore
import { defineStore } from 'pinia'

// 2. 定义仓库，第一个参数：仓库唯一ID（不能重复）
export const useUserStore = defineStore('user', {
  // state：存放全局变量，必须是函数（防止多组件实例数据污染）
  state: () => {
    return {
      // 用户基础信息
      userId: '',
      username: '',
      token: '',
      isLogin: false // 是否登录
    }
  },

  // getters：计算属性，缓存派生数据
  getters: {
    // 判断是否已登录（简化读取）
    loginStatus: (state) => state.isLogin,
    // 获取带前缀的用户名
    nickName: (state) => {
      return state.username ? `用户${state.username}` : '未登录'
    }
  },

  // actions：修改数据、写接口请求（同步异步都放这）
  actions: {
    // 同步方法：直接修改用户信息
    setUserInfo(user) {
      // 直接赋值修改state，不用像vuex一样写mutation
      this.userId = user.id
      this.username = user.name
      this.token = user.token
      this.isLogin = true
    },

    // 异步方法：登录请求（接口请求写在actions）
    async login(account, pwd) {
      // 模拟后端接口请求
      const res = await new Promise((resolve) => {
        setTimeout(() => {
          resolve({ id: 1001, name: '小白', token: 'abc123xyz' })
        }, 1000)
      })
      // 请求成功后调用上面同步方法存入全局
      this.setUserInfo(res)
      return res
    },

    // 退出登录，清空所有用户数据
    logout() {
      // $reset() pinia自带方法，一键重置当前仓库所有state为初始值
      this.$reset()
    }
  }
})
```

### 页面中使用仓库（三种常用写法）

#### 写法 1：基础使用（最常用）

```vue
<template>
  <div>
    <p>用户名：{{ userStore.username }}</p>
    <p>展示昵称：{{ userStore.nickName }}</p>
    <button @click="loginHandle">登录</button>
    <button @click="logoutHandle">退出</button>
  </div>
</template>

<script setup>
// 导入仓库函数
import { useUserStore } from '@/stores/user'
// 实例化仓库
const userStore = useUserStore()

// 调用actions登录
const loginHandle = async () => {
  await userStore.login('admin', '123456')
}

// 调用退出登录
const logoutHandle = () => {
  userStore.logout()
}
</script>
```

####  写法 2：解构读取数据（注意直接解构会丢失响应式！）

错误示范（数据修改不会更新页面）：

```vue
// 错误！直接解构state失去响应式
const { username, isLogin } = useUserStore()
```

正确解构：使用 `storeToRefs` 保持响应式

```php
<script setup>
import { useUserStore } from '@/stores/user'
import { storeToRefs } from 'pinia'

const userStore = useUserStore()
// 数据用storeToRefs解构（state、getters）
const { username, nickName } = storeToRefs(userStore)
// 方法直接解构actions，无响应式问题
const { login, logout } = userStore
</script>

<template>
  <p>{{ username }}</p>
  <p>{{ nickName }}</p>
  <button @click="login">登录</button>
</template>
```

#### 写法 3：直接修改 state（简单场景不用写 actions）

Pinia 允许页面直接修改 state，Vuex 不允许，这是一大简化

```vue
const userStore = useUserStore()
// 直接修改单个变量
userStore.username = '新名字'

// 批量修改多个变量 $patch
userStore.$patch({
  username: '批量修改',
  userId: 2002
})
```



## Pinia 实用内置 API（小白常用）

### `$reset()`：重置当前仓库所有 state 为初始值（退出登录清空数据）

```vue
userStore.$reset()
```

`$patch(对象)`：批量修改多个 state，推荐代替多次赋值

`$subscribe`：监听 state 数据变化（数据一改自动触发回调）

```js
userStore.$subscribe((mutation, state) => {
  console.log('用户数据变了', state)
  // 可以持久化存入本地存储localStorage
  localStorage.setItem('user', JSON.stringify(state))
})
```

数据持久化（进阶，刷新页面不丢失登录态）

安装插件：`pinia-plugin-persistedstate`，一行配置自动存 localStorage



---

## Pinia 四大核心概念（超通俗解释）重要

**store**：仓库，一个业务对应一个仓库文件（用户仓库、购物车仓库）

**state**：仓库的**数据源**，存所有变量（等同于组件的 data）

**getters**：仓库的计算属性（等同于组件 computed，基于 state 派生数据）

**actions**：仓库的方法（等同于组件 methods，同步 / 异步请求接口都写这里）



## pinia是基于内存的

**Pinia 本质上就是运行在浏览器 JavaScript 内存中的状态管理器。**

```bash
浏览器打开
    ↓
创建 Vue App
    ↓
创建 Pinia Store
    ↓
数据存在内存
```

### F5刷新

```bash
F5
 ↓
整个 JS 环境销毁
 ↓
Vue重新加载
 ↓
Pinia重新创建
```

## 持久化Pinia(做权限管理的话不推荐只是做一下记录而已)

> 因为持久化缓存的话 后台改了权限 你刷新还是旧权限体验感不好

```bash
pnpm install pinia-plugin-persistedstate
```

### main.js

```js
import { createPinia } from 'pinia'
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate'

const pinia = createPinia()

pinia.use(piniaPluginPersistedstate)

app.use(pinia)
```



### 改store的存储

```js

import { defineStore } from 'pinia'

export const usePermissionStore = defineStore('permission', {
  state: () => ({
    apiPermissionList: [] as string[]
  }),

  actions: {
    setApiPermission(list: string[]) {
      this.apiPermissionList = list
    },

    hasApiPerm(apiPath: string) {
      return this.apiPermissionList.includes(apiPath)
    }
  },

  persist: true  //加一行这个就行了
})
```

**解决webstorm加了`persist: true`后报错**

让 tsconfig 识别类型文件(打开项目根目录 `tsconfig.json`，在 `include` 里加入 `src/types/**/*.d.ts`)

> **记得加完重启webstorm**

```js
{
  "compilerOptions": {
    // 你的原有配置...
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.d.ts",
    "src/types/**/*.d.ts",
    "src/**/*.vue"
  ]
}
```

## Vue RBAC 权限管理为什么离不开 Pinia（全局状态库）

先讲核心：RBAC 是**全应用级的权限控制**，菜单、按钮、接口鉴权要在**任意页面、任意组件读取权限**，原生 Vue 的父子传参完全扛不住，Pinia 专门解决全局共享状态。

### 一、先搞懂 RBAC 前端要存什么权限数据

后端登录后返回当前用户的权限集合，前端需要全局持有：

1. 接口权限标识（`/system/user/add`、`/system/user/delete`）—— 控制按钮是否显示、请求拦截

2. 菜单权限列表 —— 动态过滤侧边栏菜单

3. 角色、用户信息、数据权限标识

   

   这些数据

   整个项目所有页面都要读取

   ：侧边栏、表格操作按钮、弹窗、路由守卫、请求拦截器。

### 二、不用 Pinia 会遇到的致命痛点

#### 1. 父子组件层层传参（props 穿透地狱）

首页 Layout 布局、子页面、弹窗、深层表格组件，每一层都要传递权限数组，组件嵌套深了极其繁琐，改一处全页面改。

#### 2. 临时存 localStorage，每次用都要读取解析

如果只存在本地缓存，每次判断按钮权限都要 `JSON.parse(localStorage.getItem('perm'))`，重复解析、代码冗余；

而且**响应式失效**：权限更新后页面不会自动刷新菜单 / 按钮，必须手动刷新页面。

#### 3. 路由守卫、axios 拦截器拿不到组件内数据

`router/index.ts`、`request.ts` 是独立 JS 文件，不属于任何 Vue 组件，根本访问不到组件内`ref`定义的权限变量，无法做全局路由鉴权、接口拦截。

#### 4. 权限更新无法全局同步

切换账号、重新获取权限后，所有页面、弹窗、菜单不会自动更新，页面权限显示错乱。

### 三、Pinia 完美适配 RBAC 的 4 个核心优势

#### 1. 全局单例仓库，任何文件直接读取

不管是 Layout 侧边栏组件、任意页面、axios 请求拦截、路由守卫，都能一行代码拿到权限：

```js
// 任意ts/vue文件直接导入，不用传参
import { usePermissionStore } from '@/stores/permission'
const permStore = usePermissionStore()
permStore.hasApiPerm('/user/delete') // 直接鉴权
```

路由守卫示例（独立 js 文件也能用）：

```js
// router/index.ts
import { usePermissionStore } from '@/stores/permission'
router.beforeEach(async (to, from, next) => {
  const permStore = usePermissionStore()
  // 判断是否拥有访问页面的权限
})
```

#### 2. 完整响应式，权限一变，全页面自动更新

你之前的菜单 `computed(()=>filterMenuList)` 依赖 Pinia 的`apiPermissionList`：

- 登录接口赋值权限 `permStore.setApiPermission()`

- 仓库状态更新后，

  所有依赖权限的模板、计算属性自动重新渲染

  

  侧边栏菜单、页面新增 / 删除按钮实时显示 / 隐藏，不用手动刷新页面。

#### 3. 统一封装权限方法，全局复用

把鉴权逻辑封装在 store 的`actions`里，全项目统一调用，不用重复写判断逻辑：

```js
// store统一方法
hasApiPerm(apiPath: string): boolean {
  return this.apiPermissionList.includes(apiPath)
}
// 所有页面通用，统一维护，修改只改一处
v-if="permStore.hasApiPerm('/user/add')"
```

#### 4. 搭配持久化插件，刷新页面不丢失权限

你用到的`pinia-plugin-persistedstate`，开启`persist:true`后，权限数组自动存入 localStorage：

- 页面刷新、关闭重开，不用重复请求用户信息、重新获取权限
- 不用自己手写 localStorage 存取、JSON 序列化，自动完成

### 四、RBAC 完整工作流程（Pinia 是中间枢纽）

1. 登录页面：登录成功，调用接口获取用户权限数组
2. 存入 Pinia 权限仓库 `permStore.setApiPermission(list)`
3. 全局任意位置读取仓库做鉴权：
   - Layout 侧边栏：computed 过滤有权限的菜单
   - 页面按钮：`v-if` 判断接口权限，隐藏无权限操作
   - 路由守卫：校验页面访问权限，无权限跳转 403
   - 请求拦截器：接口无权限时统一拦截报错
4. 切换账号 / 退出登录：清空 Pinia 权限仓库，自动清空所有页面权限

### 五、补充：为什么不用 Vuex，现在全用 Pinia？

Vue2 时代用 Vuex，Vue3 官方主推 Pinia，对 RBAC 更友好：

1. 去掉繁琐的 mutation，直接 action 修改状态，权限赋值代码更简洁
2. 无命名空间，每个业务独立仓库（权限、用户、标签页分开），解耦
3. TS 类型天然友好，权限数组、鉴权方法自动类型提示
4. 体积更小、响应式性能更好，配合持久化插件开箱即用

### 极简总结

RBAC 需要**跨组件、跨文件、全局共享、响应式自动更新**的权限数据：

- 组件内 ref 只在当前页面生效，跨组件传递麻烦；
- localStorage 无响应式，每次使用重复解析；
- Pinia 全局状态库刚好解决全部痛点，是 Vue 项目做权限管理的标准方案。

## 

