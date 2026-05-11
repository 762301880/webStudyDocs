## Vue CLI 是什么

**Vue CLI 是 Vue 官方出的「脚手架工具」，帮你一键快速创建、管理、运行 Vue 项目的命令行工具**。

### 拆解开理解

- **CLI**：Command Line Interface，**命令行工具**（就是你在终端输命令用的）
- **Vue CLI**：专门给 Vue 项目用的**官方脚手架**

### 它能干什么？

不用你手动建文件夹、配 webpack、配打包、配路由、配环境……

直接敲几条命令就搞定：

1. 一键创建 Vue 项目

   ```sh
   vue create 项目名
   ```

2. **自带配置好的打包、开发服务器、热更新**

3. 内置 webpack、babel 等底层配置，**不用自己瞎配工程化**

4. 可以选模板：Vue2 / Vue3、是否装路由、Vuex、TS 等

## 先检查是否真的安装了 Vue CLI

先在 PowerShell 里运行这个命令，看看有没有安装：

```shell
npm list -g @vue/cli
```

### 情况 A：显示 `empty` 或找不到

说明**没安装**，直接运行安装命令：

```shell
npm install -g @vue/cli
```

### 情况 B：显示版本号（例如 @vue/cli@5.0.8）

说明**已经安装**，只是环境变量没配置 

### 检测是否安装成功

```shell
PS D:\Desktop\element_ui_test> vue -V
@vue/cli 5.0.9
```

##  创建项目示例

```shell
PS D:\Desktop> vue create elementui_test  # 创建一个 elementui_test 项目


Vue CLI v5.0.9
? Please pick a preset: Default ([Vue 3] babel, eslint)


Vue CLI v5.0.9
✨  Creating project in D:\Desktop\elementui_test.
🗃  Initializing git repository...
⚙️  Installing CLI plugins. This might take a while...


added 830 packages in 48s

110 packages are looking for funding
  run `npm fund` for details
🚀  Invoking generators...
📦  Installing additional dependencies...


added 86 packages in 6s

122 packages are looking for funding
  run `npm fund` for details
⚓  Running completion hooks...

📄  Generating README.md...

🎉  Successfully created project elementui_test.
👉  Get started with the following commands:

 $ cd elementui_test
 $ npm run serve
```

**默认测试启动**

```shell
PS D:\Desktop> cd .\elementui_test\
PS D:\Desktop\elementui_test> npm run serve

> elementui_test@0.1.0 serve
> vue-cli-service serve

 INFO  Starting development server...


 DONE  Compiled successfully in 15165ms                                                                         13:59:16


  App running at:
  - Local:   http://localhost:8080/  # 启动后的本地ip地址
  - Network: http://10.152.23.3:8080/

  Note that the development build is not optimized.
  To create a production build, run npm run build.
```

