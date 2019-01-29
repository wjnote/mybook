## mobile开发
开发H5页面中那么布局的活总是少不了，这也将面临不同终端的适配问题。开发中常遇到的坑
移动端在绑定事件的时候一般都会阻止默认事件，和冒泡事件

#### 移动端用到touch事件，在给子元素绑定click事件时，无论怎么点击子元素click都无效
click事件是在touch事件之后的，依次的顺序是 touchstart, touchmove, touchend, touchcancel, click, 这就是移动端click点击300ms的原因。
```js
$('.parent').on('touchstart',function(){console.log('touchstart')})
$('.parent').on('touchmove',function(){console.log('touchmove')})
$('.parent').on('touchend',function(){console.log('touchend')})
$('.child').on('click',function(){console.log('click')})
```
这样在 child元素上点击的时候会输出
```shell
// 点击的时候
touchstart
touchen
click

// 移动时
touchstart
touchmove
touchend
```
**这样表明touch事件会先于click事件执行，子元素的click会冒泡到父元素上，先执行父元素的touch事件，然后子元素捕捉到事件并执行子元素上的click事件**
```js
$('.parent').on('click',function(){console.log('parent click')})
$('.child').on('click',function(){console.log('child click')})

// child click
// parent click
```
上面就没有捕获了，是先执行子元素的点击事件，再冒泡到父元素的click事件
```js
$('.parent').on('touchstart',function(event){event.preventDefault(); console.log('touchstart')})
$('.parent').on('touchend',function(){console.log('touchend')})
$('.parent').on('click',function(){console.log('parent click')})
$('.child').on('click',function(){console.log(' child click')})
```
**这样就会点击子元素时，只执行父元素的touch事件，因为阻止默认事件的原因(event.preventDefault()放在touchend中也是同样的效果)，就不会产生捕获阶段，不会执行子元素的click事件和父元素的click事件** 如果要执行子元素的点击事件，就只能讲事件代理到父元素上，采用`event.target` 来执行