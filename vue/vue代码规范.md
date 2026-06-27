## 资料

| 名称         | 地址                                                         |
| ------------ | ------------------------------------------------------------ |
| 官方规范参考 | [link](https://v2.cn.vuejs.org/v2/style-guide/)  [link](https://www.trae.cn/article/3133480706) |
| 网络博客     | [link](https://docs.jeremyjone.com/standard/vue/#_3-vue-%E6%8C%87%E4%BB%A4%E7%9A%84%E4%BD%BF%E7%94%A8)  [link](https://hntsz.github.io/docs/FRONTEND_VUE3_GUIDELINES.html) |
|              |                                                              |

## 规范记录

### 一，基础开发环境

- **构建工具**：统一使用 [Vite](https://vitejs.dev/) 构建项目，提升开发启动速度和热更新效率。
- **项目类型**：单页应用（SPA）。
- **包管理器**：可按项目选择 `pnpm` / `yarn` / `npm`，但应锁定锁文件版本，避免跨环境依赖变更问题。
- **Node版本管理**：使用MVN实现无缝切换，优雅解决多项目Node版本冲突。

### 二，项目目录结构（推荐）

```bash
src/
├── assets/           # 静态资源（图片、字体等）
├── components/       # 全局/通用组件
├── views/            # 页面组件
├── layout/           # 布局组件
├── router/           # 路由配置
├── store/            # 状态管理（视项目使用）
├── api/              # API 封装模块
├── types/            # 全局类型定义
├── utils/            # 通用工具方法
├── directives/       # 自定义指令（可选）
├── hooks/            # 封装的逻辑函数（composables）
|—— plugins/          # Vue插件注入管理
```

说明：

- 每个页面对应一个独立的 `views/模块`，下设子页面与本地组件。
- 通用组件放入 `components/`，保持复用性。

### 三,组件与代码风格

#### 使用规范

- **必须使用 `<script setup>`** 语法；
- 每个组件最外层只能有一个根标签
- 每个组件文件结构推荐如下顺序：

```vue
<script setup lang="ts">
  ...
</script>

<template>
  <div>
    ...
  </div>
</template>

<style scoped>
  ...
</style>
```

#### 命名规范

| 类型   | 命名示例        | 说明                               |
| ------ | --------------- | ---------------------------------- |
| 组件名 | `UserCard`      | 大驼峰，尽量名词化                 |
| 变量名 | `userList`      | 小驼峰                             |
| 方法名 | `fetchUserList` | 动宾结构，小驼峰                   |
| 文件名 | `user-card.vue` | 推荐使用中划线风格（ kebab-case ） |

------

### 四、样式规范

- 使用 `scoped` 样式隔离组件作用域；

- 具体样式方案视项目选择：

  - SCSS（推荐在大中型项目中使用）

  - | UnoCss | Tailwind CSS（如需快速构建响应式界面） |
    | ------ | -------------------------------------- |
    |        |                                        |

  - CSS Modules（用于组件级样式隔离）

建议在团队层面保持统一风格：

```
.btn {
  @apply text-white bg-primary px-4 py-2 rounded;
}
```

------

### 五、UI 框架使用规范（Element Plus）

- 推荐使用 [Element Plus 按需引入方式](https://element-plus.org/zh-CN/guide/quickstart.html#on-demand-import)；
- 组件封装建议建立基础组件层（BaseInput.vue、BaseTable.vue 等）；
- 禁止直接使用内联样式；
- 表单校验建议使用 `el-form` 配合 YUP 等库进行扩展校验。

------

### 六、代码校验与格式化

- **ESLint + Prettier** 为团队默认配置工具；
- 推荐 ESLint 规则：
  - `eslint:recommended`
  - `@typescript-eslint/recommended`
  - `plugin:vue/vue3-recommended`
- 配置 `.eslintrc.js` 和 `.prettierrc` 文件统一格式；
- 可结合 [husky + lint-staged](https://typicode.github.io/husky/#/) 实现 commit 前自动校验。

------

### 七、接口请求（视项目定制）

推荐封装统一请求工具：

```js
// api/http.ts
const service = axios.create({
  baseURL: import.meta.env.VITE_API_BASE,
  timeout: 10000,
});

// 添加请求/响应拦截器
service.interceptors.response.use(
  res => res.data,
  err => {
    ElMessage.error(err.message);
    return Promise.reject(err);
  }
);

export default service;
```

------

### 八、类型定义与约定

- 必须使用 TypeScript 类型系统；
- 所有 API 接口数据结构应定义在 `types/` 中；
- 所有组件 props 必须显式声明类型；
- 推荐使用 `defineProps<T>()`、`withDefaults()` 等方式增强类型安全性。

------

### 九、组件复用与抽象

- 避免组件功能堆叠；超过 300 行建议拆分；
- 公共逻辑应提取为 `hooks/useXXX.ts` 或 `utils/xxx.ts`；
- 所有事件、状态、接口数据的含义必须具备可读性，禁止缩写。

------

### 十、代码规范

- 变量命名统一使用小驼峰

  ```
  // bad
  const user_name = ''
  
  // good
  const userName = ''
  ```

- 响应式声明填写默认值或者ts类型

  ```
  // bad
  const userName = ref()
  
  // good
  const userName = ref('')
  // good
  const userName = ref<string>()
  ```

- 开发完成的功能删掉多余注释和日志

- 尽量使用 ES6+ 语法

  ```
  // bad
  const handleCheckboxChange = (item, index) => {
    for (let i = 0; i < checked.value.length; i++) {
      if (i !== index) {
        checked.value[i] = false;
      }
    }
    productPackage.value = item;
  };
  
  // good
  const handleCheckboxChange = (item, index) => {
    checked.value = checked.value.map((_, i) => i === index);
    productPackage.value = item;
  };
  ```

- 函数/变量 使用多行注释进行提示

  ```
  //  bad
  // 统计输入的字数
  const handleInput = () => {};
  
  // good
  /** 统计输入的字数  */
  const handleInput = () => {};
  ```

- 使用Async+Await 解决Promise 回调

  ```
  // bad
  const getProductPackage = () => {
    return getProductPackageList({ productId: id }).then((res) => {
      productPackageList.value = res;
    });
  };
  
  // good
  const getProductPackage = async () => {
     productPackageList.value = await getProductPackageList({ productId: id })
  };
  ```

- 使用表达式和运算符

  ```
  // bad
  <td v-if="item.useCount !== null"></td>
  <td v-else>不限查询次数</td>
  
  // good
  <td></td>
  // good
  <td></td>
  ```

- 使用 reactive 代理复杂数据类型 (待定)

  ```
  // bad
  const user = ref({
    name: 'xxx',
    age: 16
  })
  
  // good
  const user = reactive({
    name: 'xxx',
    age: 16
  })
  ```

- 使用 reactive 管理弹窗状态

  ```
  // bad
  const createAppDialogVisiable = ref(false);
  const appDeatailDialogVisiable = ref(false);
  const dialogVisiable = ref(false);
  const resertDialogVisible = ref(false);
  
  // good
  const dialogVisible = reactive({
    createApp: false,
    appDeatail: false,
    dialog: false,
    resert: false,
  })
  ```

------

### 十一、路由规范

- url路径与页面路径对应

  ```
  // bad
  {
    path: '/dashboard/product/manage',
    name: 'ProductOrder',
    component: () => import('@/views/dashboard/product/index.vue'),
  },
  
  // good
  {
    path: '/dashboard/product/manage',
    name: 'ProductManage',
    component: () => import('@/views/dashboard/product/manage/index.vue'),
  },
  // good
  {
    path: '/dashboard/product/manage/index',
    name: 'ProductManage',
    component: () => import('@/views/dashboard/product/manage/index.vue'),
  },
  ```

- 使用props接收路由参数

  [友情链接](https://router.vuejs.org/zh/guide/essentials/passing-props.html#将-props-传递给路由组件)

  ```
  // bad 无法知道 params 里包含的参数
  const route = useRoute();
  const id = route.params.id;
  
  // good 明确当前页面参数依赖
  const props = defineProps<{
    id: string
  }>()
  ```

------

### 十二、注释标记

**TODO** 代码中尚未完成的部分或者开发者计划在未来添加或修改的功能。

**NOTE** 用于提供额外的信息或解释某些决策的原因。

**HACK** 标记那些可能不是最优解，但为了特定目的暂时采用的解决方案。这种标记通常用来指出代码中可能存在的临时性或非标准做法。

**XXX** 通常表示不确定或有问题的地方，可能需要进一步检查或修改。

**INFO** 类似于”NOTE”，用于提供信息性的备注。

**WARNING** 指出潜在的问题或需要特别注意的地方

**REVIEW** 表示该段代码需要经过复审，尤其是在代码合并之前。

**OPTIMIZE** 标记那些可以进行优化的部分。

#### 案例

❌ 错误的注释标记：

```
// TODO 这里需要优化一下
```

✅ 正确的注释标记：

```
/**
 * TODO: 这里需要优化一下，当数据量大时可能会有性能问题
 */
```

[js表达式和运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators)

------

### 十三、其他建议

- 不允许使用 `any`，除非有必要；
- 禁止操作 DOM，请使用 Vue Ref、组件事件机制；
- 避免在模板中书写复杂逻辑，使用 computed / method 处理；
- 禁止硬编码（如提示语、接口地址），建议抽出配置文件；