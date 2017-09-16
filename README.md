![cover](http://img11.360buyimg.com/n1/jfs/t9235/31/23884526/167954/f14fc1cf/599efe0aN5822da51.jpg)

# 初识移动Web前端
HTML5新特性引入带给移动Web前端新的变化

移动Web与原生应用各有优劣势

一些问题

* 浏览器兼容
* 网速
* 各种繁多的框架


# 移动Web开发环境搭建
**编辑器**

* Visual Studio Code
* Atom
* Sublime
* ...

**Node.js**

npm 命令

* npm install/uninstall
* npm update
* npm outdated - 检测模块过时
* npm ls - 查看安装的模块
* npm init
* npm help
* npm root - 查看包的安装路径
* npm config
* npm cache
* npm start/stop/restart
* npm view
* npm version
* npm test
* npm adduser
* npm publish
* npm access - 在发布的包上设置访问级别

Web代理工具 - [NProxy](https://github.com/hellolwq/nproxy)（看了一下12年后没再更新了）

Http服务器 - [http-server](https://github.com/indexzero/http-server)

# HTML5常用特性
## 新的语义
结构上header、nav、section、article、aside、footer等

表单上，支持更多类型的input，如url email date color number range week month等，支持更多的属性如autofocus form placeholder pattern placeholder required等

其他元素比如有 meter progress

![new html5 attr](https://github.com/swordrain/mobile-web-notes/blob/master/screenshot/new%20html5%20attr.png)

音频和视频的支持

```
<audio controls autoplay loop preload="auto" volumn=1>
	<source src="vincent.ogg" />
	<source src="vincent.mp3" />
	您的浏览器不支持Audio
</audio>

<video width="400" height="300" controls>
	<source src="dizzy.mp4" type="video/mp4" />
	<source src="dizzy.webm" type="video/webm" />
	<source src="dizzy.ogg" type="video/ogg" />
	<p>您的浏览器不支持Video</p>
</video>

//从第5秒开始播放
<source src="dizzy.mp4#t=5" type="video/mp4" />

//从第5秒播放到第10秒
<source src="dizzy.mp4#5,10" type="video/mp4" />

//可以对最后一个source元素绑定error事件，检测是否有可用源，如果没有，进行处理
```

## 设备访问
**地理位置**

```
if(navigator.geolocation){
	navigator.geplocation.getCurrentPosition(
		(position) => {},
		(error) => {},
		options
	)	
} else {
	alert('您的浏览器不支持地理位置获取')
}

position包含latitude longitude accuracy
error包含message code，code的值有UNKNOWN_ERROR(0) PERMISSION_DENIED(1) POSITION_UNAVALIABLE(2) TIMEOUT(3)
options可以设置enableHighAccuracy timeout maximumAge
```

**媒体设置调用**

最初是`navigator.getUserMedia`，现在标准是`navigator.mediaDevices.getUserMedia`，在不同的浏览器上可能需要前缀

```
if(navigator.mediaDevices.getUserMedia || navigator.getUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia) {

} else {
	
}
```

旧版语法

```
getUserMedia(constraints, successCallback, errorCallback)
```

新版语法

```
getUserMedia(constraints).then(successCallback).catch(errorCallback)
```

`constraints`指定请求的媒体类型，包含video和audio，例如

```
{video: true, audio: true}
{video: {width: 640, height: 360}}
{video: {facingMode: 'user'}} //前置摄像头
{video: {facingMode: {exact: 'environment'}}} //后置摄像头
```

调用摄像头显示以及拍照

```
<video id="video" autoplay style="width:480px; height: 320px"></video><!-- 不能自闭合-->
<div>

  <button id="capture">拍照</button>
</div>

<canvas id="canvas" width="480" height="320"></canvas>

<script>
function getUserMedia(constraints, success, error){
  if(navigator.mediaDevices.getUserMedia){
    navigator.mediaDevices.getUserMedia(constraints).then(success).catch(error)
  } else  if(navigator.webkitGetUserMedia){
    navigator.webkitGetUserMedia(constraints, success, error)
  } else if(navigator.mozGetUserMedia){
    navigator.mozGetUserMedia(constraints, success, error)
  } else if(navigator.getUserMedia){
    navigator.getUserMedia(constraints, success, error)
  }
}
var video = document.getElementById("video")
var canvas = document.getElementById("canvas")
var context = canvas.getContext("2d")

function success(stream){
  var CompatibleURL = window.URL || window.webkitURL
  video.src = CompatibleURL.createObjectURL(stream)
  video.play()
}

function error(error){
  console.error(error)
}

if(navigator.mediaDevices.getUserMedia || navigator.getUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia) {
  getUserMedia({video: {width: 480, height: 320}}, success, error)
} else {
   alert("不支持访问用户媒体设备")
}

document.getElementById('capture').addEventListener("click", function(){
  context.drawImage(video, 0, 0, 480, 320)
})

</script>


```

![getUserMedia](https://github.com/swordrain/mobile-web-notes/blob/master/screenshot/getUserMedia.png)

**方向事件和移动事件**

```
window.addEventListener('deviceorientation', e => {
	e.alpha
	e.beta
	e.gamma
}, isAbsolute)

window.addEventListener('devicemotion', e => {
	e.acceleration
	e.accelerationIncludingGravity
	rotationRate
	interval
})
```

##离线和存储

**`.manifest`或`.appcache`文件**

```
CACHE MANIFEST
#cache之后的资源会被离线缓存
CACHE:
main.html
style.css
main.js
#network之后的资源总是从线上获取
account/
```

然后在`<html manifest="./offline.appchca">`引入

**service worker**

旧版有一个`Application Cache`，现已不推荐

Service Worker主要提供四类功能

* 后台消息传递
* 网络代理
* 离线缓存
* 消息推送

```
navigator.serviceWorker.register('sw.js').then(registration=>{
	console.log("service worker 注册成功")
}).catch(error => {
	console.log("service worker 注册失败")
})

//sw.js代码
var cacheFiles = {
	'style.css',
	'main.js'
}

//在install事件里缓存
self.addEventListener('install', evt => {
	evt.waitUntil(
		caches.open('mycache').then(cache => {
			return cache.addAll(cacheFiles)
		})
	)
})
```

[参考](https://www.w3.org/TR/service-workers)

**LocalStorage和SessionStorage**

**IndexedDB**

## 图像效果
**Canvas**

**SVG**

**WebGL(Three.js)**

## 通信
**PostMessage**（可以用作跨域或窗口通信）

```
otherWindow.postMessage(message, targetOrigin, [transfer])
otherWindow - 另一个窗口的引用
message - 将要发送的数据
targetOrigin - 通过窗口的origin属性来指定哪些窗口能接收到消息事件，其值可以是字符“*”或者一个URL
transfer - 一串和message同时传递的Transferable对象，这些对象的所有权将被转移给消息的接收方，而发送一方不再保有所有权，可选

监听消息使用
document.addEventListener('message', (e) => {})
```

**XMLHttpRequest Level 2**

```
var xhr = new XMLHttpRequest()

xhr.timeout = 3000
xhr.ontimeout = (e) => {
	
}

xhr.onload = function(){
	var data = JSON.parse(xhr.responseText)
}
xhr.onerror = function(e){

}
xhr.open('GET', "http://localhost:4412", true)
xhr.send()

//表单
var formData = new FormData()
formData.append('username', "zhangsan")
formData.append('id', 123)
xhr.send(formData)
//文件
var formData = new FormData()
for(var i = 0; i < files.length; i++){
	formData.append('files[]', files[i])
}
//二进制
xhr.open('GET', '/path/to/image.png')
xhr.responseType = 'blob'
//跨域
if(typeof xhr.withCredentials === undefined){
	//不支持跨域
}
//下载
xhr.onprogress = updateProgress
//上传
xhr.upload.onprogress = updateProgress
function updateProgress(e){
	if(e.lengthConputable) {
		var percentComplete = e.loaded / e.total
	}
}

```

**Server Sent Event** (Content-Type是"text/event-stream")

**WebSocket**

**WebRTC**

* MediaDevices - 提供查询和访问媒体输入设备的方法，如getUserMedia
* RTCPeerConnection - 提供建立点对点连接的方法，并维护和监听连接，建立连接的点对点之间可以传输视频流和音频流
* RTCDataChannel - 用于点和点之间双向传输任意数据的网络通道

```
//视频录制
<body>

  <video id="source" autoplay muted></video>
  <video id="recorded" autoplay loop></video>
  <div>
    <button id="record" disabled>开始录制</button>

  </div>
  <script src="https://webrtc.github.io/adapter/adapter-latest.js"></script>
  <script>
    var mediaSource = new MediaSource()
    //添加媒体数据源打开时的监听
    mediaSource.addEventListener('sourceopen', handleSourceOpen, false)
    var mediaRecorder, recordedBlobs, sourceBuffer
    var sourceVideo = document.getElementById("source")
    var recordedVideo = document.getElementById("recorded")
    var recordButton = document.getElementById("record")
    recordButton.onclick = toggleRecording

    var constraints = {audio: true, video: {width: 320}}
    function handleSuccess(stream){
      recordButton.disabled = false
      window.stream = stream
      sourceVideo.srcObject = stream
    }
    function handleError(error){
      console.error(error)
    }
    //获取用户媒体
    navigator.mediaDevices.getUserMedia(constraints).then(handleSuccess).catch(handleError)
    function handleSourceOpen(event){
      sourceBuffer = mediaSource.addSourceBuffer('video/webm; codecs="vp8"')
    }
    function handleDataAvailable(event){
      if(event.data && event.data.size > 0){
        recordedBlobs.push(event.data) //追加数据
      }
    }
    function toggleRecording(){
      if(recordButton.textContent === '开始录制'){
        startRecording()
      } else {
        stopRecording()
        recordButton.textContent = '开始录制'
      }
    }

    function startRecording(){
      recordedBlobs = []
      var mimeTypes = [
        'video/webm;codecs=vp9',
        'video/webm;codecs=vp8',
        'video/webm'
      ]
      //查找支持视频格式
      var mimeType = mimeTypes.find(type=>MediaRecorder.isTypeSupported(type) || '')
      try{
        mediaRecorder = new MediaRecorder(window.stream, {mimeType})
      }catch(e){
        alert('创建媒体录制异常' + options.mimeType)
        return
      }

      recordButton.textContent = '停止录制'
      mediaRecorder.ondataavailable = handleDataAvailable
      mediaRecorder.start(10)
    }

    function stopRecording(){
      mediaRecorder.stop()
      var buf = new Blob(recordedBlobs, {type: 'video/webm'})
      recordedVideo.src = window.URL.createObjectURL(buf)
    }

  </script>
</body>
```

##其他常用特性

**History API**(pushState replaceState window.addEventListener('popstate', (e)=>{}))

**Drag&Drop**

**Web Workers**

**Performance API**

#CSS3
CSS3各模块详情[https://www.w3.org/Style/CSS/current-work](https://www.w3.org/Style/CSS/current-work)

查询各属性在浏览器支持情况[http://caniuse.com/](http://caniuse.com/)

使用Modernizr[https://modernizr.com](https://modernizr.com)检测浏览器支持CSS3

##选择器
常用选择器

* *
* E
* .class
* \#id
* E[attr]
* E[attr=val]
* E[attr~=val]
* E[attr|=val]
* E[attr^=val]
* E[attr$=val]
* E[attr*=val]
* E F
* E > F
* E + F
* E ~ F
* S1, S2, ...

伪类和伪元素

* :link
* :visited
* :hover
* :active
* :focus
* :target
* :lang(val)
* :enabled
* :disabled
* :checked
* :root
* :nth-child(n)
* :nth-last-child(n)
* :nth-of-type(n)
* :nth-last-of-type(n)
* :first-child
* :last-child
* :first-of-type
* :last-of-type
* :only-child
* :only-of-type
* :empty
* :not(s)
* ::first-line
* ::first-letter
* ::before
* ::after

##响应式开发
* 物理像素
* 逻辑像素
* 像素比 - window.devicePixelRatio
* viewport
	* width
	* initial-scale
	* minimum-scale
	* maximum-scale
	* height
	* usr-scalable
* flex布局
* media query


