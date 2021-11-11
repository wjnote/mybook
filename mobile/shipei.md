## 移动适配方案
移动端的开发就是手机端的页面开发，在开发中不需要考虑css兼容低版本浏览器，只需要兼容webkit内核浏览器即可，一般css3的新特性都可以用的。在开发中常用的解决方案


### 网易的做法  rem + pxToRem 的方式
网易的页面在不同的设备下页面显示不同，页面除了`font-size`外其他的都使用 `rem` 为单位，直接通过js来计算页面的根节点大小
1. 需要设置`viewport`的值为`initial-scale=1,maximum-scale=1, minimum-scale=1`
2. 先拿设计稿横向分辨率除以100得到body的宽度（iphone6的横向分辨率750，body的width 750/100=7.5rem）
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


### vh + vw 的方式
vw是相对于视窗宽度的单位，随着宽度变化而变化，其实rem只是 vw 的一种hack技术，通过监听实现了vw的效果

需要注意的是 vw 的兼容性不如 rem 高，在一些低版本的 safari 上有兼容性问题，但以后会成为更好的兼容方案

当在Ip6下时，100vw代表375px,而视觉稿一般是750px,为了方便算，当html上的1rem代表50px时，视觉稿上的像素跟rem就存在了100倍的转化关闭，而此时1vw代表是3.75px，所以html上的font-siez为50/3.75，约等于13.33333vw,
```css
html {
  background-color: #eee;
  font-size: 13.33333vw;
}
```

如果项目中再配合构建工具开发，可以设置[postcss-px-to-viewport](https://github.com/evrone/postcss-px-to-viewport),我们就可以通过配置直接写设计稿上的像素，会自动装换，只需要一个配置文件(post.config.js)文件就可以了
```shell
require('postcss-px-to-viewport')({
  viewportWidth: 750,
  viewportHeight: 1334,
  unitPrecision: 5,
  viewportUnit: 'vw',
  selectorBlackList: [],
  minPixelValue: 1,
  mediaQuery: false
})
```
上面解决了转换的计算，我们一般页面都可以使用vw来适配页面
1. 容器适配，可以使用vw
2. 文本的适配，可以使用vw
3. 大于1px的边框，圆角，阴影都可以使用vw
4. 内距和外距都可以使用vw

> 在开发中会发现设置vw并不完全是屏幕的宽度  例如 20vw 在iphone6 中会显示 75.04px 这样最后就会有一些出入，可以配合使用 calc()函数来完美解决








### 淘宝的做法
淘宝的做法是动态改变页面的viewport的值，devicePixelRatio称为设备像素比，每款设备的devicePixelRatio都是已知，并且不变的，目前高清屏，普遍都是2，不过还有更高的，比如2.5, 3 等，通过修改
`maximum-scale=0.5,minimum-scale=0.5,initial-scale=0.5,user-scalable=0`,这样做的目的是保证页面的大小和设计稿保持一致，设计稿是750，则iPhone6的页面也是750，而且在高清屏的情况下，显示的更加清晰。

**注意：和网易一样的，淘宝也设置了一个临界点，当设备竖着时横向物理分辨率大于1080时，html的font-size就不会变化了，原因也是一样的，分辨率已经可以去访问电脑版页面了。**

----

开发中使用的设置HTML页面的根节点的字体大小
```js
(function (doc, win) {
    var dpr = win.devicePixelRatio || 1,
    docEl = doc.documentElement || doc.body,
    // orientationchange 事件 用来监听手机屏幕的反转
    resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
    recalc = function() {
      //(window.innerWidth);UC 或者QQ 安卓4.0 以下原生浏览器下面是没有
      var clientWidth = docEl.clientWidth; 
      if (!clientWidth) return;

      docEl.style.fontSize = 100 * (clientWidth / 750) + 'px';
      docEl.setAttribute('data-dpr', dpr);
    };

  if (!doc.addEventListener) return;
  win.addEventListener(resizeEvt, recalc, false);
  doc.addEventListener('DOMContentLoaded', recalc, false);
})(document, window);
```



### 适配iphoneX

ihonex 的出现，产生了刘海屏这样的效果，开发移动端有增加了新的难度，为了保证页面的显示效果，我们必须把页面限制在安全范围内，但是不影响整体的效果

#### viewport-fit 

`viewport-fit` 是专门为了适配ihoneX而诞生的一个属性，它用于限制网页如何在安全区域内展示，是`meta`中的`viewport` 的一个属性值，

1. contain  可视窗口完全包含网页内容
2. cover  网页内容完全覆盖可视窗口
3. 默认情况下设置为auto  效果和 contain 相同

我们要将顶部和底部合理的摆放在安全区域内，`iOS11`新增了2个css函数 `env  constant` 用于设定安全区域和边界的距离，函数的内部可以是四个常量

- safe-area-inset-left   安全区域距离左边边界的距离
- safe-area-inset-right   安全区域距离右边边界的距离
- safe-area-inset-top   安全区域距离上边边界的距离
- safe-area-inset-bottom   安全区域距离底部边界的距离

**必须要设置了viewport-fit后才能使用这两个函数**

`<meta name="viewport" content="viewport-fit=cover">`

`constant` 在`iOS < 11.2` 的版本中生效   `env` 在`iOS >= 11.2` 版本中生效，我们在设置的时候需要2个都设置

```css
body{
  padding-bottom: constant(safe-area-inset-bottom);
  padding-bottom: env(safe-area-inset-bottom);
}
```





### 横屏适配

很多视口我们都要对横屏和竖屏显示不同的布局，所以我们需要检测不同场景设定不同的样式，我们一般是设置了屏幕不能旋转

`JavaScript`检测横屏，可以通过`window.orientation` 来获取屏幕旋转的方向

```js
window.addEventListener('resize', ()=>{
  if(window.orientation === 180 || window.orientataion===0){
    // 正常方向或屏幕旋转180读     竖屏
  }
  if(window.orientation === 90 || window.orientation === -90){
    // 屏幕旋转90度   横屏
  }
})
```

```css
@media screen and (orientation: portrait){ /* 竖屏 */}
@media screen and (orientation: landscape){ /* 横屏 */}
```

