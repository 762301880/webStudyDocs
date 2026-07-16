## 资料

| 名称          | 地址                                                         |
| ------------- | ------------------------------------------------------------ |
| jsDoc官网文档 | [中文文档](https://jsdoc.node.org.cn/)  [官方](https://jsdoc.app/) |

## JSDoc 方法 / 函数注释完整使用教程

### 一、基础模板（普通函数）

```js
/**
 * 函数描述：功能做什么
 * @param {类型} 参数名 - 参数说明
 * @param {类型} [可选参数] - 可选参数说明
 * @param {类型} [带默认值=默认值] - 带默认值的可选参数
 * @returns {类型} 返回值说明
 */
function add(a, b) {
  return a + b;
}
```

### 二、常用核心标签详解

#### 1. @param 入参

格式：`@param {类型} 变量名 - 描述`

- 可选参数用 `[]` 包裹
- 默认值写在括号内 `[num=0]`

```javascript
/**
 * 计算两数之和
 * @param {number} a - 第一个数字
 * @param {number} [b=10] - 第二个数字，默认10
 * @param {string} [name] - 可选名称
 * @returns {number} 相加结果
 */
function sum(a, b = 10, name) {
  return a + b;
}
```

#### 2. @returns / @return 返回值

二选一即可，标注返回类型与含义

```javascript
/**
 * @returns {string} 拼接后的字符串
 */
function getName() {
  return "test";
}
```

#### 3. @typedef 自定义复杂类型（对象 / 数组）

适合重复使用的对象结构

```javascript
/**
 * @typedef {Object} UserInfo
 * @property {number} id - 用户ID
 * @property {string} username - 用户名
 * @property {boolean} isVip - 是否会员
 */

/**
 * 获取用户信息
 * @param {number} userId - 用户ID
 * @returns {UserInfo} 用户详情对象
 */
function getUser(userId) {}
```

#### 4. @async / @await 异步函数

配合 Promise 使用

```javascript
/**
 * 异步请求接口
 * @async
 * @param {string} url - 请求地址
 * @returns {Promise<Object>} 返回接口数据
 */
async function request(url) {
  return await fetch(url).then(res => res.json());
}
```

#### 5. @throws / @exception 抛出异常

标注函数会抛出的错误类型与场景

```javascript
/**
 * 除法计算
 * @param {number} a - 被除数
 * @param {number} b - 除数
 * @returns {number} 商
 * @throws {Error} 除数不能为0
 */
function div(a, b) {
  if (b === 0) throw new Error("除数不能为0");
  return a / b;
}
```

#### 6.@callback 回调函数类型

```javascript
/**
 * @callback SuccessCb
 * @param {number} code - 状态码
 * @param {string} msg - 提示信息
 */

/**
 * 执行回调
 * @param {SuccessCb} cb - 成功回调
 */
function handle(cb) {}
```

#### 7. @deprecated 废弃方法

标记接口不再推荐使用

```javascript
/**
 * 旧版加法函数
 * @deprecated 请使用 sum() 替代
 * @param {number} a
 * @param {number} b
 * @returns {number}
 */
function oldAdd(a, b) {}
```

#### 8. @example 示例代码

生成文档时展示调用案例

```javascript
/**
 * 两数相乘
 * @param {number} a
 * @param {number} b
 * @returns {number}
 * @example
 * mul(2, 3) // 6
 */
function mul(a, b) {}
```

###  三、箭头函数写法

```javascript
/**
 * 箭头函数示例
 * @param {string} str - 输入字符串
 * @returns {string} 大写字符串
 */
const toUpper = (str) => str.toUpperCase();
```

### 四、Class 类内方法注释

```javascript
class User {
  /**
   * 用户构造函数
   * @param {number} id - 用户id
   * @param {string} name - 姓名
   */
  constructor(id, name) {
    this.id = id;
    this.name = name;
  }

  /**
   * 获取用户完整名称
   * @returns {string}
   */
  getFullName() {
    return this.name;
  }
}
```

### 五、TS 风格 JSDoc 高级类型（纯 JS 项目推荐）

支持联合类型、泛型、数组、可选属性

```javascript
/**
 * @param {string | number} id - id可以是字符串或数字
 * @param {Array<string>} list - 字符串数组
 * @param {?boolean} flag - 可为null的布尔值
 * @param {Record<string, any>} obj - 任意键值对象
 * @returns {Promise<string[]>}
 */
function demo(id, list, flag, obj) {}
```

### 六、Vue/uniApp 接口函数完整示例

```javascript
/**
 * 获取商品列表
 * @async
 * @param {number} [page=1] - 页码，默认1
 * @param {number} [size=10] - 每页条数
 * @returns {Promise<{list: GoodsItem[], total: number}>}
 * @throws {Error} 网络请求失败抛出异常
 * @example
 * getGoodsList(1, 20).then(res => console.log(res.list))
 */
async function getGoodsList(page = 1, size = 10) {
  const res = await api.get("/goods", { page, size });
  return res.data;
}

/**
 * @typedef {Object} GoodsItem
 * @property {number} id - 商品ID
 * @property {string} title - 商品标题
 * @property {number} price - 价格
 */
```

### 七、生成文档命令

1. 安装

```bash
npm install -g jsdoc
```

1. 生成文档（输出到 out 文件夹）

```bash
jsdoc src/ -d out
```

1. 指定配置文件

```bash
jsdoc src/ -c jsdoc.json
```