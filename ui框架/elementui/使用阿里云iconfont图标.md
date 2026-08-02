# Vue3 + ElementPlus 侧边栏兼容阿里图标库完整集成文档

## 一、整体实现目标

1. 菜单同时兼容 **ElementPlus 内置图标**、**阿里 iconfont 字体图标** 两套图标渲染逻辑
2. 阿里图标保留自定义尺寸、颜色、外边距样式，不向下继承文字颜色干扰子菜单
3. 侧边栏一级菜单、二级子菜单渲染逻辑区分，样式互不污染
4. 菜单折叠状态图标自适应展示

## 二、前期准备：阿里图标库接入项目

### 2.1 生成并获取图标资源链接

1. 登录阿里图标库官网，新建项目，将所需图标加入购物车并添加至项目
2. 项目设置：
   - 字体格式：勾选 `Unicode`、`Font class`
   - 前缀默认：`icon-`（无需修改）
3. 点击【查看在线链接】，复制 `Font class` 格式的 CSS 地址

### 2.2 全局引入阿里图标样式

在项目根目录 `public/index.html` 文件头部引入样式链接：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <link rel="stylesheet" href="https://xxx.alicdn.com/t/font_xxxxxxx_xxxxxx.css">
</head>
<body>
  <div id="app"></div>
</body>
</html>
```

> 多色 SVG 图标需引入 JS 格式 Symbol 链接，单色字体图标仅引入 CSS 即可

## 三、路由配置规范（区分两套图标写法）

### 3.1 路由 meta 字段约定

|  字段   |    作用    |                           填写规范                           |
| :-----: | :--------: | :----------------------------------------------------------: |
|  icon   |  图标标识  | Element 图标：直接填写图标英文名（如`Menu`）阿里图标：完整 class 类名 `iconfont icon-xxx` |
|  style  | 自定义样式 | 仅阿里图标使用，书写行内样式字符串，用于控制颜色、字号、间距 |
| permApi |  权限接口  |        后端鉴权标识，和图标逻辑无关，保留原有业务逻辑        |

### 3.2 路由示例代码

```js
const routes = [
  {
    path: '/',
    component: () => import('@/views/index/Index.vue'),
    redirect: '/userManage/list',
    children: [
      // ElementPlus内置图标写法
      {
        path: 'home',
        meta: {
          title: '首页控制台',
          icon: 'Menu'
        }
      },
      // 阿里字体图标写法（一级父菜单）
      {
        path: "vue3_demo",
        component:() =>import('@/views/layout/SubLayout.vue'),
        meta: {
            title: 'vue3案例',
            icon: 'iconfont icon-vue3',
            style:"margin-right: 8px;color: #42b883;font-size: 20px"
        },
        children: [
          // 子菜单统一使用Element图标，不受父级颜色继承影响
          {
            path: "Computed",
            meta: {
                title: 'Computed计算属性',
                icon: 'Box'
            }
          }
        ]
      }
    ]
  }
]
```

> 注意：父菜单不要将文字颜色写入 style，会被子菜单继承；颜色仅作用于图标最优

## 四、侧边栏菜单模板渲染逻辑（核心兼容代码）

### 4.1 完整菜单模板代码

```vue
<template>
  <el-container style="height: 100vh">
    <!-- 左侧侧边栏 -->
    <el-aside
        :width="isCollapse ? '64px' : '220px'"
        style="background:#272e3b; transition: width 0.3s ease;"
    >
      <el-menu
          :default-active="route.path"
          background-color="#272e3b"
          text-color="#fff"
          active-text-color="#409eff"
          router
          :collapse="isCollapse"
          collapse-transition
          style="border-right: none;"
      >
        <template v-for="item in filterMenuList" :key="item.path">
          <!-- 一级菜单：无子菜单 -->
          <el-menu-item v-if="!item.children" :index="'/' + item.path">
            <!-- 判断：包含iconfont前缀 = 阿里字体图标 -->
            <template v-if="item.meta?.icon?.includes('iconfont')">
              <i :class="item.meta.icon" :style="item.meta.style"></i>
            </template>
            <!-- 否则为ElementPlus内置图标 -->
            <template v-else>
              <el-icon :style="item.meta.style">
                <component :is="iconMap[item.meta.icon]" />
              </el-icon>
            </template>
            <span>{{ item.meta?.title }}</span>
          </el-menu-item>

          <!-- 一级父菜单：包含二级子菜单 -->
          <el-sub-menu v-else :index="item.path">
            <template #title>
              <template v-if="item.meta?.icon?.includes('iconfont')">
                <i :class="item.meta.icon" :style="item.meta.style"></i>
              </template>
              <template v-else>
                <el-icon :style="item.meta.style">
                  <component :is="iconMap[item.meta.icon]" />
                </el-icon>
              </template>
              <span>{{ item.meta?.title }}</span>
            </template>

            <!-- 二级子菜单：仅支持Element图标，不读取父级自定义样式 -->
            <el-menu-item
                v-for="child in item.children"
                :key="child.path"
                :index="'/' + item.path + '/' + child.path"
            >
              <el-icon>
                <component :is="iconMap[child.meta?.icon]" />
              </el-icon>
              <span>{{ child.meta?.title }}</span>
            </el-menu-item>
          </el-sub-menu>
        </template>
      </el-menu>
    </el-aside>

    <!-- 右侧头部+主体区域省略，保留原有业务代码 -->
  </el-container>
</template>
```

### 4.2 script 图标引入基础配置

```vue
<script setup>
import {ref, computed, watch, onMounted} from 'vue'
import {useRoute, useRouter} from 'vue-router'
// 全局导入ElementPlus所有内置图标
import * as ElementIcons from '@element-plus/icons-vue'

// 图标映射对象，用于动态渲染Element图标
const iconMap = ElementIcons
const route = useRoute()
const router = useRouter()
const isCollapse = ref(false) // 菜单折叠状态

// 菜单权限过滤、面包屑、Tabs逻辑保留原有代码不变
</script>
```

## 五、样式处理：解决颜色继承、图标间隙问题

### 5.1 核心样式穿透代码（style scoped 内编写）

```css
<style scoped>
/* 穿透scoped，修改Element内置菜单默认间距 */
:deep(.el-menu-item__icon) {
  margin-right: 8px !important;
}
:deep(.el-sub-menu__icon) {
  margin-right: 8px !important;
}

/* 强制二级子菜单文字不继承父菜单颜色，统一默认白色 */
:deep(.el-sub-menu .el-menu-item) {
  color: #ffffff !important;
}
/* 子菜单激活态文字颜色保持主题蓝色 */
:deep(.el-sub-menu .el-menu-item.is-active) {
  color: #409eff !important;
}

/* 原有用户信息、tabs样式省略 */
</style>
```

### 5.2 样式参数说明

1. `margin-right: 8px`：图标与右侧文字标准间距，可按需调整 6px~12px
2. 子菜单强制白色文字：阻断父级阿里图标自定义颜色向下继承
3. 激活态配色：贴合 ElementPlus 默认主题色，视觉统一

## 六、多色 SVG 图标保留原色方案（拓展）

字体图标仅支持单色，若需要图标自带多色（Vue 彩色 logo、卡通图标），使用 **Symbol SVG 模式**：

1. index.html 引入 JS 资源：

```html
<script src="https://xxx.alicdn.com/t/font_xxxxxxx_xxxxxx.js"></script>
```

1. 封装通用 SVG 图标组件

```vue
<!-- components/SvgIcon.vue -->
<template>
  <svg class="svg-icon" aria-hidden="true">
    <use :xlink:href="iconName"></use>
  </svg>
</template>
<script setup>
defineProps(['iconName'])
</script>
<style scoped>
.svg-icon {
  width: 1.2em;
  height: 1.2em;
  vertical-align: middle;
}
</style>
```

1. 菜单中调用：

```vue
<SvgIcon icon-name="#icon-pinia" />
```

> 优势：完全保留 SVG 原始配色，不受 color 属性覆盖

## 七、常见问题排查

### 7.1 阿里图标不显示

1. index.html CSS 链接地址有效，无 404 报错
2. class 类名和阿里图标库内名称完全一致，前缀必须携带 `iconfont`

### 7.2 图标颜色被覆盖

1. 行内 style 添加 `color: xxx !important` 强制优先级
2. 多色图标切换 Symbol SVG 引入方式

### 7.3 子菜单文字变色

检查是否父路由 meta.style 写入了 color 属性，移除父菜单文字颜色配置即可