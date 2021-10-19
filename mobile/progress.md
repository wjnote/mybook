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

#### transform 属性影响 position: fixed 

css规范中有规定：如果元素的 transform 值不为 none，则该元素会生成包含块和层叠上下文。CSS Transforms Module Level 1 不只在手机上，电脑上也一样。除了 fixed 元素会受影响之外，z-index（层叠上下文）值也会受影响。绝对定位元素等和包含块有关的属性都会受到影响。当然如果 transform 元素的 display 值为 inline 时又会有所不同。最简单的解决方法就是 transform 元素内部不能有 absolute、fixed 元素.



#### 移动开发不同手机弹出数字键盘问题

1. `type=tel`  ios 和 Android的键盘表现差不多

2. `type=number` ios下不是九宫格键盘，输入不方便，并且在旧版Android在输入框后面会有个无用的小尾巴，可以使用`webkit`私有的伪元素fix掉

   ```css
   input[type=number]::-webkit-inner-spin-button,  
   input[type=number]::-webkit-outer-spin-button { 
           -webkit-appearance: none; 
           appearance: none; 
           margin: 0; 
   }
   ```

3. `pattern` 属性： pattern 用于验证表单输入的内容，在输入框加上pattern可以让前端的验证更加高效，属性值要用正则表达式 `<input type='number' pattern='[0-9]*' />` 输入框只能输入数字



#### 移动手机input手机键盘显示搜索按键

在移动端在输入完成要显示搜索需要满足下面几项 

1.  `input  type='search'`
2.  放在form中，并且form有action属性 `<form action='.'><input type='search'/> </form>`

如果页面中不设置搜索按钮，而是点击键盘中的搜索时就自动搜索，可以监听输入框的回车事件，比如在 `keypress` 事件监听中，获取 `keyCode` 是否为回车，同时是放在`form`中，点击搜索页面会自动刷新的，我们可以加一个隐藏的`input`来解决，或者使用iframe来解决页面刷新的问题

```html
<form action='.'>
  <input id="searchbtn" type="search" />
  <input type="text" style="display:none; " />
</form>

<form  action='.' target="framfile" id="form1">
  <input type="search" placeholder="请输入内容">
  <iframe name="framfile" style="display: none;" frameborder="0"></iframe>
</form>
<!-- 将form的target属性导向到iframe的name上面，这样页面地址就没有改变页面也不会刷新 -->
```

设置`type=search` 的输入框在获取焦点的时候，默认右边会出现一个小叉叉，删除输入的全部内容，不同浏览器样式不同，这个可以设置css去掉

```css
input[type='search']::-webkit-search-cancel-button{
  -webkit-appearance: none;
}
```



