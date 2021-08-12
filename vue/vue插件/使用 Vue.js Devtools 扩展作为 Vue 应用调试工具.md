#  说明&下载

 本文搬去取[地址](https://laravelacademy.org/post/21914)

## 说明

> 如果我们的前端页面使用了 Vue.js 框架，那么安装了 Vue.js Devtools 扩展的浏览器会嗅探到其中的 Vue 实例、组件、事件、路由以及状态。以上篇教程创建的 HTML 文档为例，在 IntelliJ IDEA 中点击在 Chrome 浏览器预览之后，在打开的 Chrome 浏览器页面通过 Option + Command + I 打开开发者工具（Windows 是 F12）面板，可以在在标签页的最右侧看到 Vue 选项卡：

## 下载

> 不推荐用谷歌商店进行下载,需要翻墙比较麻烦,推荐使用第三方的软件
>
> 网站下载

这里推荐使用***[极简插件](https://chrome.zzzmh.cn/)***---[Vue.js devtools](https://chrome.zzzmh.cn/info?token=ljjemllljcmogpfapbkkighbhhppjdbg)

***安装不做叙述解释请自行查阅百度***

#  使用vue.js Devtools 插件示例

- 示例使用代码

```vue
<!DOCTYPE html>
<html lang="zh">
	<head>
		<meta charset="UTF-8">
		<meta name="viewport" content="width=device-width, initial-scale=1.0">
		<meta http-equiv="X-UA-Compatible" content="ie=edge">
		<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.js"></script>
		<title>数据演示绑定</title>
	</head>
	<body>
		<div id="app">
			<input type="text" v-model="name" placeholder="你的名字">
			 <div>{{name}}</div>
		</div>
		<script type="text/javascript">
			let data = {
				name: "我的名字是张三"
			}
			new Vue({
				el:'#app',
				data:data
			})
		</script>
	</body>
</html>

```

## 使用示例

> 打开控制台就可以看见vue选项
>
> 在组件标签页（Components）下点击 Root 就可以看到当前页面 Vue 实例中的模型数据。如果修改输入框中的值，由于在该元素上设置了数据绑定，所以对应的修改也会同步到模型数据：

![1628750585(1).jpg](https://i.loli.net/2021/08/12/9kRgnb3ISFqOiUt.png)