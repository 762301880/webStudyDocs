# 1.在js中使用的语句

## 1.1返回上一个页面并且刷新2020-03-27

```javascript
self.location=document.referrer
```

 

## 1.2 直接跳转界面

```javascript
 window.location.href="你要跳转的地址"
```

## 1.3返回上一个

```javascript
onclick="window.history.go(-1)"
```

### 1.4 更改父窗口的位置。

```javascript
window.parent.close();

window.parent.open("about:blank","_self").close() ;

window.parent.location.href="{:url('您需要跳转的地址')}";
```





 