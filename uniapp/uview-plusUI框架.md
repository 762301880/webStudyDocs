## 资料

| 名称            | 地址                                      |
| --------------- | ----------------------------------------- |
| uview-plus 官网 | [link](https://uview-plus.jiangruyi.com/) |



## [安装](https://uview-plus.jiangruyi.com/components/install.html)

### Hbuilder X方式安装

[安装地址-无脑安装](https://ext.dcloud.net.cn/plugin?name=uview-plus)

安装完成之后看到项目中有**uni_modules\uview-plus** 代表安装成功



## 安装后配置

### [HbuilderX安装后配置](https://uview-plus.jiangruyi.com/components/downloadSetting.html)

#### 配置步骤

#### 引入uview-plus主JS库

在项目根目录中的`main.js`中，引入并使用uview-plus的JS库，注意这两行要放在`const app = createSSRApp(App)`之后。

```bash
// main.js
import uviewPlus from '@/uni_modules/uview-plus'

// #ifdef VUE3
import { createSSRApp } from 'vue'
export function createApp() {
  const app = createSSRApp(App)
  app.use(uviewPlus)
  return {
    app
  }
}
// #endif
```

#### 在引入uview-plus的全局SCSS主题文件

在项目根目录的`/uni.scss`中引入此文件, 如果不存在uni.scss自己创建一个空白文件。

```bash
/* uni.scss */
@import '@/uni_modules/uview-plus/theme.scss';
```

#### 引入uview-plus基础样式

在`App.vue`中**首行**的位置引入，注意给style标签加入lang="scss"属性。

```css
<style lang="scss">
	/* 注意要写在第一行，注意不能引入至uni.scss，同时给style标签加入lang="scss"属性 */
	@import "@/uni_modules/uview-plus/index.scss";
</style>
```

#### 配置manifest

在项目的manifest.json中增加mergeVirtualHostAttributes配置

```bash
"mp-weixin" : {
	"appid" : "",
	...
	"mergeVirtualHostAttributes" : true
},
"mp-toutiao" : {
	"appid" : "",
	...
	"mergeVirtualHostAttributes" : true
}
```

#### 安装依赖库

```bash
npm i dayjs
npm i clipboard
```

#### 配置easycom组件模式

此配置需要在项目根目录的`pages.json`中进行。

##### uview-plus3.x配置

```json
// pages.json
{
	"easycom": {
		"autoscan": true,
		// 注意一定要放在custom里，否则无效，https://ask.dcloud.net.cn/question/131175
		"custom": {
			"^u--(.*)": "@/uni_modules/uview-plus/components/u-$1/u-$1.vue",
			"^up-(.*)": "@/uni_modules/uview-plus/components/u-$1/u-$1.vue",
	    "^u-([^-].*)": "@/uni_modules/uview-plus/components/u-$1/u-$1.vue"
		}
	},
	
	// 此为本身已有的内容
	"pages": [
		// ......
	]
}
```

