# Vue3 script-setup 组件通信：defineProps /defineEmits 完整详解

## 一、基础说明

适用环境：Vue3 `<script setup>` 语法糖

特性：无需 import，编译器自动识别；**只能写在代码顶层，不能嵌套在函数、if、循环内**。

## 二、defineProps 父传子（接收外部参数）

### 1. 核心作用

1. 声明子组件允许接收哪些外部参数；
2. 提供类型校验、必填校验、默认值、自定义校验规则；
3. 接收父组件通过 `:属性名` 传递的数据。

### 2. 两种写法

#### 写法 1：数组简写（仅声明参数名，无校验）

子组件代码

```vue
<script setup>
// 声明接收 name、age 两个参数
const props = defineProps(['name', 'age'])
console.log(props.name, props.age)
</script>
```

父组件使用

```vue
<Child :name="张三" :age="18" />
```

#### 写法 2：对象完整配置（推荐，带校验）

```vue
<script setup>
const props = defineProps({
  // 基础类型限制
  title: String,
  // 必填参数
  id: {
    type: String,
    required: true
  },
  // 带默认值数字
  count: {
    type: Number,
    default: 0
  },
  // 对象/数组默认值必须用函数返回
  user: {
    type: Object,
    default: () => ({ name: '' })
  },
  // 自定义校验规则
  score: {
    type: Number,
    validator: (val) => val >= 0 && val <= 100
  }
})
</script>
```

### 3. 关键注意点

1. **单向数据流**：props 只读，子组件不能直接修改；如需更新数据，通过 `defineEmits` 通知父组件修改；
2. 模板内可直接使用属性，无需 `props.xxx`；

```vue
<template>
  <view>{{ title }} {{ count }}</view>
</template>
```

1. 解构响应式陷阱

```js
// ❌ 错误：直接解构丢失响应式
const { title } = defineProps(['title'])

// ✅ 正确：搭配 toRefs 保留响应式
import { toRefs } from 'vue'
const props = defineProps(['title'])
const { title } = toRefs(props)
```

## 三、defineEmits 子传父（派发自定义事件）

### 1. 核心作用

1. 声明子组件可触发的自定义事件；
2. 子组件触发事件并携带参数，父组件通过 `@事件名` 监听接收数据；
3. 配合 `update:xxx` 实现 `v-model:xxx` 双向绑定。

### 2. 两种写法

#### 写法 1：数组简写（仅声明事件名称）

```vue
<script setup>
const emit = defineEmits(['update:openFlag', 'update-total', 'confirm'])

// 触发事件并传参
emit('update:openFlag', false)
emit('update-total', 99)
</script>
```

父组件监听

```vue
<Child 
  @update:openFlag="val => showFlag = val"
  @update-total="total = $event"
/>
```

#### 写法 2：对象校验（Vue3.3+，校验事件参数类型）

```vue
const emit = defineEmits({
  // 无参数事件
  confirm: null,
  // 校验布尔参数
  'update:openFlag': (val) => typeof val === 'boolean',
  // 校验数字参数
  'update-total': (num) => typeof num === 'number'
})
```

### 3. 标准双向绑定规范 v-model:xxx

Vue 约定 `update:属性名` 事件，可简写 `v-model:属性名`

子组件代码

```js
const props = defineProps(['openFlag'])
const emit = defineEmits(['update:openFlag'])

// 关闭弹窗同步父状态
const close = () => {
  emit('update:openFlag', false)
}
```

父组件两种等价写法

```vue
<!-- 简写 -->
<CommentPopup v-model:open-flag="showCommentFlag" />

<!-- 完整展开 -->
<CommentPopup :open-flag="showCommentFlag" @update:openFlag="v => showCommentFlag = v" />
```

## 四、完整业务实战（评论弹窗场景）

### 子组件 CommentPopup.vue

```vue
<template>
  <view v-if="openFlag" class="popup">
    <text>评论总数：{{ total }}</text>
    <button @tap="closePopup">关闭弹窗</button>
    <button @tap="changeTotal">更新评论数量</button>
  </view>
</template>

<script setup>
// 接收父组件参数
const props = defineProps({
  openFlag: {
    type: Boolean,
    required: true
  },
  total: {
    type: Number,
    default: 0
  }
})

// 声明可派发事件
const emit = defineEmits(['update:openFlag', 'update-total'])

// 关闭弹窗，同步父开关状态
const closePopup = () => {
  emit('update:openFlag', false)
}

// 修改评论总数，传递给父组件
const changeTotal = () => {
  emit('update-total', props.total + 1)
}
</script>
```

### 父页面调用代码

```vue
<template>
  <view>
    <button @tap="flag = true">打开评论弹窗</button>
    <CommentPopup 
      v-model:open-flag="flag"
      :total="commentTotal"
      @update-total="commentTotal = $event"
    />
  </view>
</template>

<script setup>
import { ref } from 'vue'
const flag = ref(false)
const commentTotal = ref(0)
</script>
```

## 五、defineProps 与 defineEmits 核心对比

|     API     |    数据流向     |           核心作用           | 使用位置 |
| :---------: | :-------------: | :--------------------------: | :------: |
| defineProps | 父组件 → 子组件 | 接收外部传入数据，做参数校验 |  子组件  |
| defineEmits | 子组件 → 父组件 | 派发自定义事件，向父传递数据 |  子组件  |

## 六、拓展：Vue3.4+ defineModel 简化双向绑定

无需手动写 props + emit，一步完成双向绑定

```js
const openFlag = defineModel({
  type: Boolean,
  default: false
})
// 直接修改自动同步父组件状态
openFlag.value = false
```

## 七、通用限制注意事项

1. `defineProps` / `defineEmits` 仅能在 `<script setup>` 顶层执行，不能写在函数、判断、循环内部；
2. props 不可直接修改，必须通过 emit 通知父更新；
3. 直接解构 props 会丢失响应式，需搭配 `toRefs`；
4. 模板属性统一使用短横线命名 `open-flag`，JS 内使用驼峰 `openFlag`。