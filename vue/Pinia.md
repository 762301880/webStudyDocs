## 资料

| 名称       | 地址                                                    |
| ---------- | ------------------------------------------------------- |
| pinia 官网 | [link](https://pinia.vuejs.org/zh/getting-started.html) |



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