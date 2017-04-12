# 图片批量压缩后批量上传
#### 前端采用jquery的ajax，利用XMLHttpRequest可以发送二进制的功能将要上传的图片经过canvas压缩后上传到服务器
> 前端 用到 Uini8Array Blob FormData等
#### 后端采用express框架通过第三方中间件多多张图片进行存储
>multer第三方插件 npm install multer进行安装 var multer = require('multer'); //这是一个Node.js的中间件处理multipart/form-data
var upload = multer({dest:'./tmp'}); // 图片临时的存储路径

