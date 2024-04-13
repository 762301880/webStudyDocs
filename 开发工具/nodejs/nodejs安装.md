# nodejs安装

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

> 配置环境变量**解压目录**

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

