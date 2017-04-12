# 图片批量压缩后批量上传
#### 前端采用jquery的ajax，利用XMLHttpRequest可以发送二进制的功能将要上传的图片经过canvas压缩后上传到服务器
> 前端 用到 Uini8Array Blob FormData等
`
var sendData = [];
$('#photo').on('change', function(e) {
	var file = this.files;
	
	for (var i = 0; i < file.length; i ++) {

		getBase64Image(file[i], send);
	}

	function send(data){

		sendData.push(data);

	}


});`
`
$('form').on('submit', function(e){

	e.preventDefault();


	var formdata= new FormData();


	for (var i = 0; i < sendData.length; i ++) {
	
		var blob= dataURLtoBlob(sendData[i]);

		formdata.append('file',blob);
	}

	$.ajax({
	
		url : '/movieadd',
		data :  formdata,
		processData : false,
		contentType : false,
		dataType: 'json',
		type : "POST",
		success : function(data){

			alert('图片上传成功')
		}
	});
	function dataURLtoBlob(dataurl) {
	
		var arr = dataurl.split(','), mime = arr[0].match(/:(.*?);/)[1],
				bstr = atob(arr[1]), n = bstr.length, u8arr = new Uint8Array(n);
		while(n--){

			u8arr[n] = bstr.charCodeAt(n);
		}

		return new Blob([u8arr], {type:mime});
	}

})`
`
function getBase64Image(file, fn) {

	var fr = new FileReader();
	var count = 0;

	fr.onload = function(e) {
	
		var baseCode = e.target.result;
		var image = null;

		// 压缩
		if(file.size>1024){
		
			image = new Image();
			image.src = baseCode;
			image.onload = function() { //创建一个image对象，给canvas绘制使用
			
				var canvas = document.createElement("canvas");
				canvas.width = this.width;
				canvas.height = this.height; //计算等比缩小后图片宽高
				var ctx = canvas.getContext('2d');
				ctx.drawImage(this, 0, 0, canvas.width, canvas.height);

				var baseCode = canvas.toDataURL(file.type, 0.8); //重新生成图片

				fn(baseCode);

			}

		}else {


				fn(baseCode);




		}


	};

	fr.readAsDataURL(file);

}
`
#### 后端采用express框架通过第三方中间件进行多张图片进行存储
>multer第三方插件 npm install multer进行安装
`
var express = require('express');
var router = express.Router();
var movieDao = require('../../dao/movieDao.js');
var fs = require('fs');
var multer = require('multer'); //这是一个Node.js的中间件处理multipart/form-data
var upload = multer({dest:'./tmp'});


`
`
router.get('/', function(req, res, next) {

	res.render('movieadd', { title: 'Express' });
});
`
`
router.post('/', upload.array("file"), function(req, res, next) {

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

module.exports = router;`

