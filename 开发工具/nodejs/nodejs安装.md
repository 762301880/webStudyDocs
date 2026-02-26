# Node.js 到底是什么

### 一、Node.js 到底是什么？

首先要纠正一个常见误解：**Node.js 不是一门编程语言，也不是一个框架**。

它的官方定义是：**一个基于 Chrome V8 引擎的 JavaScript 运行时环境**。

用大白话解释：

- JavaScript 原本只能在浏览器里跑（比如网页的交互、表单验证），Node.js 把浏览器里执行 JS 的核心引擎（V8）抽出来，让 JS 可以脱离浏览器，直接在你的电脑（服务器）上运行。
- 你可以把它理解成：Node.js 是一个 “JS 解释器”，就像 Python 需要 Python 解释器、Java 需要 JVM 一样，有了它，你的电脑就能执行 JS 代码了。

### 二、Node.js 能用来干嘛？

核心作用是**让 JavaScript 可以开发后端程序**，具体常见用途：

1. **搭建后端服务器**

   以前后端开发主要用 Java、Python、PHP 等，现在可以用你熟悉的 JS 写后端接口、处理 HTTP 请求、操作数据库。比如：

   ```javascript
   // 最简单的 Node.js 服务器示例
   const http = require('http'); // 引入 Node.js 内置的 http 模块
   
   // 创建服务器，处理请求并返回响应
   const server = http.createServer((req, res) => {
     res.writeHead(200, {'Content-Type': 'text/plain'});
     res.end('你好，这是 Node.js 搭建的服务器！\n');
   });
   
   // 监听 3000 端口
   server.listen(3000, () => {
     console.log('服务器运行在 http://localhost:3000');
   });
   ```

   

   运行这段代码后，打开浏览器访问 `http://localhost:3000`，就能看到对应的文字，这就是最基础的后端服务。

   

2. **前端工程化工具的基石**

   你日常用的前端工具（比如 npm、yarn、webpack、Vite、Vue CLI、React 脚手架）全是基于 Node.js 运行的。没有 Node.js，这些工具根本启动不了。

   

3. **处理文件 / IO 操作**

   可以用 JS 读写电脑里的文件、处理数据流（比如批量修改文件、解析日志），这是浏览器里的 JS 做不到的（浏览器为了安全限制了文件操作）。

   ```javascript
   // 读取文件示例
   const fs = require('fs'); // 引入文件操作模块
   fs.readFile('./test.txt', 'utf8', (err, data) => {
     if (err) throw err;
     console.log('文件内容：', data);
   });
   ```

   

4. **开发跨平台应用**

   结合 Electron 框架，能用 JS/HTML/CSS 开发桌面应用（比如 VS Code、微信开发者工具都是这么做的）。

   

### 三、Node.js 的核心特点（新手需要知道）

1. **单线程、非阻塞 I/O**：适合处理大量并发请求（比如聊天应用、接口服务），但不适合做 CPU 密集型任务（比如大量数学计算）。
2. **npm 生态**：Node.js 自带的包管理工具，全球最大的开源库生态，你能想到的功能几乎都有现成的包（比如处理日期的 moment、操作数据库的 mongoose）。
3. **前后端统一语言**：前端用 JS，后端也用 JS，不用切换语言，降低了开发成本。

### 总结

1. Node.js 是 JS 的 “运行环境”，让 JS 能脱离浏览器跑在电脑 / 服务器上；
2. 核心用途：写后端接口、支撑前端工程化工具、处理文件 / 数据流、开发跨平台应用；
3. 最大优势：npm 生态丰富，前后端能用同一种语言（JS）开发。

如果是刚入门，先装 Node.js（官网 https://nodejs.org/ 下载 LTS 长期支持版），跑一遍上面的示例代码，就能直观感受到它的作用了。

# nodejs安装(注意安装LTS长期支持版本)

## windows安装

###  exe安装(不推荐)

首先打开官网：[官网](https://nodejs.org/zh-cn/)

> 建议使用[中文网](http://nodejs.cn/)下载,`官网`很慢 

<img src='https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1922055-20200407103647093-1112755166.png' width='600px' heigth='400px' title='点击下载'> 

下载你需要的安装包或者软件：

因为我的电脑时windows1064位的所以我选择了对应的版本
<img src='https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1922055-20200407103931031-1694730258.png' width='600px' heigth='400px' title='点击下载'> 

一路next安装：

<img src='https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1922055-20200407104123954-714053076.png' width='400px' heigth='200px' title='点击下载'> 

###  二进制包安装(推荐)

> 下载二进制包放到需要的目录

![image-20230522093926643](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20230522093926643.png)

> 配置环境变量**解压目录**  编辑**path变量**

![image-20230522094016551](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20230522094016551.png)

### 查看是否安装成功

win+r输入cmd打开命令提示工具：输入命令查看是否安装成功

```javascript
node --version
```
<img src='https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1922055-20200407104527615-867249171.png' width='400px' heigth='200px' title='点击下载'> 

推荐一个nodejs的[学习网站](http://nqdeng.github.io/7-days-nodejs/)

# 修改镜像源

## 查看 目前正在使用的镜像源：

```javascript
npm config get registry
```
## 这里我们设置淘宝的镜像：

> https://npmmirror.com/

```javascript
npm config set registry https://registry.npm.taobao.org
```
## 安装淘宝的镜像源：

```javascript
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

# 安装yarn

## yarn介绍

> Yarn 是一个快速、可靠、安全的依赖包管理工具，可用于替代 NPM。使用 Yarn 可以更快地安装依赖项，同时减少安装过程中的错误和运行时问题。以下是 Yarn 的一些关键特性：
>
> 1. 快速：由于其高效的算法，Yarn 可以快速安装依赖项，大大缩短了构建时间。
>
> 2. 稳定：Yarn 确保所有依赖项的版本和树形结构都得到良好管理，并使用锁文件确保版本的一致性，从而减少了运行时错误和不一致性。
>
> 3. 安全：通过使用公钥签名和哈希值来检查包的完整性和安全性，Yarn 保证依赖项的安全性。
>
> 4. 可定制：Yarn 支持自定义命令和配置，以满足特定项目的需求。
>
> 总之，Yarn 是一个非常强大的包管理器，可用于优化 JavaScript 应用程序的构建和依赖项管理。

## 安装

> 在安装yarn之前，你需要先安装Node.js。你可以通过以下步骤在 Node.js 上安装 yarn：
>
> 1. 首先，你需要下载并安装 Node.js，可以从官网 https://nodejs.org/en/download/ 下载安装包并进行安装。
> 2. 安装了 Node.js 之后，可以使用你的终端或命令提示符运行以下命令安装 Yarn：

```shell
#这将全局安装 Yarn，并使其可用于所有项目。

npm install -g yarn
```

验证 Yarn 是否已正确安装。在终端或命令提示符中输入以下命令：

```shell
yarn --version
```





# bug解析

## npm install -g npm升级npm报错无法加载文件 D:\Program Files\node-v18.16.1-win-x64\npm.ps1

> PS D:\web_work\order-meal-mini-program> npm install -g npm
> npm : 无法加载文件 D:\Program Files\node-v18.16.1-win-x64\npm.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 htt
> ps:/go.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。
> 所在位置 行:1 字符: 1

**解决方案**

> 搜索[powerShell](https://so.csdn.net/so/search?q=powerShell&spm=1001.2101.3001.7020)，然后右键以管理员身份运行,以管理员运行后，弹出命令窗口
>
> 在窗口上执行：`set-ExecutionPolicy RemoteSigned`，然后输入Y，按回车确认

![image-20230629141614507](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20230629141614507.png)

# 扩展

## nvm安装多个不同node版本

**资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 网络博客   | [link](https://cloud.tencent.com/developer/article/2186393?areaSource=102001.11&traceId=iFAgcnr6VOQ0mtNw6ZeIh) |
| nvm_github | [link](https://github.com/coreybutler/nvm-windows)           |

