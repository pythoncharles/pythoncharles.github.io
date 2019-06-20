---
layout: post
category: read
title:  "Form表单获取后台返回数据"
tags: [阅读,前端]
date: 2018-12-10 13:05:25
---

#### 问题描述:
&emsp;&emsp;一般的form表单提交是单向的：只能给服务器发送数据，但是无法获取服务器返回的数据，也就是无法读取HTTP应答包。
想要真正的半双工通讯一般需要使用Ajax, 但是Ajax对文件传输也很麻烦。
<!-- more -->
#### 解决方法
jQuery封装了一个form表单提交有回调功能的方法

导入
jquery.js
jquery-form.js

如下: 一个上传文件的form
```
<form id="form1" action="/shop/updateUserinfo" enctype="multipart/form-data" method="post">
    <table>
        <tr>
            <td>请选择上传文件:</td>
            <td><input type="file" name="image"/></td>
        </tr>
        <tr>
            <td><input type="submit" name="submit" value="提交"/></td>
            <td><input type="reset" name="reset" value="重置"/></td>
        </tr>
    </table>
</form>
```
js实现
```angular2html
// $(function ())是文档document加载完自动调用的函数
$(function () {
    /*
     获取form元素，调用其ajaxForm(...)方法
     内部的function(data)的data就是后台返回的数据
    */
    $("#form1").ajaxForm(function (data) {
            console.log(data);
            console.log("str:" + JSON.stringify(data));
        }
    );
});
```
请求的url在form的action中定义，获取到的后台数据是JSON格式
$(function ())不套在外面，里面的这个请求函数没法自启动
后台代码无差异

#### 注意
&emsp;&emsp;如果仅仅粗糙的使用ajax的话，那就是 $("#submit").ajax(…),　很显然里面的发给后台的数据data很难传输文件。

