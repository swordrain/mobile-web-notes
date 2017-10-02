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

其他元素比如有 meter progress datal datalist(输入选择框)

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

video.currentTime = 10 //跳到第10秒播放
video.playbackRate = 2 //播放速率
video.muted = true //静音

//字幕 .vtt格式
<video width="600" poster="coverimg.jpg">
	<!-- 指定视频格式和视频地址 -->
	<source type="video/mp4" src="http://f6.c.hjfile.cn/qiniu/classzt/20160803/90614_video_2.mp4" />
	<!-- 指定视频字幕 -->
	<track kind="subtitles" src="webvtt.vtt" default />
</video>
```

video事件

* abort
* canplay
* canplaythrough
* durationchange
* emptied
* ended
* error
* loadeddata
* loadedmetadata
* loadstart
* pause
* play
* playing
* progress
* ratechange
* seeked
* seeking
* stalled
* suspend
* timeupdate
* volumnchange
* waiting

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

* LocalStorage和SessionStorage
* IndexedDB

## 图像效果
* Canvas
* SVG
* WebGL(Three.js)

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

* History API(pushState replaceState window.addEventListener('popstate', (e)=>{}))
* Drag&Drop
* Web Workers
* Performance API

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
	* all
	* print
	* screen
	* speech
	* width
	* height
	* aspect-ratio
	* orientation
	* resolution
	* max/min前缀
* rem(方便起见，用宽度/10作为根元素的字体大小，这样宽度为10rem)
* [Flexiable](https://github.com/amfe/lib-flexible)
* multi column

```
//动态设置rem
(function(){
	var de = document.documentElement
	funciton resetFontSize(){
		var w = de.getBoundingClientRect().width
		if(w > 640) w = 640
		de.style.fontSize = (w / 10) + 'px'
	}
	resetFontSize()
	window.addEventListener('resize', resetFontSize, false)
})()
```
##动效
* transform
	* translate
	* scale
	* rotate
	* skew
	* matrix
	* perspective
* transition
	* transition-property
	* transition-duration
	* transition-timing-function
		* linear
		* ease
		* ease-in
		* ease-out
		* ease-in-out
		* cubic-bezier
	* transition-delay
* animation
	* animation-name
	* animation-duration
	* animation-timing-function
	* animation-delay
	* animation-iteration-count
	* animation-direction

##常用特性
###开放字体格式
```
@font-face {
	font-family:
	src:
	[font-weight]:
	[font-style]:
}
```

支持的格式如下

* .ttf
* .otf
* .woff
* .woff2
* .eot
* .svg

兼容性语法

```
@font-family {
	font-family: '字体名';
	src: url('字体名.eot'); /*IE9*/
	src: url('字体名.eot#iefix') format('embeded-opentype'), /*IE6-IE8*/
	url('字体名.woff') format('woff'),/*Modern Browser */
	url('字体名.ttf') format('truetype'),/*Safari, Android, iOS */
	url('字体名.svg#字体名') format('svg'); /*Legacy iOS*/
}
```

###背景
* background-color
* background-position
* background-size (length / percentage / cover / contain)
* background-repeat
* background-origin (padding-box / border-box / content-box)
* background-clip (border-box / padding-box / content-box)
* background-attachment
* background-image

background-clip

![background-clip](https://github.com/swordrain/mobile-web-notes/blob/master/screenshot/background-clip.png)

background-origin

![background-clip](https://github.com/swordrain/mobile-web-notes/blob/master/screenshot/background-origin.png)


###color
* Color Name
* HEX
* RGB
* RGBA
* HSL
* HSLA
* transparent
* **currentcolor**(当前继承的文字颜色)
* gradient
* opacity

###文字效果
* text-shadow
* text-overflow
* word-wrap
* word-break

###边框
* border
* border-radius
* border-image
* box-shadow

##预编译
* LESS
* SASS
* Compass

#JavaScript
##语法基础
##事件
###事件概述
* 事件类型 event type
* 事件目标 event target
* 事件处理程序 event handler
* 事件对象 event object
* 事件传播 event propagation

###事件委托
```
//DOM0
document.querySelector('#div').onclick = function(e){}
document.querySelector('#div')["onclick"] = function(e){}
//DOM2
document.querySelector('#div').addEventListener('click', function(e){}, false) //true - 捕获 false - 冒泡
```

可以委托到父节点，通过e.target判断触发元素，e.currentTarget表示事件代理的元素

###移动端事件
**触摸事件**
* touchstart
* touchmove
* touchend
* touchcancel

event里包含

* touches - 当前跟踪的触摸操作touch对象的集合
* targetTouches - 当前事件目标上touch对象的集合
* changeTouches - 表示至上次触摸发生了改变的touch对象的集合

每个touch对象包含

* clientX - 在视口中的X坐标
* clientY
* pageX - 在页面中的X坐标
* pageY 
* screenX - 在屏幕中的X坐标
* screenY
* target - 触摸的DOM节点

**手势事件**

* gesturestart
* gesturechange
* gestureend

event里除了包含基础的坐标信息，还有

* scale - 缩放比例
* rotation - 旋转角度

**传感器事件**

* deviceorientation
* devicemotion
* orientationchange

deviceorientation的event包含

* alpha
* beta
* gamma

devicemotion的event包含
* acceleration
* accelerationIncludingGravity
* interval
* rotationRate

orientationchange监听方向变化，通过screen.orientation.angle获取方向，0和180表示竖屏，90和270表示横屏，iOS中通过window.orientation获取方向，0和180表示竖屏，90和-90表示横屏

##作用域、闭包和this

##对象

##异步编程
* callback
* promise
* generator
* async/await

##模块化

##ES6

#移动网页样式布局
##静态布局
整体缩放或使用媒体查询

##居中

##栅格

##Flex布局
##动画优化

#前端工程化
1. 规范代码
2. js预处理
3. css预处理
4. 自动编译
5. 缩减文件体积
6. ...

* Grunt
* Gulp
* Wewbpack



#移动Web常用开发方式
* 基于DOM(Zepto)
* React
* Vue
* SPA架构

禁用原生单击300ms延迟

1. viewport禁用缩放`<meta name="viewport" content="user-scalable=no" />`
2. 设备宽度`<meta name="viewport" content="width=device-width" />`
3. 指针事件 - 将元素的`touch-action`设置为`none`或者`manipulation`可以禁用双击缩放，从而去除延迟
4. iOS8以后以125ms为界，分为Fast Tap和Slow Tap，Fast Tap仍然认为是双击缩放
5. FastClick库，通过MouseEvents自定义鼠标事件并立即触发事件响应


#混合开发
* WebView模式
* JavaScriptCore模式
* 微信小程序
* Flutter

Nativa调用Web

```
//Android
webView.loadUrl("javascript:(function(){alert('Hello')})()")
//Swift
webView.stringByEvaluatingJavaScriptFromString(from: "alert('hello')")
//iOS的WKWebView
wkWebView.evaluateJavaScript("alert('hello')"){ (item, error) in 
	...
}
//JSContext
let context: JSContext = JSContext()
context.evaluateScript("1+1")
```

Web调用Native

Android

* 重写WebViewClient.shouldOverrideUrlLoading
* 重写WebChromeClient.onJsPrompt、onJsConfirm、onJsAlert
* WebView.addJavascriptInterface

iOS

* shouldStartLoadWithRequest
* JavaScriptCore提供原生代码被JS调用需要先定义遵守JSExport协议的JavaScriptDelegate协议

```
@objc protocol SeiftJavaScriptDelegate: JSExport{
	...
}

@objc class SwiftJavaScriptModel: NSObject, SwiftJavaScriptDelegate {
	weak var controller: UIViewController?
	weak var jsContext: JSContext?
	...

}
```

**Cordova**

**React Native**

#前端开发调试

* 浏览器Chrome/Safari
* 抓包工具Charles/Fiddler
* 多终端同步工具BrowserSync/Emmet LiveStyle
* Android在线模拟器Manymo
* 响应式测试工具Ghostlab
* 移动端Web调试工具Weinre
* JavaScript远程调试工具Vorlon.js
* 云真机调试BrowserStack
* Web端移动设备管理控制工具Smartphone Test Farm(STF)
* 多浏览器兼容性测试平台F2etest
* React调试React Developer Tools/Redux DevTools

#前端单元测试
* Jasmine
* Mocha/Chai
* Sinon
* Karma
* Istanbul
* Benchmark.js基准测试

#前端性能优化
##常用网站性能优化指标
网页的资源请求和加载阶段减少资源访问及加载阶段所消耗的时间。

网页渲染阶段避免JS阻塞（把JS放底部），避免重新布局（必然引起重新绘制和合并，但一些特殊属性如opacity或transform可以在不同层单独绘制，而是直接执行合并图层，并且是在GPU的硬件渲染中）

加快JS脚本的执行速度

##Yahoo性能优化法则

##性能优化工具
* YSlow
* PageSpeed
* WebPagetest

##Http协议头缓存

##资源按需加载
* 基于RequireJS的按需加载
* 基于Webpack的按需加载
* 图片懒加载

```
window.addEventListener('scroll', loadImage, false)
var imgList = Array.property.slice.call(document.querySelectorAll('img'))
function loadImage(){
	for(var i = 0; i <= imgList.length; i++){
		var el = imgList[i]
		if(isShow(el)){
			el.src = el.getAttribute('data-src')
			imgList.splice(i, 1)
		}
	}
}
function isShow(el){
	var rect = el.getBoundingClientRect()
	var isAppear = rect.right > 0 && rect.left < window.innerWidth && rect.bottom > 0 && rect.top < window.innerHeight
	return isAppear
}
```

##不同网络类型的优化实战
```
navigator.onLine
window.addEventListener('online', ()=> {})
window.addEventListener('offline', ()=>{})
//navigator.connection还未得到广泛支持

```
## Nginx配置Combo合并Http请求

![cover](http://img14.360buyimg.com/n1/jfs/t7660/264/1450357776/147826/93184d8e/599d28caN2188ee08.jpg)

# 移动页面开发
## 页面布局
###Viewport

默认的Viewport为980px

* 逻辑像素(CSS像素)
* 物理像素
* PPI
* DPR

DPR是物理像素和逻辑像素的比例

```
var dpr = window.devicePixcelPixelRatio

//CSS中的媒体查询可以通过如下属性适配(可能需要前缀)
device-pixel-ratio
min-device-pixel-ratio
max-device-pixel-ratio

```

查询像素数据[http://screensiz.es](http://screensiz.es)

**3个Viewport**

1.Layout Viewport

默认的980px的Viewport，在JS中通过如下代码获取

```
document.documentElement.clientWidth
document.documentElement.clientHeight
```
![layout viewport](http://images2015.cnblogs.com/blog/211606/201602/211606-20160218164648378-347236300.jpg)


2.Visual Viewport

浏览器或App Webview中的可视区域，根据缩放改变，通过如下代码获取

```
window.innerWidth
window.innerHeight
```
![visual viewport](http://images2015.cnblogs.com/blog/211606/201602/211606-20160218163818644-33534893.jpg)

3.Ideal Viewport

理想的像素

* iPhone 4~5 320px
* iPhone 6 375px
* iPhone 6+ 414px
* Android 360px

```
//一般设置等于屏幕宽度
window.screen.width
```

**设置Viewport**

```
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0" >

//如果只是把Layout Viewport设为设备宽度，以下代码即可
<meta name="viewport" content="width=device-width;" >
<meta name="viewport" content="initial-sscal=1.0;" >

//或者通过JS
document.write('<meta name="viewport" content="width=变化值">')

var vp = document.getElementById('viewport')
vp.setAttribute('content', 'width=变化值')

```
###布局形式
* 传统布局
* 滑屏布局

###Media Query
两种方式

```

@media screen and (max-width: 320px) {
	background: #FFFFFF;
}

<link rel="stylesheet" media="screen and (max-width: 320px)" href="ip5.css" />
```

可供选择的媒体条件有

| 属性  | 值 | Min/Max  |
| ---- |----| ----|
| device-aspect-ratio| 整数 | yes |
| device-height| 长度单位 | yes |
| device-width| 长度单位 | yes |
| height| 长度单位 | yes |
| width| 长度单位 | yes |
| resolution| 分辨率 | yes |
| orientation| Portrait/Landscape | no |
| grid| 整数 | no |
| color-index| 整数 | yes |
| color| 整数 | yes |

其他非屏幕的媒体类型

* all
* braille
* embossed
* handled
* print
* projection
* screen
* speech
* tty
* tv

常见的断点设置

```
@media screen and (max-width: 320px){ /*iPhone 6之前的设备*/}
@media screen and (max-width: 360px){ /*Android设备*/}
@media screen and (max-width: 375px){ /*iPhone 6屏幕的设备*/}
@media screen and (max-width: 414px){ /*iPhone 6p屏幕的设备*/}
@media screen and (max-width: 768px){ /*iPad竖屏*/}
```

```
@media screen and (orientation: portrait) {}
@media screen and (orientation: landscape) {}
```

```
@media screen and (device-pixel-ration: 2){}
```

###屏幕适应

**百分比布局**

W(元素当前百分比宽度) / padding-bottom = 图片真实宽度 / 图片真实高度

> 百分比的margin和padding都是以父元素的宽度为基础进行计算，当排版变为`-webkit-writing-mode: vertical-lr`时，是以父元素的高度为基础

**缩放法**

以640px作为设计基础，计算设备宽度和640px的比例，进行缩放

缩放有zoom和tranform的scale两种。zoom缩放后容器占据的空间跟着一起缩放，而scale缩放后容器占据的空间仍然不变，所以一般选择zoom缩放

```
(function(fixWidth, id){
	var elm = document.getElementById(id)
	function setZoom(){
		var cliWidth = document.documentElement.clientWidth || document.body.clientWidth
		var blWidth = cliwidth / fixWidth
		elm.style.zoom = blWidth
	}
	window.addEventListener('resize', setZoom, false)
	setZoom()
})(640, 'wrap')
```

**REM自适应**

```
(function(){
	if(!window.addEventListener) return;
	var html = document.documentElement
	function setFont(){
		var cliWidth = html.clientWidth
		html.style.fontSize = 100 * (cliWidth/640) + 'px'
	}
	document.addEventListener('DOMContentLoaded', setFont, false)
})()
```

###内容排布
**视频和iFrame的自适应**

对于16：9的视频，设置`padding-bottom`为56.25%(设置width和height都为100%)

**水平垂直居中**


##页面调试
* Chrome开发工具(用chrome://inspect查看连接的设备)
* iOS设备开启(Safari - 高级 - Web检查器)，然后在Safari的开发菜单里找到连接的设备

##常用库和框架

* jQuery Mobile
* Zepto
* Cocos2d
* CreateJS(用FlashIDE可以导出代码)

#技术创意形式
##动画形式
###CSS3
* transform
* transition
* animation

###帧动画
* 通过CSS3`timing-function`的`step()`函数控制
* 通过JS控制Canvas
* 通过JS控制CSS

###Canvas

###SVG

```
//描边动画
@keyframes stroke {
	0% {
		stroke-dasharray: 0, 5000;
		stroke-dashoffset: 0;
	}
	100% {
		stroke-dasharray: 5000, 5000;
		stroke-dashoffset: 0;
	}
}
```

```
//从直角三角形变为等腰三角形
@keyframes path{
	0% {
		d:path('M 200 100 L 300 100 L 200 300 Z');
	}
	100% {
		d:path('M 100 100 L 300 100 L 200 300 Z');
	}
}
//通过d:path还以实现复杂的曲线变形动画
```

###Three.js

##移动设备Web API详解
### Video
```
<video src="movie.mp4" controls>
	不支持video
</video>
```

相关属性

* autoplay (在iOS和Android原生浏览器默认都不支持)
* playsinline
* preload
* poster (封面，在iOS中支持，可以用一张图片覆盖来自行实现)

相关事件

* play
* progress
* canplay
* canPlayThrough
* playing
* timeUpdate

###Audio
mp3 wav能够在iOS和Android上运行，ogg只支持Android，其他格式wma、aac、flac、m4a可能有体积过大、兼容性或音质等问题

```
<audio controls>
	<source src="music.mp3" type="audio/mp3">
</audio>

<audio src="music.mp3" controls></audio>

```

相关属性

* autoplay
* controls
* loop
* preload
* src

相关事件

* abort
* canplay
* canplaythrough
* durationchange
* emptied
* ended
* error
* loadeddata
* loadedmetadata
* loadstart
* pause
* play
* playing
* progress
* ratechange
* seeking
* seeked
* stalled
* suspend
* timeupdate
* volumnchange
* waiting

测试audio API的[例子](http://tgideas.qq.com/book/danceonfingers/chapter2/section2/audio_api/)

###getUserMedia
Android4.4以上，部分浏览器或Webview需要https协议才能使用该API

```
//摄像头捕捉
navigator.webkitGetUserMedia({video: true}, function(stream){
	video.src = window.URL.createObjectURL(stream)
	localMediaStream = stream
}, function(e){

})

//从视频流中拍照
btnCapture.addEventListrner("touchend", function(){

	if(localMediaStream){
		canvas.setAttribute('width', video.videoWidth)
		canvas.setAttribute('height', video.videoHeight)
		ctx.drawImage(video, 0, 0)
	}
}, false)

//用户声音录制
navigator.getUserMedia({audio: true}, function(e){
	context = new audioContext()
	audioInput = context.createMediaStreamSource(e)	volumn = context.createGain()
	recorder = context.createScriptProcessor(2048, 2, 2)
	recorder.onaudioprocess = function(e){
		recordingLength += 2048
		recorder.connect(context.destination)
	}	
}, function(err){

})

//保存用户录制的声音
var buffer = new ArrayBuffer(44 + interleaved.length * 2)
var view = new DataView(buffer)
fileReader.readAsDataURL(blob) // android chrome audio不支持blob
audio.src = event.target.result
```

###Web Speech
* SpeechRecognition实现语音识别
* SpeechSynthesis实现文本转语音合成
* SpeechGrammar实现自定义语法创建

| 名称  | iOS6+ | Android5.0+  |
| ---- |----| ----|
| SpeechRecognition | 不支持 | 不支持 |
| SpeechSynthesisUtterance | 支持 | 支持 |
| SpeechGrammar | 不支持 | 不支持 |

SpeechSynthesisUtterance的内容

| 名称  | 说明 | iOS6+  |
| ---- |----| ----|
| voiceURI | 语音地域 | 不支持 |
| volumn | 调整音量 | 支持 |
| rate | 设定语速 | 支持 |
| pitch | 设定语调 | 支持 |
| text | 语音的内容 | 支持 |
| lang | 语言、语系-地区码 | 支持 |

测试Web Speech API的[例子](http://tgideas.qq.com/book/danceonfingers/chapter2/section2/webspeech/)

###Web Audio
对音频数据进行分析处理，iOS6+和Android4.4+

测试Web Speech API的[例子](http://sy.qq.com/brucewan/device-api/web-audio.html)

```
//核心代码
var context = new webkitAudioContext()
var source = context.createBufferSource() //创建一个声音源
source.buffer = buffer //告诉该源播放何物
createBufferSourcesource.connect(context.destination) //将该源与硬件连接
source.start(0) //播放
```

两种方式加载音频 

```
//audio标签
var sound, audio = new Audio()
audio.addEventListener('canplay', function(){
	sound = context.createMediaElementSource(audio)
	sound.connect(context.destination)
})

//XMLHttpRequest
var sound, context = createAudioContext()
var audioURI = '/audio.mp3'
var xhr = new XMLHttpRequest()
xhr.open('GET', audioURL, true)
xhr.responseType = 'arraybuffer'
xhr.onload = function(){
	context.decodeAudioData(request.response, function(buffer){
		source = context.createBufferSource()
		source.buffer = buffer
		source.connect(context.destination)
	})
}
```

分析

```
var audioCtx = new (window.AudioContext || window.webkitAudioContext)()
var analyser = audioCtx.createAnalyser()
analyser.fftSize = 2048
var bufferLength = analyser.frequencyBinCOunt
var dataArray = new Uint8Array(bufferLength)
analyser.getByteTimeDomainData(dataArray)
function draw(){
	drawVisual = requestAnimationFrame(draw)
	analyser.getByteTimeDomainData(dataArray)
	//other codes
}
draw()
```

处理节点，[参考](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Audio_API)

###Geolocation API
兼容iOS6.0以上和Android2.3以上，iOS10需要https协议才能使用该API

`getCurrentPosition()`用于获取一次地理位置，`watchPosition()`用于持续获取地理位置，直到`clearWatch()`取消

第一个回调方法`positionCallback`返回对象中重要的参数

* coords.accuracy
* coords.altitude
* coords.altitudeAccuracy
* coords.heading
* coords.latitude
* coords.longtitude
* coords.speed
* timestamp

第二个回调方法`positionErrorCallback`三类错误信息

* PERMISSION_DENIED  - 错误代码1
* POSITION_UNAVAILABLE - 错误代码2
* TIMEOUT - 错误代码3

第三个回调方法`positionOptions`用于设置数据获取的方式，是一个固定格式的JSON

* enableHighAcuracy - 是否启动高精度模式
* maximumAge - 设置定位的缓存过期时间，0为禁用缓存
* timeout - 设置获取定位信息时长

```
var option = {
	enableHighAccuracy: true,
	maximumAge: 0,
	timeout: 30000
}
if(navigator.geolocation){
	navigator.geolocation.getCurrentPosition(showPosition, showError, option)
}

function showPosition(position){
	var lat = position.coords.latitude
	var lon = position.coords.longitude
	
}

function showError(error){
	switch(error.code){
		case error.PERMISSION_DENIED:
		
		break
		case error.POSITION_UNAVAILABLE:
		
		break
		case error.TIMEOUT:
		
		break
	}
}
```

###陀螺仪
deviceOrientation API，deviceOrientation事件的回调会返回DeviceOrientationEvent Type的对象，包含alpha beta gamma三个属性

alpha范围是0~360，beta范围是-180~180，gamma范围是-90~90

deviceMotion API，deviceMotion事件会返回DeviceMotionEvent Type的对象，包含acceleration accelarationIncludingGravity rotationRate三个属性

###设备震动
iOS不支持，Android在4.4版本以上支持

```
navigator.vibrate(2000)
navigator.vibrate([1000, 500, 1000])
```

###电池状态
```
var battery = navigator.battery||naivgator.webkitBattery||navigator.mozBattery||navigator.msBattery
if(batter){

}
```

Battery API常用属性

* level
* dischargingTime
* chargingTime
* charging

事件

* chargingchange
* chargingtimechange
* dischargingtimechange
* levelchange

###环境光
Ambient Light，提供一个devivelight事件监听，返回event.value表示环境光的强度，单位是lux，目前只有Android的Firefox支持

```
if('ondevicelight' in window){
	window.addEventListener('devicelight', e => {
	
	})
}
```

###网络信息
一种是通过navigator的online、offline属性，或者通过`window.addEventListener('online/offline', e=>{})`，一种是通过Network Information API里的navigator.connection对象，对象有`connection.type`值表示网络连接的类型，也可以通过`connection.addEventListener('typechange', e=>{})`监听

##WebVR
常用解决方案

* A-frame
* ThreeJS

##创意点
* 基于微信录音接口
* 基于微信语音识别
* 基于摄像头和相册
* 基于人脸识别
* 基于陀螺仪
* 基于手势
* 基于Websocket

#页面性能优化
##资源优化
###图像
**用CSS属性减少使用图像**


**图像格式的选择**

| 图片格式  | 压缩方式 | 透明度 | 动画 | 应用范围 |
| ---- |----| ----| ---- | ---- |
| JPEG | 有损压缩 | 不支持 | 不支持 | 复杂的图形 |
| GIF | 无损压缩 | 支持索引色透明 | 支持 | 简单的颜色和动画 |
| PNG | 无损压缩 | 支持索引色透明和ALPHA透明 | 不支持 | 较复杂的颜色、图形 |
| SVG | 无损压缩 | 支持 | 支持 | 矢量图形 |

JPEG分为baseline-jpeg和preogressive-jpeg两种，baseline-jpeg采用从上到下的扫描方式存储，在打开的时候，图形数据按存储的顺序从上到下显示出来，直到数据读完。preogressive-jpeg采用多次扫描的方式存储，在打开的时候会先显示图片的模糊轮廓，然后进行下一次扫描，每扫描一次，图像的清晰度增加，直到数据读完。在移动端的JPEG格式上，推荐后者。

GIF分为GIF静态图片和GIF动画，压缩率较高，最多只能支持256色

PNG格式分为PNG8、PNG24及PNG32，PNG8可支持索引色透明及ALPHA通道半透明，但最多支持256色，文件较小；PNG24可支持真彩色，但不支持透明，文件较大；PNG32可支持真彩色，支持ALPHA通道半透明，但文件巨大

SVG是一种矢量图形格式

* 颜色较多及图形复杂的图像优先选择JPEG
* 颜色较少图形结构简单及矢量图形优先选择SVG、GIF和PNG8

**图像压缩及合并**

* JPEG推荐使用jpegtran工具进行二次压缩
* PNG推荐使用PNGoo压缩
* 进行图像合并以减少HTTP请求数量
	* 过于复杂的不合并，尤其是JPEG，合并多用于图标等小而多的元素
	* 尽量将颜色相近的图片合并到一个雪碧图里
	* 控制图片的空隙
	* 雪碧图建议手工合并

###音频
**移动端音频格式解析**

主要支持的音频格式有wav、ogg、mp3，相比较而言mp3具有大小的较大优势

**音频工具**

* Adobe Audition
* GoldWave
* Cool Edit

**音频参数及压缩**

* 采样率
* 比特率
* 位数
* 声道

**音频雪碧图**

音频横向拼接，用JS控制播放 

```
if(audioObj.currentTime >= timeEnd){ 
	audioObj.pause()
}
```

###视频
**视频格式分析**

* ogg - 带有Theora视频编码和Vorbis音频编码
* mpeg4 - 带有H.264视频编码和AAC音频编码（主流）
* webm - 带有VP8视频编码和Vorbis音频编码

**视频工具**

* Premiere
* FormatFactory
* 暴风转码
* QQ影音工具

**视频参数及压缩**

* 分辨率
* 帧率

###代码
* 控制HTML的DOM层级
* 处理空格
* 简化命名
* 样式表放头部
* 脚本放尾部，必要时延迟加载
* 合理使用CSS选择器

##加载优化
###浏览器分析
**解析及加载原理**

浏览器解析并完成请求的完整过程

* Queueing 等待加载
* Stalled/Blocking 阻塞
* Proxy negotiation 代理服务器谈判
* Request sent 请求发送
* Waiting 等待服务器响应
* Content Download 主题内容下载

另外两种耗时可能

* Initial Connection/Connecting 初始化连接
* SSL

浏览器HTML解析与资源加载顺序

浏览器在完成HTML文档解析后会形成最原始的DOM树，然后开始对其他文档内资源的加载。DOM树形成的事件就是document的DOMContentLoaded。在解析HTML文档代码，并自上而下生成请求列表后，浏览器开始对列表中的请求进行并行加载，Chrome里并行的请求有6个。

在window.onload事件被触发时，浏览器加载进度提示结束，通过缩减DOMContentLoaded到onload的时长，可以有效优化用户体验到的页面打开速度

**DOM渲染过程**

* 常规文档流的DOM渲染 - 按照语义和默认样式自上而下渲染
* 样式对常规文档流渲染过程的影响 - 在整个DOM树渲染完后对样式修改可能产生重绘和重排，尽量避免
* JS对常规文档流渲染过程的影响 - 监听DOMContentLoaded和load事件

###加载优化
* 在DOMContentLoaded事件触发第一时间开始加载CSS、首屏图片及首屏交互设计的JS
* 对用户交互行为触发的请求，可以将请求加载控制语句写到对应的交互行为监听事件中
* 对于只需要后台静默加载的需求，可以等到所有资源加载完成后再加载，一般放在load事件处理

##脚本优化

#页面效果验证
##数据埋点
需要埋点的数据

**PV/UV**

PV - 页面被访问的次数
UV - 页面被访问的人次数，用cookie区分人次数

**关键转化率**

转化率用来衡量一个时间段内，特定的用户行为量和页面浏览的比率

**页面用户行为**

**数据分析平台**

[腾讯云分析](http://mta.qq.com/h5) [腾讯分析](http://v2.ta.qq.com)

##分析数据

##数据参考

