## 注意事项

### 不要把全部项目全上传到git仓库里面去了

这些**绝对不能上传**：

1. **`node_modules/`** —— 依赖包（几百 MB，别人下载后自己 npm i 就行）
2. **`dist/`** —— 打包后的文件（构建产物）
3. **`.env`** —— 环境变量、密钥（会泄露信息）
4. **`.vscode/` / `.idea/`** —— 编辑器配置（每个人不一样）
5. 日志、缓存文件

**忽略模板**   `.gitignore`

```shell
# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*
lerna-debug.log*

node_modules
dist
dist-ssr
*.local

# Editor directories and files
.vscode/*
!.vscode/extensions.json
.idea
.DS_Store
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?

```

**拉取项目后执行 **

```shell
npm install   # 安装环境依赖

npm run dev   # 启动开发环境
```

