# nodejs安装

## windows安装

首先打开官网：[官网](https://nodejs.org/zh-cn/)

> 建议使用[中文网](http://nodejs.cn/)下载,`官网`很慢 

<img src='https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/1922055-20200407103647093-1112755166.png' width='600px' heigth='400px' title='点击下载'> 

下载你需要的安装包或者软件：

因为我的电脑时windows1064位的所以我选择了对应的版本
<img src='https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/1922055-20200407103931031-1694730258.png' width='600px' heigth='400px' title='点击下载'> 

一路next安装：

<img src='https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/1922055-20200407104123954-714053076.png' width='400px' heigth='200px' title='点击下载'> 

win+r输入cmd打开命令提示工具：输入命令查看是否安装成功

```javascript
node --version
```
<img src='https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/1922055-20200407104527615-867249171.png' width='400px' heigth='200px' title='点击下载'> 

推荐一个nodejs的[学习网站](http://nqdeng.github.io/7-days-nodejs/)

# 修改镜像源

## 查看 目前正在使用的镜像源：

```javascript
npm config get registry
```
## 这里我们设置淘宝的镜像：

```javascript
npm config set registry https://registry.npm.taobao.org
```
## 安装淘宝的镜像源：

```javascript
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

