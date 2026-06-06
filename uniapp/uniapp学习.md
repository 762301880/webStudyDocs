## 资料

| 名称     | 地址                                                    |
| -------- | ------------------------------------------------------- |
| 官方文档 | [link](https://uniapp.dcloud.net.cn/quickstart-hx.html) |

##  目录结构

```bash
uni-app-study/
├── pages/                 # 页面目录
│   └── index/
│       └── index.vue      # 首页
├── App.vue                # 应用根组件（全局生命周期）
├── main.js                # 入口文件
├── pages.json             # 页面路由与窗口样式
├── manifest.json          # 应用配置（多端、打包、权限等）
├── index.html             # H5 入口（主要给 Web 端用）
├── uni.scss               # 内置样式变量（可全局引用）
├── uni.promisify.adaptor.js  # 将 uni API Promise 化（Vue2 时常用）
├── package.json           # npm 依赖（你加了 hls.js）
├── package-lock.json
├── .gitignore
└── git_push.sh            # 自定义脚本（非 uni-app 标准）
```

## **.gitignore ** 忽略不需要的文件

> `.gitignore ` 

```bash
node_modules/
unpackage/
.hbuilderx/
.DS_Store
```

###  node_modules/

**作用：** 存放项目所有**npm 依赖包**（vue、webpack、uni-app 插件、hls.js 等都在这里）。

**特点：**

- 体积超级大（几十 MB ~ 几百 MB）
- 可以通过 `package.json` 重新生成
- **不需要提交到 Git**

### unpackage/

**uni-app 专属编译产物文件夹**

**作用：**

- 存放打包后的小程序、App、H5 代码
- 存放调试、发行、自定义基座文件
- 每次编译都会自动生成 / 覆盖

**为什么放 .gitignore：**

自动生成的文件，不需要提交，提交也没用，还会冲突。

### .hbuilderx/

**HBuilderX 编辑器的配置文件夹**

**作用：**

- 保存你项目的编辑器设置
- 保存运行配置、插件配置、断点等
- 只对你本地电脑有效

**为什么放 .gitignore：**

每个人编辑器习惯不同，不需要共享。

###  .DS_Store

**Mac 电脑系统自动生成的隐藏文件**

**作用：**

存储文件夹的显示属性（图标位置、背景、视图信息等）。

**Windows 没有这个文件。**

### 总结（超级好记）

- **node_modules/**：依赖包，可重建
- **unpackage/**：编译产物，自动生成
- **.hbuilderx/**：编辑器配置，本地用
- **.DS_Store**：Mac 系统垃圾文件

**它们都不需要提交到 Git，所以 .gitignore 里必须写上。**

## 弄一个单独的测试目录用于测试代码

### 目录结构

```bash
uni-app-study
├─ pages
│  └─ index/index.vue    # 首页（放案例导航跳转）
├─ demo                  # 统一demo总文件夹
│  ├─ demo01.vue         # 案例01
│  ├─ demo02.vue         # 案例02
└─ pages.json
```

### pages.json 路由配置（直接注册 2 个页面）

```bash
{
	"pages": [ //pages数组中第一项表示应用启动页，参考：https://uniapp.dcloud.io/collocation/pages
		{
			"path": "pages/index/index",
			"style": {
				"navigationBarTitleText": "uni-app"
			}
		},
		// 新增demo页面照着这个格式写
		{
			"path": "demo/demo01",
			"style": {
				"navigationBarTitleText": "Flex布局案例"
			}
		},
		{
			"path": "demo/demo02",
			"style": {
				"navigationBarTitleText": ""
			}
		}
	],
	"globalStyle": {
		"navigationBarTextStyle": "black",
		"navigationBarTitleText": "uni-app",
		"navigationBarBackgroundColor": "#F8F8F8",
		"backgroundColor": "#F8F8F8"
	},
	"uniIdRouter": {}
}
```

### 访问demo01

http://localhost:5173/#/demo/demo01

#### 补充:路由为什么要加个# 才行

uni-app H5 **默认是 hash 路由模式**（Vue-Router 默认规则）：

`#` 是**锚点标识**，**#后面的地址不会发给后端服务**，全部由前端 JS 解析路由，所以必须写 `/#/demo/demo01` 才能访问。

- hash：`http://localhost:5173/#/demo/demo01` ✅ 刷新不 404，不用改服务
- history：`http://localhost:5173/demo/demo01` 无 #，但直接输地址刷新会 404

#### 补充:page/index/index.vue  样式 会影响   demo/demo01.vue

[官方组件作用域css文档](https://cn.vuejs.org/api/sfc-css-features#scoped-css)

为什么 `index.vue` 的样式会污染 `demo01.vue`？

因为你写的样式是 **全局样式**，不是**组件私有样式**！

看这个关键区别：

```vue
<style>
/* 没有 scoped → 全局样式！所有页面所有组件都生效！ */
</style>

<style scoped>
/* 有 scoped → 只对当前页面生效，不污染其他页面！ */
</style>
```

总结

1. **`<style>`** → 全局样式，**所有页面共享**（会互相污染）
2. **`<style scoped>`** → 私有样式，**只当前页面生效**（不会污染别人）