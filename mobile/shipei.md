## 移动适配方案
移动端的开发就是手机端的页面开发，在开发中不需要考虑css兼容低版本浏览器，只需要兼容webkit内核浏览器即可，一般css3的新特性都可以用的。

如果是简单的页面可以设置顶部和底部固定高度，中间的内容填充中间全部，内容使用定位的方式。


关于移动适配方案目前使用的主要是两种： 网易使用`rem配vm的方式` ，  淘宝使用的`meta方案`


### 网易的做法
网易的页面在不同的设备下页面显示不同的，页面除了`font-size`外其他的都使用 `rem` 为单位，直接通过js来计算页面的根节点大小
1. 需要设置`viewport`的值为`initial-scale=1,maximum-scale=1, minimum-scale=1`
2. 先拿设计稿竖着的横向分辨率除以100得到body的宽度（iphone6的横向分辨率750，body的width 750/100=7.5rem）
3. 在布局时，设计图的尺寸除以100得到css中的样式
4. 在页面加载之后需要设置根节点宽度`document.documentElement.style.fontSize=document.documentElement.clienWidth / 7.5 + 'px'`
5. `font-size` 可能需要额外的媒体查询，并且 `font-size`不能使用rem，比如网易的设置
```css
@media screen and (max-width: 321px){
  .m-navlist{font-size:15px;}
}
@media screen and (min-width:321px) and (max-width:400px){
  .m-navlist{font-size:15px;}
}
@media screen and (min-width:400px){
  .m-navlist{font-size:15px;}
}
```
6. **需要特别注意的是当 deviceWidth 大于设计稿的横向分辨率的时候，html的fontsize 始终等于 横向分辨率/body元素宽**
```js
var deviceWidth = document.documentElement.clientWidth;
if(deviceWidth > 750) deviceWidth = 750;
document.documentElement.style.fontSize = deviceWidth / 7.5 + 'px';
```
> 之所以这样，是因为deviceWidth大于750的时候，物理像素大于1350，应该去访问PC网站了，而不应该使用rem随着变化

### 淘宝的做法
淘宝的做法是动态改变页面的viewport的值，devicePixelRatio称为设备像素比，每款设备的devicePixelRatio都是已知，并且不变的，目前高清屏，普遍都是2，不过还有更高的，比如2.5, 3 等，通过修改
`maximum-scale=0.5,minimum-scale=0.5,initial-scale=0.5,user-scalable=0`,这样做的目的是保证页面的大小和设计稿保持一致，设计稿是750，则iPhone6的页面也是750，而且在高清屏的情况下，显示的更加清晰。

**注意：和网易一样的，淘宝也设置了一个临界点，当设备竖着时横向物理分辨率大于1080时，html的font-size就不会变化了，原因也是一样的，分辨率已经可以去访问电脑版页面了。**

```js
(function (doc, win) {
    var dpr = win.devicePixelRatio || 1,
    docEl = doc.documentElement || doc.body,
    // orientationchange 事件 用来监听手机屏幕的反转
    resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
    recalc = function() {
      var clientWidth = docEl.clientWidth; //(window.innerWidth);UC 或者QQ 安卓4.0 以下原生浏览器下面是没有
      if (!clientWidth) return;

      docEl.style.fontSize = 100 * (clientWidth / 750) + 'px';
      docEl.setAttribute('data-dpr', dpr);
    };

  if (!doc.addEventListener) return;
  win.addEventListener(resizeEvt, recalc, false);
  //DOMContentLoaded dom 加载完成，onload 有什么区别 dom css js OK 才执行的
  doc.addEventListener('DOMContentLoaded', recalc, false);
})(document, window);

```