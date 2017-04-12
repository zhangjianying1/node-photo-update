# 图片批量压缩后批量上传
#### 前端采用jquery的ajax，利用XMLHttpRequest可以发送二进制的功能将要上传的图片经过canvas压缩后上传到服务器
> 前端 用到 Uini8Array Blob FormData等
#### 后端采用express框架通过第三方中间件进行多张图片进行存储
>multer第三方插件 npm install multer进行安装 var multer = require('multer'); //这是一个Node.js的中间件处理multipart/form-data
var upload = multer({dest:'./tmp'}); // 图片临时的存储路径
·var express = require('express');
var router = express.Router();
var movieDao = require('../../dao/movieDao.js');
var fs = require('fs');
var multer = require('multer'); //这是一个Node.js的中间件处理multipart/form-data
var upload = multer({dest:'./tmp'});


/* GET home page. */
router.get('/', function(req, res, next) {
	res.render('movieadd', { title: 'Express' });
});

router.post('/', upload.array("file"), function(req, res, next) {

	//res.setHeader("Content-Type","text/plain");
	//res.setHeader("Access-Control-Allow-Origin","http://localhost:3000");



	if(req.files==undefined){
		res.json({status: 200, msg: "请选择要上传的图片..."});
	}else{
		var str="文件上传成功...";

		for(var i=0;i<req.files.length;i++){

			var filepath = "./upload/" + (Math.random()) +  '.' + req.files[i].mimetype.substring(6);



			fs.renameSync(req.files[i].path, filepath);
		}
		res.json({status: 200, msg: "上传的图片成功..."});
	}

	//movieDao.insert(req, res, next);
	//res.render('movieadd', { title: 'Express' });
});

module.exports = router;·

