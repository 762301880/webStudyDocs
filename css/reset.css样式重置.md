## reset.css

正式名称：**CSS Reset**，中文一般叫 **CSS 重置样式表 / 样式重置文件**

经典原版：**Eric Meyer's Reset CSS**（最出名的那版 reset.css）

> 作用：**暴力清除浏览器自带的默认样式**（margin、padding、字体加粗、列表圆点等全部清零），让所有元素起点一致，方便像素级还原设计稿

和它容易混淆的另一个：normalize.css

- **reset.css**：全部干掉默认样式，一切从头写
- **normalize.css**：**标准化样式**，不会一刀切删掉所有默认样式，只修复浏览器差异，保留合理原生样式（现在现代项目更常用）

##  代码示例

```css
/* 重置元素默认内外边距，清除浏览器自带默认间距 */
body, div, dl, dt, dd, ul, ol, li, h1, h2, h3, h4, h5, h6, pre, form, fieldset, legend, input, textarea, p, blockquote, th, td {
    margin: 0;
    padding: 0;
}

/* 设置页面默认文字居中，以及全局字体优先级 */
body {
    text-align: center;
    font-family: Helvetica Neue,Helvetica,Arial,Microsoft Yahei,Hiragino Sans GB,\5B8B\4F53,sans-serif;
}

/* 清除li列表前面默认的圆点 */
li {
    list-style: none;
}

/* 清除a标签默认的下划线 */
a {
    text-decoration: none;
}

/* 清除图片在旧浏览器点击时出现的边框 */
img {
    border: none;
}
```

## vite项目

**原生 Vite（vue/react/vanilla 模板）本身不会自动干掉浏览器默认样式！**

Vite 只是打包 / 构建工具，它不管 CSS 重置。新建出来的 Vite 项目，`ul`、`p`、`h1` 这些标签**依旧保留浏览器自带 margin、列表圆点**，需要你手动引入 reset /normalize。

做法：自己新建 `reset.css` / `normalize.css`，在 `main.ts` 最顶部引入

```tsx
// main.ts
import './assets/reset.css' // 放在最前面，全局生效
import { createApp } from 'vue'
import App from './App.vue'
createApp(App).mount('#app')
```

