#  说明

> 由于这次的项目需要前后端一起写所以 记录一下自己研究的模块

# 代码示例

## 前端代码示例

***前端采用layui***,**文件上传**[组件](https://www.layui.com/doc/modules/upload.html)

```php+HTML
 <div class="layui-form-item">
                <label for="username" class="layui-form-label">
                    身份证
                </label>
                <div class="layui-upload">
                    <input type="hidden" value="" name="o2o_distributor_identify_image" id="image_id_card">
                    <button type="button" class="layui-btn" id="upload_id_card">上传身份证图片</button>
                    <div class="layui-upload-list">
                        <img class="layui-upload-img" style="width: 200px;height: 100px" id="img_id_card_src"
                             src="{$o2oDistributor->o2o_distributor_identify_image}">
                    </div>
                </div>
</div>
 <!--js-->
<script>  
var uploadIdCard = upload.render({
            elem: '#upload_id_card'
            , url: "{:url('common/upload_image')}" //此处用的是第三方的 http 请求演示，实际使用时改成您自己的上传接口即可。
            , before: function (obj) {
                //预读本地文件示例，不支持ie8
                obj.preview(function (index, file, result) {
                    $('#img_id_card_src').attr('src', result); //图片链接（base64）
                });
            }
            , done: function (res) {
                console.log(res)
                //如果上传失败
                if (res.code <= 0) {
                    return layer.msg(res.msg);
                }
                //上传成功的一些操作
                # 将ajax返回过来的url传递给当前input输入框
                $('#image_id_card').attr('value', res.url)
                return layer.msg(res.msg);
            },
        });
</script>                                 
```

## 后端逻辑示例

- 图片上传逻辑(TP)

```php
# 个人封装的图片上传逻辑
 /**
     * 2021 年/8/23
     * 由于不知道上传图片的接口所以造了个轮子
     * author 姚留洋
     * @param Request $request
     */
    public function uploadImage(Request $request)
    {
        $image = $request->file('file');
        if ($image) {
            if (in_array($image->getOriginalExtension(), ['png', 'jpg', 'jpeg', 'gif', 'svg'])) {
                if (floor($image->getSize() / (1024 * 1024) > 6)) {
                    $this->error('上传图片大小必须在6M以内');
                }
            }
            // 调用方法体
            $result = $this->uploadFile($request);
            $this->success('上传成功', $result, '');
        }
    }

    public function uploadFile(Request $request)
    {

        $fileName = uniqid().'.'.$request->file('file')->getOriginalExtension();
        //截取url
        $url=$request->domain();
        $str_num_start=strpos($url,':')+1;//查询冒号开始出现的位置(从0开始)
        $str_num_end=strpos($url,'com')+2;//查询com的m结束位置(从0开始)
        $url=substr($url,$str_num_start,$str_num_end);
        //截取url-end
        $path =   'uploads/admin/' . date('Y-m-d');
        $isMove = $request->file('file')->move(public_path().$path,$fileName);
        return $url.'/'.$path.'/'.$fileName;
    }
```

> 说明具体的代码后端逻辑比较简单所以就不展示了