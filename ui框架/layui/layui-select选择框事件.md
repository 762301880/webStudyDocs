author: yaoliuyang

created_at:2021/10/9

# 说明

> 本来采用jquery+layui 开发一个不分离的后台项目遇到了需要读取select
>
> 选择框事件，不知道为啥layui 框架无法采用jquery 的形式获取，select 当前
>
> 选择的值，好在百度有答案

# 代码示例

## layui-js写法

```js
  <select lay-filter="test"></select>
      
  //js    
  layui.use(['form', 'layedit', 'laydate'], function () {
        var form = layui.form # 引入form表单

        form.on('select(selectType)', function (data) {
         console.log(data.elem); //得到select原始DOM对象
	     console.log(data.value); //得到被选中的值
	     console.log(data.othis); //得到美化后的DOM对象
        });
    })
```

## 原生jquery写法

> 选择对应的下拉框后会弹出选择的值

```js
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8"> 
<title>select 选择框事件</title>
<script src="https://cdn.staticfile.org/jquery/1.10.2/jquery.min.js">
</script>
<script>
$(document).ready(function(){
  $(".field").change(function(){
   alert($(this).val())
  });
});
</script>
</head>
<body>
<p>汽车匹配:
<select class="field" name="cars">
<option value="volvo">Volvo</option>
<option value="saab">Saab</option>
<option value="fiat">Fiat</option>
<option value="audi">Audi</option>
</select>
</p>
<p>在下拉列表选择一个选项。</p>
</body>
</html>
```

