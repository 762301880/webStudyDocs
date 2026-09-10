## scss中文官网

| 名称         | 地址                                   |
| ------------ | -------------------------------------- |
| scss中文官网 | [link](https://sass.bootcss.com/guide) |

## SCSS 是什么

SCSS 是 **Sass 的语法之一**（Sass 有两种语法：`.sass` 缩进语法、`.scss` 大括号语法）

> SCSS 完全兼容原生 CSS 语法，后缀名 `.scss`，可以直接写 css，也可以使用增强特性。

## 核心特性

1. **变量**：统一管理颜色、字号、间距
2. **嵌套**：层级嵌套选择器，结构和 HTML 对应
3. **混合器 `@mixin`**：可复用样式片段，支持传参
4. **继承 `@extend`**：复用选择器样式
5. **导入 `@import`**：拆分多个 scss 文件
6. **运算**：支持加减乘除颜色、尺寸计算
7. **函数**：自定义函数处理样式

> ⚠️ SCSS 不能直接在浏览器运行，**需要编译成普通 CSS**（工具：Vite/Webpack、sass 命令、gulp 等）

## 完整 SCSS 示例

新建文件 `style.scss`

```css
// ====================== SCSS 变量定义 ======================
// 变量使用 $ 开头，用于统一维护样式，改一处全局生效
$primary-color: #2563eb;    // 主题主色
$secondary-color: #64748b;  // 次要文字颜色
$white: #ffffff;
$base-font-size: 16px;      // 基础字号
$base-spacing: 8px;         // 基础间距单位
$border-radius: 6px;        // 圆角

// ====================== @mixin 混合器（支持传参） ======================
// 封装可复用样式块，可以接收参数，类似函数
@mixin flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

// 带参数的混合器，参数可设置默认值
@mixin card-shadow($shadow-color: rgba(0,0,0,0.1)) {
  box-shadow: 0 2px 8px $shadow-color;
}

// ====================== @extend 继承 ======================
// 公共基础样式，可供其他选择器继承
%base-btn {
  padding: $base-spacing $base-spacing * 2;
  border-radius: $border-radius;
  border: none;
  cursor: pointer;
  font-size: $base-font-size;
}

// ====================== 样式嵌套（核心特性） ======================
.container {
  width: 1200px;
  margin: 0 auto;
  padding: $base-spacing * 2;

  // 嵌套子选择器，编译后变成 .container .box
  .box {
    width: 300px;
    height: 200px;
    // 使用混合器
    @include flex-center;
    @include card-shadow();
    background: $white;
    border-radius: $border-radius * 2;

    // & 代表父选择器本身，常用于 hover、伪类
    &:hover {
      background-color: #f8fafc;
    }

    // 伪元素
    &::before {
      content: "";
      display: block;
      width: 40px;
      height: 4px;
      background: $primary-color;
    }
  }
}

// ====================== 按钮：使用继承 @extend ======================
.btn {
  // 继承 %base-btn 的所有样式
  @extend %base-btn;

  &-primary {
    background-color: $primary-color;
    color: $white;
    &:hover {
      background-color: darken($primary-color, 10%); // sass内置函数，加深颜色
    }
  }

  &-default {
    background-color: $secondary-color;
    color: $white;
  }
}

// ====================== 运算示例 ======================
.calc-demo {
  width: 100px + 50px;        // 150px
  height: $base-spacing * 3;  // 8px *3 = 24px
}

// ====================== 自定义函数 ======================
// 自定义函数，计算 rem（假设基准16px）
@function px2rem($px) {
  @return $px / $base-font-size * 1rem;
}

.rem-demo {
  font-size: px2rem(24px); // 输出 1.5rem
}
```

### 编译后对应的 CSS（方便对照理解）

> 执行编译命令：`sass style.scss style.css`

```css
/* 编译后 css，SCSS 变量、mixin、嵌套都会被展开 */
.container {
  width: 1200px;
  margin: 0 auto;
  padding: 16px;
}
.container .box {
  width: 300px;
  height: 200px;
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  background: #ffffff;
  border-radius: 12px;
}
.container .box:hover {
  background-color: #f8fafc;
}
.container .box::before {
  content: "";
  display: block;
  width: 40px;
  height: 4px;
  background: #2563eb;
}
.calc-demo {
  width: 150px;
  height: 24px;
}
.rem-demo {
  font-size: 1.5rem;
}
.btn, .btn-primary, .btn-default {
  padding: 8px 16px;
  border-radius: 6px;
  border: none;
  cursor: pointer;
  font-size: 16px;
}
.btn-primary {
  background-color: #2563eb;
  color: #ffffff;
}
.btn-primary:hover {
  background-color: #1d4ed8;
}
.btn-default {
  background-color: #64748b;
  color: #ffffff;
}
```

## 在 Vue 项目使用 SCSS（Vite）

### 1. 安装依赖

```bash
npm install sass -D
```

### 2. Vue 文件使用

```vue
<template>
  <div class="container">
    <div class="box">SCSS Demo</div>
    <button class="btn btn-primary">按钮</button>
  </div>
</template>

<style lang="scss">
// 可以直接写上面的 scss 代码
$primary-color: #2563eb;
.container {
  padding: 16px;
  .box {
    color: $primary-color;
  }
}
</style>
```

