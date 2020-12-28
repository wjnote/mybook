## iframe解析

#### iframe的src的值发生改变,才会刷新iframe里面的内容.
解决办法就是在iframe后面添加一个随机数 / 时间戳，用来使iframe的src不一致
```js
$("#iframe").attr("src","${h5.url}"+"?time="+new Date().getTime());

// 重置iframe的刷新状态
document.getElementById('zqt_logout_iframe').contentWindow.location.reload(true);
```

#### 我们常用的iframe的属性设置
```shell
# 有一部分 常用的属性都是直接设置在 iframe 标签上的
1.frameborder:是否显示边框，1(yes),0(no)

2.height:框架作为一个普通元素的高度，建议在使用css设置。

3.width:框架作为一个普通元素的宽度，建议使用css设置。

4.name:框架的名称，window.frames[name]时专用的属性。

5.scrolling:框架的是否滚动。yes,no,auto。

6.src：内框架的地址，可以使页面地址，也可以是图片的地址。
7.srcdoc , 用来替代原来HTML body里面的内容。但是IE不支持, 不过也没什么卵用
8.sandbox: 对iframe进行一些列限制，IE10+支持
```

#### 获取iframe里的内容
主要的API就是2个 `contentWindow` 和 `contentDocument`，这2个API 只是 DOM 节点提供的方式
iframe.contentWindow 获取iframe的window对象
iframe.contentDocument 获取iframe的document对象

```js
var iframe = document.getElementById("iframe1");
var iwindow = iframe.contentWindow;
var idoc = iwindow.document;
console.log("window",iwindow);//获取iframe的window对象 js文件的方法内容
console.log("document",idoc);  //获取iframe的document
console.log("html",idoc.documentElement);//获取iframe的html
console.log("head",idoc.head);  //获取head
console.log("body",idoc.body);  //获取body
```

#### 在iframe中获取父级内容
在同域下，父页面可以获取子iframe的内容，那么子iframe也可以操作父页面的内容，主要是下面几个API
```js
window.parent  // 获取上一级的window对象 如果上一级还是iframe则获取了iframe的window对象
window.top  // 获取最顶级容器的window对象，就是你打开的页面的文档
window.self  // 返回自身的window引用，可以理解为 window === window.self
```

#### iframe 的作用
1. iframe 主要是使用在页面的广告中，为了保证页面的安全性，可以使用iframe来加载广告，我们可以将iframe理解为一个沙盒，里面的内容能够被 top window 完全控制，并且主页的css样式不会入侵iframe里面的样式

2. 之前一个项目中，在一个网站登录了用户信息，在另外的网站刷新也需要自动登录，这时就可以使用iframe
> 开发过程中发现，如果直接在js代码中 ajax对方的接口，则会报请求跨域，所以使用设置iframe的SRC的方式来请求

3. 因为iframe享有着click的最优先权，当有人在伪造的主页中进行点击的话，如果点在iframe上，则会默认是在操作iframe的页面。 所以，钓鱼网站就是使用这个技术，通过诱导用户进行点击，

#### 自适应iframe
在之前开发的项目中，要求iframe能完全自适应页面，默认情况下，iframe 会自带滚动条，不会全屏，如果想要自适应iframe的话。
1. 去掉滚动条 `<iframe src='' id="iframe1" scrolling="no">`
2. 设置iframe的高为body的高
```js
var iwindow = iframe.contentWindow;
var idoc = iwindow.document;
iframe.height = idoc.body.offsetHeight;
```
3. 有需要还可以设置其他的属性  
```js
allowtransparency: true/false  // 是否允许iframe设置为透明 默认为false
allowfullscreen: true/false    // 是否允许iframe全屏，默认false
```


完整的带有所有属性的 iframe 节点
`<iframe id="google_ads_frame2" name="google_ads_frame2" width="160" height="600" frameborder="0" src="target.html" marginwidth="0" marginheight="0" vspace="0" hspace="0" allowtransparency="true" scrolling="no" allowfullscreen="true"></iframe>`



#### 项目中如果使用iframe跨域了

1. 无法读取 cookie ， localStorage , indexDB
2. DOM 无法获取 
3. ajax 请求无法发送



例如一个需求是引用别人封装好的一个视频播放器，iframe里面有全屏按钮，点击后需要让页面iframe全屏，收到同源策略的限制，iframe无法告诉页面

**解决办法**

> 使用中间页面

我们可以使用一个与a页面同域下的c页面作为中间页面，b页面加载c页面。c页面调用a页面的方法，从而实现b页面调用a页面方法，由于c页面和a页面是同源的，可以避开跨域问题。

```html
<!-- c页面就只是一个执行的方法 -->
<body>
 <script>
  window.onload = function () {
   parent.parent.toggleFullScreen();
  }
 </script>
</body>
```



> postmessage

window.postMessage 方法可以安全的实现跨域通信，写明目标窗口的协议、主机地址或端口就可以发信息给它

```html
parent.postMessage(value, 'http://a.demo.com')
```

```js
// A 页面
window.addEventListener("message", function( event ) {
  if(evet.origin !== '') return;  // 先判断一下是否来自你想设置的那个域名
  sonmeFn();
})
```

