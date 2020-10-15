# 编程小技巧3

1. 当页面中有弹框的时候，需要禁止页面的滚动效果，如果是PC端的可以直接使用css设置，如果是移动端则需要添加事件监听(UC浏览器无效)

   ```js
   document.body.parentNode.style.overflow = 'hidden'
   document.body.parentNode.style.overflow = 'auto'
   
   function bodyScroll(e){e.preventDefault(); e.stopPropagetion()}
   document.addEventListener('touchmove',bodyScroll,false); // 遮罩出现
   document.removeEventListener('touchmove', bodyScroll, false); // 遮罩消失
   ```

2. 默认图片，在页面中会设置默认的头像

   ```js
   function defaultImg(){
     var img = event.target;
     img.src= ''; 
     img.onerror = null; // 为了防止默认图片地址错误的时候，会一直抖动
   } 
   ```

3. 在开发中对于后台返回的数据需要先验证长度，再循环遍历，可以使用 `&&` 快速确认,  下面的这种方式`&&`右边只能执行方法或者是执行具名函数， `&&`  前后不能出现 `=` 赋值符号， 也不能执行 `return` 等

   ```js
   res.data.length && res.data.forEach();
   res.data.length && fnname();
   ```

4. JS中的获取HTML集合，提供了一个类似数组的列表，这个列表是一个‘假定实时态’，当底层文档对象变化时，它也会自动更新，HTML集合一直保持着和文档的连接，当需要访问信息，例如访问列表的长度时，也会重复执行查询的过程，会导致很慢，优化方式是用一个数组来复制该列表，或者将长度保存为一个局部变量

   ```js
   var alidivs = document.getElementsByTagName('div')
   for(var i =0;i< alidivs.length;i++){
     document.body.appendChild(document.createElment('div'))
   }   // 这个方法会陷入死循环
   ```

5. 开发中在一行内容超过时显示省略号很常见，现在需要设置超过2行会在第二行的末尾显示省略号的方式,需要使用到CSS3的内容，其中 `-webkit-line-clamp` 表示需要几行的内容，最好高度就行高的几倍

   ```css
    overflow:hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    /*! autoprefixer: off */
    -webkit-box-orient: vertical;
   ```
   > 开发中会发现 `-webkit-box-orient` 没有效果，需要加上如下的注释  /*! autoprefixer: off */
   
6. axios请求中作为请求主题被发送的数据，类型必须是 **`string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams`**

   - 浏览器专属类型： `FormData,  File,  Blob`
   - Node专属： StreamNode专属： Stream

7. 有时给项目下载包的时候npm运行报错，需要先清除缓存数据 `npm cache clear --force`

8. 如果项目中需要全屏(类似F11的效果) 实现代码

   ```js
   function fullScreen(){
     var docElm = document.documentElement;  // 可以选择指定的某个元素，让某个元素全屏
     if (docElm.requestFullscreen) {
       docElm.requestFullscreen();
     } else if (docElm.msRequestFullscreen) {
       docElm = document.body; //overwrite the element (for IE)
       docElm.msRequestFullscreen();
     } else if (docElm.mozRequestFullScreen) {
       docElm.mozRequestFullScreen();
     } else if (docElm.webkitRequestFullScreen) {
       docElm.webkitRequestFullScreen();
     }
   };
   // 退出全屏模式
   function outFullScreen(){
     if (document.exitFullscreen) {
       document.exitFullscreen();
     } else if (document.msExitFullscreen) {
       document.msExitFullscreen();
     } else if (document.mozCancelFullScreen) {
       document.mozCancelFullScreen();
     } else if (document.webkitCancelFullScreen) {
       document.webkitCancelFullScreen();
     }
   }
   ```

   >  全屏效果的实现，如果页面中有echarts图需要在页面改变后重置echarts图