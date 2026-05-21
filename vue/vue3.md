## [vue3文档](https://cn.vuejs.org/guide/essentials/application.html)

##  [创建一个vue3项目](https://cn.vuejs.org/guide/quick-start.html)(不推荐)

```shell
PS D:\Desktop> npm create vue@latest

> npx
> create-vue

┌  Vue.js - The Progressive JavaScript Framework
│
◇  请输入项目名称：
│  vue_study
│
◇  是否使用 TypeScript 语法？
│  No
│
◇  请选择要包含的功能： (↑/↓ 切换，空格选择，a 全选，回车确认)
│  none
│
◇  选择要包含的试验特性： (↑/↓ 切换，空格选择，a 全选，回车确认)
│  none
│
◇  跳过所有示例代码，创建一个空白的 Vue 项目？
│  No

正在初始化项目 D:\Desktop\vue_study...
│
└  项目初始化完成，可执行以下命令：

   cd vue_study
   npm install
   npm run dev

| 可选：使用以下命令在项目目录中初始化 Git：

   git init && git add -A && git commit -m "initial commit"
```

##  运行项目(开发环境)

```shell
   npm install  # 安装依赖
   
   npm run dev  # 运行开发环境
```

###  命令解析

1. `npm install`

**意思：安装项目需要的依赖包**

你可以把它理解成：

**玩游戏前，先把游戏需要的所有插件、素材都下载安装好。**

- 它会读取项目里的 `package.json` 文件
- 自动下载所有需要的第三方库（比如 Vue、axios、ElementUI 等）
- 安装完会生成一个 `node_modules` 文件夹（所有依赖都在这里）

简写：

```shell
npm i
```

2.  `npm run dev`

**意思：启动开发环境，让项目跑起来**

相当于：

**安装好游戏后，点击 “开始运行”**

- 它会启动一个本地服务器
- 你可以在浏览器里打开项目看到页面
- 代码修改后页面会自动刷新（热更新）
- 只用于**开发时调试**，不是上线用的

**最通俗的总结**

1. **`npm install` = 安装依赖**

   第一次下载项目必须先运行，不然跑不起来。

   

2. **`npm run dev` = 启动项目**

   安装完依赖后，运行它就能在本地打开网页开发了。

## 运行项目线上环境(打包部署)

### 线上环境只需要做 3 步

#### 第 1 步：打包项目（生成上线文件）

在项目里执行：

```shell
npm run build
```

执行完会生成一个文件夹，通常叫：

- `dist`
- 或者 `build`
- 或者 `output`

这个文件夹里就是**能上线的所有代码**。

#### 第 2 步：把 dist 文件夹放到线上服务器

有很多选择，最简单的 3 种：

1. **GitHub Pages（免费，最推荐新手）**
2. **Gitee Pages（国内免费）**
3. **自己买的服务器 / 宝塔面板**
4. **Netlify / Vercel（国外免费）**

**核心：只需要上传 dist 文件夹即可。**

#### 第 3 步：配置访问（不用写代码）

上传完 dist，别人就能通过网址访问你的网站了。

### 补充

#### 本地环境非服务器打开打包后的项目dist/index.html是空白的

浏览器**禁止直接用本地文件（file://）** 运行前端项目，必须用 **服务器（http://）** 打开。

现在的方式 = 违规，所以直接空白、报错。

#####  正确解决方法（2 选 1，超级简单）

方法 1：最推荐 → 用「本地预览服务器」打开（10 秒搞定）

你需要装一个**小工具**，让 dist 文件夹变成可访问的网页：

1. 先在终端运行这个（只装一次）：

```bash
npm install -g serve
```

1. 进入你的 `dist` 文件夹，运行：

```bash
serve
```

1. 它会给你一个地址，比如：

```bash
http://localhost:3000
```

打开就能看到页面，**不再空白、不再报错**！

方法 2：如果你要「真正上线给别人看」

1. 把 

   ```
   dist
   ```

    文件夹上传到：

   - GitHub Pages
   - Netlify
   - 你的服务器

   

2. 用** 网址（http/https）**访问

   

   就完全正常了！

**结论（一定要记住）**

❌ **错误**：双击 `dist/index.html` → 白屏 + CORS 报错

✅ **正确**：用服务器打开（serve / 线上网址）→ 正常显示

## [项目结构](https://www.runoob.com/vue3/vue3-project-intro.html)

一个 Vue 3 项目通常包含以下文件和文件夹：

```shell
my-vue-app/
├── node_modules/       # 项目依赖的第三方库
├── public/             # 静态资源文件夹
│   ├── index.html      # 应用的 HTML 模板
│   └── ...             # 其他静态资源（如图片、字体等）
├── src/                # 项目源代码
│   ├── assets/         # 静态资源（如图片、字体等）
│   ├── components/     # 可复用的 Vue 组件
│   ├── views/          # 页面级组件
│   ├── App.vue         # 根组件
│   ├── main.js         # 项目入口文件
│   ├── router.js       # 路由配置
│   ├── store.js        # Vuex 状态管理配置
│   └── ...             # 其他配置和资源
├── package.json        # 项目配置和依赖管理
├── package-lock.json   # 依赖的精确版本锁定文件
└── README.md           # 项目说明文档
```







##  疑问补充

###  npm create  vue@latest   或者  vite@latest 区别

一句话：

- `npm create vue@latest`：**Vue 官方专用脚手架**，只做 Vue，帮你把 Router/Pinia/ESLint 等都配置好。
- `npm create vite@latest`：**Vite 通用脚手架**，可建 Vue/React/ 原生 JS 等，Vue 模板是最简裸配置。

#### 本质不同：谁提供、干什么

##### npm create vue@latest(不推荐)

- 实际调用：**create-vue**（Vue 官方包）
- 定位：**只生成 Vue 3 项目**的专用脚手架
- 底层：**内部就是基于 Vite**，不用你再选构建工具

运行后会交互式问你：

- 项目名
- 是否用 TypeScript
- 是否加 JSX
- 是否加 Vue Router
- 是否加 Pinia（状态管理）
- 是否加 ESLint + Prettier
- 是否加单元测试等

👉 结果：**开箱即用、配置完整的 Vue 3 工程**，企业级规范直接给你配好。

##### npm create vite@latest(推荐)

- 实际调用：**create-vite**（Vite 官方包）

- 定位：

  通用前端脚手架

  ，可生成：

  - 原生 JS（vanilla）
  - Vue / Vue-ts
  - React / React-ts
  - Preact、Svelte 等

- 底层：**Vite 本身**

要手动选模板：

```shell
✔ Select a framework: › Vue
✔ Select a variant: › JavaScript / TypeScript
```

👉 结果：**最精简的 Vue + Vite 裸项目**，只有：

- vite.config.ts

- App.vue、main.ts

- 基本 index.html

  

  没有 Router、没有 Pinia、没有 ESLint，全都要自己后续安装配置。

####  生成的项目差异（Vue 场景对比）

##### 用 `create-vue`（vue@latest）

- ✅ 内置 Vite（不用自己配）
- ✅ 可选：Router + Pinia 自动配置好
- ✅ 可选：ESLint + Prettier 配置好
- ✅ 可选：单元测试（Vitest）配置好
- ✅ tsconfig、env 等都按 Vue 最佳实践配置好
- 适合：**正式项目、中大型项目、不想折腾配置**

##### 用 `create-vite`（vite@latest → 选 Vue）

- ✅ 内置 Vite
- ❌ **无 Router、无 Pinia**，需手动 `npm install vue-router pinia` 并配置
- ❌ **无 ESLint/Prettier**，需自己装插件、写配置
- ❌ 无测试框架
- 配置极少，自由度高
- 适合：**练手、小 Demo、想从零搭 Vue + Vite**

#### 怎么选（简单结论）

- 要正经写 Vue 项目、想少配多写：

  ```bash
  npm create vue@latest
  ```

（官方推荐，Vue 专属，配置齐全）

要**快速搭一个极简 Vue demo / 或想自己全套配置**：

```bash
npm create vite@latest
# 然后选 Vue
```

你要**用 React / Svelte / 原生 JS**：

只能用 `create-vite`。

####  记忆口诀

- `create vue` = **Vue 全家桶，开箱即用**
- `create vite` = **通用裸模板，自己拼装**