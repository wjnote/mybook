## 移动端的爬坑记录

在开发H5的过程中，会遇到很多坑，在此记录一下坑产生的原因，以及现行的最有效的填坑方法



#### iOS滑动不流畅

上下滑动的页面会产生卡顿，手指离开页面，页面就立即停止运动，整体表现不流畅，没有惯性运动。

> 产生原因

在iOS5.0 以及以后的版本，滑动定义了2个值，`auto / touch` 默认是auto

```css
/* 当手指离开页面，页面还会惯性滚动 */
-webkit-overflow-scrolling: touch;
/* 当手指离开页面 页面会立即停止滚动 */
-webkit-overflow-scrolling: auto; 
```

> 解决方案

1. 在滚动容器上增加滚动touch方法，将`-webkit-overflow-scrolling` 值设置为`touch`, 在外层的页面设置滚动条隐藏 `.container ::-webkit-scrollbar{display: none}`，  但是该方法可能导致页面使用 `position: fixed`固定定位的元素也跟随页面一起滚动

2. 设置外部`overflow`为`hidden`，设置内容容器`overflow`为`auto`，内部元素超出body即产生滚动，超出的部分body隐藏， 

   ```css
   body{overflow-y: hidden;}
   .wrapper{overflow-y: auto}
   ```

   

#### ios上拉/下拉 边界出现白色空白

手指按住屏幕下拉，屏幕顶部会多出一块白色空白区域，向上拉的时候顶部也会出现一块空白区域

> 产生原因

在iOS中，手指按下屏幕上下拖动，会触发 `touchmove`事件，这个事件触发的是整个`webview`容器，容器拖动，剩下的部分会空白

> 解决办法

监听事件禁止滑动，移动端触摸事件有3个，`touchstart  touchmove  touchend`  我们需要控制  `touchmove` 事件。

`touchmove` 事件的速度是可以实现定义的，取决于硬件性能和实现细节

`preventDefault` 方法，阻止同一触发点上所有的默认行为，滚动也可以阻止，所以我们可以通过监听`touchmove`，让需要滚动的地方滚动，不需要的地方禁止滚动

>  注意需要过滤掉具有滚动容器的元素

```html
<body>
  <div class="scroll" style="height: 1500px;"></div>
</body>
```

```js
var overscroll = function(el){
  el.addEventListener('touchstart', function(){
    var top = el.scrollTop;
    var totalScroll = el.scrollHeight;
    var currentScroll = top + el.offsetHeight;
    if(top===0){
      el.scrollTop =1;
    }else if(currentScroll === totalScroll){
      el.scrollTop = top -1;
    }
  })
  el.addEventListener('touchmove',function(e){
    if(el.offsetHeight < el.scrollHeight){
      e._isScroller = true
    }
  })
}
overscroll(document.querySelector('.scroll'));
document.body.addEventListener('touchmove',function(e){
  if(e._isScroller) return;
  e.preventDefault();
},{passive: false})  //passive 参数不能省略
/* ios 11.3系统以后 e.preventDefault 代码失效了 */
```

> 该方法也会把单个元素左右滑动也禁止掉