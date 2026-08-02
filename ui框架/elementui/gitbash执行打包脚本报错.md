## 事情原因

> git bash下执行打包脚本报错

```bash
$ npm run build:prod

> my-admin@0.0.0 build:prod
> vite build --mode production

'node' 不是内部或外部命令，也不是可运行的程序
或批处理文件。
git  执行shell命令报错  
```

这个报错的本质是：

> **Git Bash 执行 `npm run` 时，调用 Vite 的过程中，Windows 子进程环境找不到 Node.js。**

不是 Vite 坏了，也不是项目代码问题。



## 执行链路

命令：

```
npm run build:prod
```

实际执行：

```
npm
 |
 | 读取 package.json
 |
 ↓
"build:prod": "vite build --mode production"
 |
 ↓
调用 node_modules/.bin/vite.cmd
 |
 ↓
vite.cmd 内部执行
 |
 ↓
node vite/bin/vite.js
```

错误发生在最后一步：

```bash
vite.cmd
   |
   ↓
  node
   |
   ↓
找不到 node.exe
```

所以报：

```bash
'node' 不是内部或外部命令，
也不是可运行的程序或批处理文件。
```







## 解决

### 添加配置

```bash
echo 'export PATH="/d/node/node-v24.18.0-win-x64:$PATH"' >> ~/.bashrc
```

### 为什么执行 export 后好了？

```bash
export PATH="/d/node/node-v24.18.0-win-x64:$PATH"
```

相当于告诉当前 Git Bash：

```bash
以后找 node 去这里：
D:\node\node-v24.18.0-win-x64
```

于是：

```bash
pnpm/npm
 |
 ↓
vite.cmd
 |
 ↓
node.exe
 |
 ↓
成功
```

### 根本原因

电脑存在多个 Node 环境残留：

之前有：

```bash
D:\node\node-v20.12.0-win-x64
D:\node\node-v24.15.0-win-x64
D:\node\node-v24.18.0-win-x64
```

切换 Node 后：

- Git Bash PATH 更新了 ✅
- Windows 系统 PATH 没更新 ❌

导致：

| 环境     | node     |
| -------- | -------- |
| Git Bash | ✅ 能找到 |
| cmd.exe  | ❌ 找不到 |
| npm run  | ❌ 找不到 |
| pnpm run | ❌ 找不到 |

### 正确修复

不要每次：

```bash
export PATH=...
```

应该修改 Windows 环境变量：

Path 添加：

```
D:\node\node-v24.18.0-win-x64
```

删除旧版本：

```
D:\node\node-v20.12.0-win-x64
D:\node\node-v24.15.0-win-x64
```

然后重新打开：

- Git Bash
- VS Code
- CMD

验证：

```
cmd //c node -v
```

应该输出：

```
v24.18.0
```

之后：

```
npm run build:prod
```

就不会再报这个错误。