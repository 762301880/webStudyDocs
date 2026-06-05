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