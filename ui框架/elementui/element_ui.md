## 资料

| 名称                  | 地址                                                         |
| --------------------- | ------------------------------------------------------------ |
| elementui官网   vue2  | [vue2.x](https://element.eleme.cn/#/zh-CN)                   |
| elementui官网    vue3 | [vue3.x](https://element-plus.org/zh-CN/)  [install](https://element-plus.org/zh-CN/guide/installation) |

## [安装](https://element.eleme.cn/#/zh-CN/component/installation)

### npm 安装 支持vue2

推荐使用 npm 的方式安装，它能更好地和 [webpack](https://webpack.js.org/) 打包工具配合使用。

```shell
npm i element-ui -S    # 默认支持vue2不支持vue3
```

### 命令解释

 **`npm i element-ui -S`**

> `npm`：Node 包管理工具（安装依赖必备）
>
> `i`：是 `install` 的**简写**，意思就是「安装」
>
> `element-ui`：要安装的**包名**（饿了么团队开发的 Vue UI 组件库）
>
> `-S`：安装参数

**`-S` 参数到底是什么？**

`-S` = `--save` 的**简写**

**作用：**

把安装的包 **写入 `package.json` 的 `dependencies` 字段**

→ 也就是**生产环境依赖**（项目上线后也需要用到）

**对比记忆（最常用 3 个）**

|  参数  |     全称     |     写入位置      |                   使用场景                    |
| :----: | :----------: | :---------------: | :-------------------------------------------: |
|  `-S`  |   `--save`   |  `dependencies`   | **项目运行必须**（element-ui、vue、axios 等） |
|  `-D`  | `--save-dev` | `devDependencies` |    **开发时用**（webpack、babel、sass 等）    |
| 无参数 |      -       |      不写入       |               临时安装，不记录                |

**完整等价命令**

下面这 3 条命令**效果完全一样**：

```shell
npm i element-ui -S
npm install element-ui --save
npm install element-ui
```

**新版 npm 已经默认 -S**，所以现在直接写 `npm i element-ui` 就够了。

**安装后会发生什么？**

1. 在项目 `node_modules/` 里下载 `element-ui` 代码
2. 在 `package.json` 里自动添加：

```json
"dependencies": {
  "element-ui": "^2.15.14"  // 版本号自动生成
}
```

**总结**

- `npm i element-ui` = 安装 Element UI
- `-S` = 保存为**生产依赖**（新版 npm 可省略）
- 日常开发直接用：`npm i element-ui` 即可

### npm 安装 支持vue3

```shell
# vue3
npm install element-plus --save
```

