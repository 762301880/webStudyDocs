## 安装pinia

**vue3**

```bash
# npm
npm install pinia
# yarn
yarn add pinia
# pnpm
pnpm add pinia
```

## 项目目录结构

```bas
src/
├── store/
│   └── user.js     # 用户仓库示例
├── main.js         # 全局注册pinia
└── pages/
    └── index/index.vue  # 使用页面
```

### store/user.js（定义仓库，无任何接口）

```js
import { defineStore } from 'pinia'

// 唯一id：user
export const useUserStore = defineStore('user', {
  // 状态，相当于vuex state
  state: () => ({
    name: '小明',
    age: 18,
    token: '',
    list: [1, 2, 3]
  }),

  // 计算属性，相当于vuex getters
  getters: {
    // 拼接姓名年龄
    userInfo: (state) => {
      return `${state.name}，今年${state.age}岁`
    },
    listTotal: (state) => state.list.length
  },

  // 修改状态，同步/异步都放这里，代替mutations+actions
  actions: {
    // 同步修改
    setName(name) {
      this.name = name
    },
    addAge() {
      this.age++
    },
    // 批量修改state
    setUserInfo(obj) {
      this.$patch(obj)
    },
    // 模拟异步（不用请求接口，单纯延时）
    async asyncChangeName(newName) {
      // 模拟等待1秒
      await new Promise(resolve => setTimeout(resolve, 1000))
      this.name = newName
    },
    clearUser() {
      // 重置state到初始值
      this.$reset()
    }
  }
})
```

### main.js 全局挂载 pinia

```js
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'

const pinia = createPinia()
const app = createApp(App)

app.use(pinia)
app.mount('#app')
```

### 页面 pages/index/index.vue 使用示例

```vue
<template>
  <view class="content">
    <view>姓名：{{ userStore.name }}</view>
    <view>年龄：{{ userStore.age }}</view>
    <view>用户信息(getter)：{{ userStore.userInfo }}</view>
    <view>数组长度：{{ userStore.listTotal }}</view>

    <button @click="userStore.addAge">年龄+1</button>
    <button @click="userStore.setName('小红')">改名字为小红</button>
    <button @click="userStore.setUserInfo({name:'小李',age:22})">批量修改</button>
    <button @click="userStore.asyncChangeName('异步改名')">1秒后异步改名</button>
    <button @click="userStore.clearUser">重置所有数据</button>

    <!-- 直接修改state（不推荐，简单演示可用） -->
    <button @click="userStore.age = 30">直接修改age</button>
  </view>
</template>

<script setup>
// 引入仓库
import { useUserStore } from '@/store/user'
// 获取仓库实例
const userStore = useUserStore()
</script>
```

## 核心知识点（极简总结）

1. **state**：存放所有响应式数据
2. **getters**：派生数据，计算属性
3. **actions**：统一修改数据，支持同步 / 异步，不需要区分 mutation
4. `$patch`：批量更新多个 state
5. `$reset()`：一键恢复 state 初始值
6. 无需 mutations，比 Vuex 简洁很多
7. 全程无接口、无请求，纯本地状态管理

## 拓展：持久化（可选，页面刷新不丢失）

如需本地存储，安装插件：

```bash
pnpm add pinia-plugin-persistedstate
```

修改 `store/index.js`

```js
import { createPinia } from 'pinia'
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate'
const pinia = createPinia()
pinia.use(piniaPluginPersistedstate)
export default pinia
```

仓库开启持久化，在 `user.js` 末尾添加：

```js
export const useUserStore = defineStore('user', {
  // ...state/getters/actions
  persist: true // 开启持久化
})
```







