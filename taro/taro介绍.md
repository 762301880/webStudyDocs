## 资料

| 名称         | 地址                                 |
| ------------ | ------------------------------------ |
| taro官方文档 | [link](https://docs.taro.zone/docs/) |



## 介绍(类似于uniapp)

**Taro** 是一个开放式跨端跨框架解决方案，支持使用 React/Vue/Nerv 等框架来开发 [微信](https://mp.weixin.qq.com/) / [京东](https://mp.jd.com/?entrance=taro) / [百度](https://smartprogram.baidu.com/) / [支付宝](https://mini.open.alipay.com/) / [字节跳动](https://developer.open-douyin.com/) / [QQ](https://q.qq.com/) / [飞书](https://open.feishu.cn/document/uYjL24iN/ucDOzYjL3gzM24yN4MjN) / [快手](https://mp.kuaishou.com/) 小程序 / H5 / RN / [ASCF元服务](https://developer.huawei.com/consumer/cn/doc/atomic-ascf/ascf-overview) 等应用。

现如今市面上端的形态多种多样，Web、React Native、微信小程序等各种端大行其道。当业务要求同时在不同的端都要求有所表现的时候，针对不同的端去编写多套代码的成本显然非常高，这时候只编写一套代码就能够适配到多端的能力就显得极为需要。

## 安装

Taro 项目基于 node，请确保已具备较新的 node 环境（>=16.20.0），推荐使用 node 版本管理工具 [nvm](https://github.com/creationix/nvm) 来管理 node，这样不仅可以很方便地切换 node 版本，而且全局安装时候也不用加 sudo 了。

### CLI 工具安装

首先，你需要使用 npm 或者 yarn 全局安装 `@tarojs/cli`，或者直接使用 [npx](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b):

#### pnpm

```bash
# 使用 pnpm 安装 CLI

pnpm install -g @tarojs/cli
```

## 项目初始化

使用命令创建模板项目：

```bash
taro init myApp
```

![图片](https://img30.360buyimg.com/ling/jfs/t1/121270/15/15083/672721/5f89357dEf36b7fe2/ecb98df1436cd3d5.jpg)

##  [编译运行](https://docs.taro.zone/docs/GETTING-STARTED#%E5%BE%AE%E4%BF%A1%E5%B0%8F%E7%A8%8B%E5%BA%8F)

### 微信小程序

编译命令

```bash
# pnpm
$ pnpm dev:weapp
$ pnpm build:weapp

# yarn
$ yarn dev:weapp
$ yarn build:weapp

# npm script
$ npm run dev:weapp
$ npm run build:weapp

# 仅限全局安装
$ taro build --type weapp --watch
$ taro build --type weapp

# npx 用户也可以使用
$ npx taro build --type weapp --watch
$ npx taro build --type weapp

# watch 同时开启压缩
$ set NODE_ENV=production && taro build --type weapp --watch # CMD
$ NODE_ENV=production taro build --type weapp --watch # Bash
```

