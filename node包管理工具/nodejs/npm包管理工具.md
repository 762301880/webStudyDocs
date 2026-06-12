## 三大主流包管理器对比

### 简单介绍

npm包管理工具 类比于python 的 pip包管理工具

### 命令对照表（一模一样用法，只是换前缀）

|     功能     |      npm      |       yarn       |       pnpm       |
| :----------: | :-----------: | :--------------: | :--------------: |
|    初始化    |  npm init -y  |   yarn init -y   |   pnpm init -y   |
|   安装依赖   |  npm i 包名   |  yarn add 包名   |  pnpm add 包名   |
|   开发依赖   | npm i 包名 -D | yarn add 包名 -D | pnpm add 包名 -D |
|   全局安装   | npm i 包名 -g | yarn global add  |   pnpm add -g    |
|     卸载     |  npm un 包名  |   yarn remove    |   pnpm remove    |
| 安装全部依赖 |     npm i     |       yarn       |   pnpm install   |
|   运行脚本   |  npm run xxx  |     yarn xxx     |     pnpm xxx     |

---



### pnpm（最强推荐）

**pnpm 全称：Performant npm（高性能 npm）**。

优点：

1. **速度最快**
2. 硬链接存储，**极度省硬盘**
3. 依赖结构更严谨，防幽灵依赖
4. 现代前端项目（Vue3/Vite/React）标配

缺点：极老项目偶尔小兼容问题

### 安装

```bash
# 3. 安装 pnpm（推荐）
npm i pnpm -g
```

#### 镜像加速

```bash
# pnpm

pnpm config set registry https://registry.npmmirror.com
```



####  pnpm升级

##### 添加环境变量

```bash
C:\Users\yaoliuyang\AppData\Local\pnpm\bin  # 一般在用户目录下的AppData\Local中
```

##### 安装完成后升级

```bash
pnpm self-update
```



---



### npm(不推荐)

**npm**：Node.js**自带**，最老牌，兼容性最强

缺点：速度慢、占用磁盘大、依赖冗余

#### 安装

```php
# 1. npm 自带，无需安装
node -v && npm -v
```

#### 镜像加速

```bash
# npm
npm config set registry https://registry.npmmirror.com
```

---



### yarn(不推荐)

优点：早期比 npm 稳、锁版本严格

缺点：**现在过时**，生态慢慢偏向 pnpm

#### 安装

```bash
# 2. 安装 yarn
npm i yarn -g
```

#### 镜像加速

```bash
# yarn
yarn config set registry https://registry.npmmirror.com
```







## 疑问补充

### npm /yarn/pnpm(推荐)  初始化的包能不能通用

####  结论：**绝大部分通用，但是有坑**

#### 1. 包源码完全通用

同一个 npm 包，**npm /yarn/pnpm 装出来代码一模一样**，写法、导入、用法全都通用。

#### 2. 不能直接混用的地方（重点）

1. **node_modules 结构不一样**

   

   - npm/yarn：平铺目录

   - pnpm：

     硬链接 + 虚拟目录,结构完全不同不能互相删了直接用,必须重新装

   

2. **lock 文件不互通**

   - npm → `package-lock.json`

   - yarn → `yarn.lock`

   - pnpm → 

     ```
     pnpm-lock.yaml
     ```

     锁文件不能混用，用哪个管理器就留哪个锁文件。

   

3. **脚本、命令大部分通用**

   - `install / uninstall / run dev` 基本一样
   - 少数原生依赖、二进制包**可能装完不兼容**，最好清缓存重装

####  3. 最简切换规则

- 从 npm 切 pnpm：

  删 

  ```
  node_modules
  ```

   \+ 

  ```
  package-lock.json
  ```

   → 

  ```
  pnpm install
  ```

- 从 pnpm 切 npm：

  

  删 

  ```
  node_modules
  ```

   \+ 

  ```
  pnpm-lock.yaml
  ```

   → 

  ```
  npm install
  ```

#### 4. 一句话总结

**代码通用，目录不通用，锁文件不通用，切换必须删包重安装。**

