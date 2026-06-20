## 资料

| 名称                 | 地址                                                         |
| -------------------- | ------------------------------------------------------------ |
| elementui官网   vue2 | [vue2.x](https://element.eleme.cn/#/zh-CN)                   |
| element-plus    vue3 | [vue3.x](https://element-plus.org/zh-CN/)  [install](https://element-plus.org/zh-CN/guide/installation)  [看这个-组件](https://element-plus.org/zh-CN/component/form) |

## [安装-element-plus](https://element-plus.org/zh-CN/guide/installation)

### pnpm 安装

```bash
pnpm install element-plus
```

### 一、核心行为

1. **下载安装**

   从 npm 镜像拉取 `element-plus` 正式包，以及它**所有依赖**，存入项目 `node_modules`（pnpm 硬链接模式，占用体积很小）。

2. **修改配置文件**

   - `package.json`：新增 `"element-plus": "^版本号"` 到 **dependencies**（生产依赖，项目运行必需）
   - `pnpm-lock.yaml`：自动生成 / 更新锁文件，锁定所有包版本，保证团队安装一致性。

### 二、目录变化

- 项目根目录 `node_modules` 出现 `element-plus` 文件夹
- 可直接在代码里 **导入使用** 组件、样式、图标等

##  快速开始

### [按需导入element-plus](https://element-plus.org/zh-CN/guide/quickstart#%E6%8C%89%E9%9C%80%E5%AF%BC%E5%85%A5)

然后把下列代码插入到你的 `Vite` 或 `Webpack` 的配置文件中

#### vite（vite.config.ts）

> `vite.config.ts`

```shell
import { defineConfig } from 'vite'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

export default defineConfig({
  // ...
  plugins: [
    // ...
    AutoImport({
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      resolvers: [ElementPlusResolver()],
    }),
  ],
})
```

#### 按需导入含义解析

这是 **Vite + Element Plus 自动按需导入** 的配置，不用手动写 `import`，开箱即用组件和 API。

逐段解释

1. **依赖包作用**
   - `unplugin-auto-import/vite`：**自动导入函数 / API**（比如 `ElMessage`、`ElMessageBox`）
   - `unplugin-vue-components/vite`：**自动导入 Vue 组件**（比如 `<el-button>`、`<el-input>`）
   - `ElementPlusResolver`：专属解析器，告诉插件要识别 Element Plus 的内容
2. 配置含义

   ```js
AutoImport({
    resolvers: [ElementPlusResolver()],
})
   ```

自动识别 Element Plus 的**方法、全局 API**，代码里直接调用，不用手动 `import`。

```js
Components({
  resolvers: [ElementPlusResolver()],
})
```

模板里直接写 `<el-xxx>` 组件，插件自动帮你引入对应组件和样式。

**实际效果（对比传统写法）**

❶ 以前（手动引入）

```vue
<template>
  <el-button @click="showMsg">按钮</el-button>
</template>

<script setup>
// 必须手动导入
import { ElButton, ElMessage } from 'element-plus'
const showMsg = () => ElMessage.success('成功')
</script>
```

❷ 配完上面代码后（**零导入**）

直接写就行，完全不用 `import`：

```vue
<template>
  <el-button @click="showMsg">按钮</el-button>
</template>

<script setup>
const showMsg = () => ElMessage.success('成功')
</script>
```

#### 额外优点

1. **按需加载**：只打包你用到的组件 / 样式，减小打包体积
2. **简化代码**：省去重复的导入语句
3. 配合 Vite 热更新，开发体验更好

## [国际化中文配置](https://element-plus.org/zh-CN/guide/i18n)

> 场景[paginate](https://element-plus.org/zh-CN/component/pagination) 分页配置会导致英文展示很不友好

### 配置方案一 `App.vue`中全局注册中文配置

```vue
<template>
  <el-config-provider :locale="zhCn">
    <router-view />
  </el-config-provider>
</template>

<script setup>
import zhCn from 'element-plus/es/locale/lang/zh-cn'
</script>
```