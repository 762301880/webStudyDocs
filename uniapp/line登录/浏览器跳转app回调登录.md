## uniapp LINE 浏览器跳转登录完整方案（H5 / 小程序 / App 通用跳转授权）

### 一、核心原理

LINE OAuth2 授权流程：

1. 前端拼接 LINE 官方授权地址，跳转内置浏览器打开
2. 用户授权后 LINE 回调你的业务域名（redirect_uri）
3. 后端接收回调 `code`，换取 access_token、用户信息
4. 前端轮询 / 监听回调页面存储的登录凭证完成登录

LINE 授权基础参数

- 授权地址：`https://access.line.me/oauth2/v2.1/authorize`
- 必备参数：
  - `response_type=code`
  - `client_id`：LINE Channel ID
  - `redirect_uri`：后端回调域名（必须在 LINE 后台白名单）
  - `scope=openid%20profile%20email`
  - `state`：防跨站伪造随机字符串

### 二、uniapp 多端跳转代码（App/H5 / 微信小程序）

#### 1. 封装 LINE 登录工具 js（lineOauth.js）

```js
// utils/lineOAuth.js
export default {
  config: {
    lineClientId: "你的LINE Channel ID",
    redirectUri: "https://xxx.com/api/line/callback", // LINE后台白名单回调地址
    scope: "openid profile email"
  },

  // 生成防CSRF随机state
  createState() {
    let str = "";
    const chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
    for (let i = 0; i < 32; i++) str += chars[Math.floor(Math.random() * chars.length)];
    uni.setStorageSync("line_state", str);
    return str;
  },

  // 拼接标准授权链接（默认开启Auto Login唤起LINE App）
  getAuthUrl() {
    const state = this.createState();
    const params = new URLSearchParams({
      response_type: "code",
      client_id: this.config.lineClientId,
      redirect_uri: this.config.redirectUri,
      scope: this.config.scope,
      state: state
      // 禁止添加 disable_auto_login:true 否则强制网页登录
    });
    return `https://access.line.me/oauth2/v2.1/authorize?${params.toString()}`;
  },

  /**
   * App端唤起系统浏览器（唯一能拉起LINE App授权方案）
   */
  launchLineAuth() {
    return new Promise((resolve, reject) => {
      const url = this.getAuthUrl();
      // #ifdef APP-PLUS
      // 指定LINE包名，优先匹配唤起LINE客户端
      plus.runtime.openURL(
        url,
        (err) => {
          reject("唤起浏览器失败，请安装浏览器");
        },
        "jp.naver.line.android" // Android LINE包名，iOS可忽略
      );

      // 轮询监听登录结果，超时3分钟
      const timeout = 180000;
      const start = Date.now();
      const pollTimer = setInterval(() => {
        const loginData = uni.getStorageSync("line_login_success");
        if (loginData) {
          clearInterval(pollTimer);
          uni.removeStorageSync("line_login_success");
          resolve(loginData);
          return;
        }
        if (Date.now() - start > timeout) {
          clearInterval(pollTimer);
          reject("登录超时或取消授权");
        }
      }, 1000);
      // #endif

      // #ifdef H5
      window.location.href = url;
      resolve();
      // #endif
    });
  }
};
```

#### 2. 登录页面调用示例 login.vue

```vue
<template>
  <view class="login">
    <button @click="lineLogin">LINE一键授权登录</button>
  </view>
</template>

<script>
import lineOAuth from "@/utils/lineOAuth.js";
export default {
  methods: {
    async lineLogin() {
      uni.showLoading({ title: "跳转LINE授权" });
      try {
        const userInfo = await lineOAuth.launchLineAuth();
        uni.hideLoading();
        uni.showToast({ title: "授权成功" });
        console.log("LINE用户信息", userInfo);
        // 业务登录逻辑，跳转首页
        uni.switchTab({ url: "/pages/index/index" });
      } catch (err)
        uni.hideLoading();
        uni.showToast({ title: err, icon: "none" });
      }
    }
  }
};
</script>
```

### 三、后端回调页面逻辑（关键，H5 页面，域名白名单）

`https://xxx.com/api/line/callback` 页面代码（简单 HTML，接收 code，请求后端接口换取用户信息）

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>LINE登录回调</title>
</head>
<body>
<script>
// 截取url参数 code、state
function getUrlParam(name) {
    const reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)");
    const r = window.location.search.substr(1).match(reg);
    if (r != null) return decodeURIComponent(r[2]);
    return null;
}

const code = getUrlParam('code');
const state = getUrlParam('state');
const err = getUrlParam('error');

if(err) {
    // 用户取消授权
    window.postMessage({type: 'line_login_cancel'}, '*');
    window.close();
}

// 调用后端接口，code换取登录token、用户资料
fetch('/api/line/getToken', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({code, state})
})
.then(res => res.json())
.then(res => {
    if(res.code === 200) {
        // 通知uniapp webview登录成功，传递登录凭证
        window.postMessage({
            type: 'line_login_success',
            data: res.data
        }, '*');
    } else {
        window.postMessage({type: 'line_login_fail', msg: res.msg}, '*');
    }
    setTimeout(() => {
        window.close();
    }, 500);
})
.catch(() => {
    window.postMessage({type: 'line_login_fail', msg: '网络异常'});
    setTimeout(() => window.close(), 500);
})
</script>
</body>
</html>
```

### 四、App 端两种浏览器跳转方案区别

1. **系统浏览器跳转（plus.runtime.openURL）**
   - 优点：LINE APP 会自动唤起，授权体验最好
   - 缺点：跳出 uniapp 应用，返回后需要轮询登录状态
2. **内置 web-view 跳转（uni.navigateTo + web-view）**
   - 优点：全程不离开 App，支持 onMessage 实时接收登录回调，无需长轮询
   - 缺点：部分设备无法唤起 LINE App，仅打开网页版 LINE 授权

### 五、LINE 开发者后台必填配置（否则授权报错）

1. 创建 LINE Login Channel，获取 `Channel ID`、`Channel Secret`
2. 添加 **Callback URL** 白名单：填入你的 `redirectUri`
3. 开启 Web app login，勾选允许网页授权
4. 如需邮箱权限，申请 `email` scope 权限

### 六、后端接口简要逻辑（Node/PHP 通用流程）

1. 接收回调 `code`、前端传来的 `state`，校验本地存储的 state 防伪造

2. POST 请求 LINE 令牌接口换取 

   ```
   access_token
   ```

   ```
   POST https://api.line.me/oauth2/v2.1/token
   参数：grant_type, code, client_id, client_secret, redirect_uri
   ```

3. 使用 access_token 获取用户资料：`GET https://api.line.me/v2/profile`

4. 生成业务系统登录 token，返回给回调页面

### 七、常见问题

1. 跳转 400：redirect_uri 未加入 LINE 后台白名单
2. 拿不到 code：state 不匹配、scope 参数错误
3. App 无法唤起 LINE：优先使用 `plus.runtime.openURL` 系统浏览器方案
4. 小程序 web-view 空白：业务域名需要配置小程序业务域名白名单

## 实战示例代码(浏览器line授权)

### login.vue

```js
lineLogin() {

				if (!this.isActive) {
					this.$refs.uToast.show({
						type: 'error',
						message: '请勾选用户协议'
					})
					return
				}

				const clientId = '2010427790' // LINE Channel ID
				const baseUrl = config.apiurl
				// 拼接完整回调地址，插值用 ${baseUrl}
				const redirectUri = encodeURIComponent(`${baseUrl}line/getAccessToken`)

				const state = Date.now()

				const url =
					'https://access.line.me/oauth2/v2.1/authorize' +
					'?response_type=code' +
					'&client_id=' + clientId +
					'&redirect_uri=' + redirectUri +
					'&state=' + state +
					'&scope=profile%20openid'

				// APP
				// #ifdef APP-PLUS
				plus.runtime.openURL(url)
				// #endif

				// H5
				// #ifdef H5
				location.href = url
				// #endif
}
```

### app.vue

```js
        onLaunch: function(options) {
         // #ifdef APP-PLUS
		// 独立全局方法，不依赖this，自动处理无需弹窗确认
		function handleLineCallback(url) {
		    if (!url) {
		        uni.showModal({ title: "错误", content: "未获取到唤起链接", showCancel: false })
		        return
		    }
		    console.log('LINE回调原始链接：', url)
		
		    // 校验协议前缀，非LINE登录链接直接返回
		    if (!url.startsWith('dhuam://line/callback')) {
		        uni.showModal({
		            title: "协议不匹配",
		            content: "不是LINE登录链接，不处理",
		            showCancel: false
		        })
		        return;
		    }
		
		    // 手动解析query参数，替代URLSearchParams兼容低版本5+
		    let token = null;
		    const urlArr = url.split('?');
		    if (urlArr.length > 1) {
		        const queryStr = urlArr[1];
		        const queryList = queryStr.split('&');
		        for (let item of queryList) {
		            const kv = item.split('=');
		            const key = kv[0];
		            const val = kv[1];
		            if (key === 'token' && val) {
		                token = decodeURIComponent(val);
		                break;
		            }
		        }
		    }
		
		    // token为空，登录失败提示
		    if (!token) {
		        uni.showToast({ title: "无有效登录凭证", icon: "none" });
		        return;
		    }
		
		    // 自动存储token，无需用户点击确认
		    uni.setStorageSync('token', token);
		    uni.showToast({ title: "LINE登录成功", icon: "success" });
		
		    // 自动跳转首页
		    setTimeout(() => {
		        uni.reLaunch({ url: "/pages/home" });
		    }, 1000);
		}
		
		// 纯全局取参函数，不用this
		function checkSchemeUrl() {
		    // 稳定获取唤起链接，避开e.data为null的bug
		    const url = plus.runtime.arguments;
		    console.log('读取唤起url：', url);
		    if (url && url.startsWith('dhuam://line/callback')) {
		        handleLineCallback(url);
		        // 清空防止重复触发
		        plus.runtime.arguments = "";
		    }
		}
		
		// 后台唤起 newintent（App挂后台被唤醒）
		plus.globalEvent.addEventListener('newintent', () => {
		    console.log('触发newintent事件');
		    checkSchemeUrl();
		})
		
		// APP彻底关闭冷启动 applaunch（完全杀掉后点击链接唤起）
		plus.globalEvent.addEventListener('applaunch', () => {
		    console.log("触发applaunch冷启动");
		    setTimeout(() => {
		        checkSchemeUrl();
		    }, 300);
		})
		// #endif
     }        
```

### php端接口

```php
<?php

namespace app\api\controller;

use app\common\controller\Api;
use app\common\service\LineAuthService;
use think\Exception;
use think\Log;
use think\Request;


class Line extends Api
{
    // 当前方法无需登录、无需权限校验
    protected $noNeedLogin = ['*'];
    protected $noNeedRight = ['*'];

    protected $lineService = null;

    public function __construct(Request $request = null)
    {
        parent::__construct($request);
        $this->lineService = new LineAuthService();
    }

    /**
     * 获取授权token
     */
    public function getAccessToken()
    {
        try {
            $code = $this->request->param('code');
            if (empty($code)) $this->error('code码是必须的');
            $res = $this->lineService->getAccessToken($code);
            Log::info('line登录记录:' . json_encode($res));
            if (isset($res['access_token'])) {
                // 获取用户资料
                $profile = $this->lineService->getUserInfo(
                    $res['access_token']
                );

                $line_user_id = $profile['sub'] ?? "";
                $line_nickname = $profile['name'] ?? "";
                $line_avatar = $profile['picture'] ?? "";
                $user = \app\admin\model\User::where('line_user_id', $line_user_id)->find();
                //如果为空注册
                if (empty($user)) {
                    $ret = $this->auth->lineRegister($line_user_id,$line_nickname,$line_avatar);
                }
                //登录
                if (!empty($user)) {
                    $ret = $this->auth->lineLogin($line_user_id);
                }

                $userinfo = $this->auth->getUserinfo();

                $token = $userinfo['token'];
                $url = url('/api/line/lineSuccess', [], false, true) . '?token=';
                return redirect($url . $token);

            }

        } catch (Exception $exception) {
            $this->error($exception->getMessage());
        }
        $this->success('授权码返回成功', $res);
    }
    public function lineSuccess()
    {
        $token = $this->request->param('token','');
        return view('',compact('token'));
    }
}

```

#### 回调的前端界面line success.html

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LINE登录成功</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: system-ui, -apple-system, sans-serif;
        }
        body {
            min-height: 100vh;
            background: #f7f8fa;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }
        .card {
            width: 100%;
            max-width: 420px;
            background: #fff;
            border-radius: 16px;
            padding: 40px 30px;
            text-align: center;
            box-shadow: 0 4px 20px rgba(0,0,0,0.08);
        }
        .icon-success {
            width: 80px;
            height: 80px;
            margin: 0 auto 24px;
            background: #36c36c;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 42px;
        }
        h1 {
            font-size: 22px;
            color: #111;
            margin-bottom: 12px;
        }
        .desc {
            color: #666;
            font-size: 15px;
            line-height: 1.6;
            margin-bottom: 24px;
        }
        .token-box {
            background: #f3f4f6;
            padding: 12px 16px;
            border-radius: 8px;
            word-break: break-all;
            font-size: 13px;
            color: #333;
            margin-bottom: 20px;
        }
        .btn {
            display: block;
            width: 100%;
            padding: 14px;
            border-radius: 10px;
            border: none;
            font-size: 16px;
            cursor: pointer;
            margin-bottom: 12px;
        }
        .btn-primary {
            background: #00c300;
            color: white;
        }
        .btn-copy {
            background: #eee;
            color: #222;
        }
        .tip {
            font-size: 13px;
            color: #999;
            margin-top: 16px;
        }
        .count {
            color: #00c300;
            font-weight: bold;
        }
    </style>
</head>
<body>
<div class="card">
    <div class="icon-success">✓</div>
    <h1>LINE授权登录成功</h1>
    <p class="desc">正在自动唤起APP完成登录<br>若未自动跳转，请手动打开应用</p>

    <div class="token-box">登录凭证：{$token}</div>

    <button class="btn btn-primary" id="retryBtn">重新唤起APP</button>
    <button class="btn btn-copy" id="copyBtn">复制登录Token</button>

    <p class="tip">页面将在 <span class="count" id="countNum">10</span> 秒后重试唤起</p>
</div>

<script>
    const token = "{$token}";
    const appScheme = "dhuam://line/callback?token=" + token;
    let countdown = 10;
    const countEl = document.getElementById('countNum');

    // 自动唤起APP
    function openApp() {
        window.location.href = appScheme;
    }
    openApp();

    // 倒计时重试
    const timer = setInterval(() => {
        countdown--;
        countEl.innerText = countdown;
        if (countdown <= 0) {
            openApp();
            countdown = 10;
        }
    }, 1000);

    // 手动重试按钮
    document.getElementById('retryBtn').addEventListener('click', () => {
        openApp();
        countdown = 10;
    });

    // 复制token按钮
    document.getElementById('copyBtn').addEventListener('click', async () => {
        try {
            await navigator.clipboard.writeText(token);
            alert('Token复制成功，请粘贴到APP登录');
        } catch (err) {
            alert('复制失败，请手动复制文本框内凭证');
        }
    });
</script>
</body>
</html>
```

