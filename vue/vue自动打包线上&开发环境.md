## 参考文档

[环境变量与模式](https://cn.vitejs.dev/guide/env-and-mode)

## env配置

### 说明

Vite 使用 [dotenv](https://github.com/motdotla/dotenv) 从你的 [环境目录](https://cn.vitejs.dev/config/shared-options#envdir) 中的下列文件加载额外的环境变量：

```bash
.env                # 所有情况下都会加载
.env.local          # 所有情况下都会加载，但会被 git 忽略
.env.[mode]         # 只在指定模式下加载
.env.[mode].local   # 只在指定模式下加载，但会被 git 忽略
```

```bash
环境加载优先级

一份用于指定模式的文件（例如 .env.production）会比通用文件的优先级更高（例如 .env）。

Vite 总是会加载 .env 和 .env.local 文件，除此之外还会加载模式特定的 .env.[mode] 文件。在模式特定文件中声明的变量优先级高于通用文件中的变量，但仅在 .env 或 .env.local 中定义的变量仍然可以在环境中使用。

另外，Vite 执行时已经存在的环境变量有最高的优先级，不会被 .env 类文件覆盖。例如当运行 VITE_SOME_KEY=123 vite build 的时候。

.env 类文件会在 Vite 启动一开始时被加载，而改动会在重启服务器后生效。
```



### 本地配置 .env

```bash
# .env 文件内容  写入配置（必须 VITE_ 开头）
VITE_APP_TITLE = 后台管理系统
# api_url
VITE_API_URL = http://47.107.33.56:1997/api
```

### 开发配置 .env.production

```bash
# .env 文件内容  写入配置（必须 VITE_ 开头）
VITE_APP_TITLE = 后台管理系统线上
# api_url
VITE_API_URL = http://www.cs.com/api
```





## 修改package.json

```bash
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "build:dev": "vite build --mode development", //打包开发环境
    "build:prod": "vite build --mode production", //打包线上环境
    "preview": "vite preview"
  },
```

##  运行打包

```bash
# 打包开发环境
pnpm run build:dev

# 打包线上环境配置
build:prod
```

