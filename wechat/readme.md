# 微信开发

如果我们在微信公众号页面需要调用上传手机图片，微信扫描二维码，照相等都需要调用`JSSDK` 来实现手机原生的功能

> 注意css里背景图和字体文件，尽量不要大于40k，因为会影响性能。在小程序端，如果要大于40k，需放到服务器侧远程引用或base64后引入，不能放到本地作为独立文件引用。



### 微信开发的js的变化

分为运行环境变化、数据绑定模式变化、api变化3部分。

运行环境从浏览器变成v8引擎，标准js语法和api都支持，比如if、for、settimeout、indexOf等。但浏览器专用的window、document、navigator、location对象，包括cookie等存储，只有在浏览器中才有，app和小程序都不支持。

可能有些人以为js等于浏览器里的js。其实js是ECMAScript组织管理的，浏览器中的js是w3c组织基于js规范补充了window、document、navigator、location等专用对象。

微信小程序代码不是在浏览器中，所以浏览器的对象无法使用。这意味着依赖document的很多HTML的库，比如jqurey无法使用。



### 微信公众号图片上传

图片上传是页面上传将图片上传到微信服务器，然后返回一个 `media_id`， 然后让后台通过 `media_id` 和 `token` 去微信服务器取改图片资源。

1. 微信小程序的所有图片都有一个默认的宽高，`320px*240px`, 对于图片的宽高设置，需要在wxml里面设置行内的

2. 小程序的布局使用 `rpx` 但是如果文字的话就使用 `px`,  如果边框是需要 1像素的线，可以直接使用 `1rpx` 实现

3. 微信 `JSSDK` 连续调用微信API会出问题，就改成递归的方式, 例如图片上传接口一次上传多张的时候，微信API只能一次上传一张



### 小程序和HTML开发不同

小程序的数据绑定参考了vue，但自己修改了一些。小程序的页面元素也有所变化
  - div 改成 view
  - span、font 改成 text
  - a 改成 navigator
  - img 改成 image
  - input 还在，但type属性改成了confirmtype
  - form、button、checkbox、radio、label、textarea、canvas、video 这些还在。
  - select 改成 picker
  - iframe 改成 web-view
  - ul、li没有了，都用view替代
  - audio 不再推荐使用，改成api方式，背景音频api文档

  - scroll-view 可区域滚动视图容器
  - swiper 可滑动区域视图容器
  - icon 图标
  - rich-text 富文本（不可执行js，但可渲染各种文字格式和图片）
  - progress 进度条
  - slider 滑块指示器
  - switch 开关选择器
  - camera 相机
  - live-player 直播
  - map 地图
  - cover-view 可覆盖原生组件的视图容器 



### 小程序和Vue的开发不同中的绑定事件传参

在vue中绑定事件传参挺简单，直接在触发事件的方法中，把需要传递的数据作为形参传入即可，但是在小程序中，不能直接在绑定事件的方法中传入参数，需要将参数作为属性值，绑定到元素的 `data-`属性上，然后在方法中，通过 `e.currentTarget.dataset.*` 的方式获取，从而完成参数的传递，

```vu
<button @click="say('namege')"></button>
new Vue({
	el: '#app',
	methods: {say(arg){console.log(arg)}}
})

<view class="tr" bindTap="toApprove" data-id="{{item.id}}"></view>
toApprove(e){
	let id = e.currentTarget.dataset.id;
}
```



### 小程序的生命周期

onLoad: 页面加载
一个页面只会调用一次，可以在 onLoad 中获取打开当前页面所调用的 query 参数。

onShow: 页面显示
每次打开页面都会调用一次。

onReady: 页面初次渲染完成
一个页面只会调用一次，代表页面已经准备妥当，可以和视图层进行交互。
对界面的设置如wx.setNavigationBarTitle请在onReady之后设置。详见生命周期

onHide: 页面隐藏
当navigateTo或底部tab切换时调用。

onUnload: 页面卸载
当redirectTo或navigateBack的时候调用。可以给返回的页面传递参数

>在`小程序`，会在`onLoad`或者`onShow`中请求数据。

