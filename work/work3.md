## 踩坑记录3

1. 当页面中有弹框的时候，需要禁止页面的滚动效果，如果是PC端的可以直接使用`css`设置，如果是移动端则需要添加事件监听(UC浏览器无效)

   ```js
   document.body.parentNode.style.overflow = 'hidden'
   document.body.parentNode.style.overflow = 'auto'
   
   function bodyScroll(e){e.preventDefault(); e.stopPropagetion()}
   document.addEventListener('touchmove',bodyScroll,false); // 遮罩出现
   document.removeEventListener('touchmove', bodyScroll, false); // 遮罩消失
   // 或者使用下面的方法 兼容性比较广
   var i= $(window).scrollTop();
   $('body .page').css({'position': 'fixed',top: i,left: 0,right:0,bottom: 0})
   // 弹框消失的时候，在重新设置为 static
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

9. 项目中写的大屏显示需要注意的问题，特别是echarts的显示
    - 适配不同屏幕尽量还是用百分比，至于字体大小或者某些固定大小的元素，可以用rem适配
    - 布局用可用 百分比、vw.vh 等布局、也可使用flex布局，
    - 页面大小随屏幕大小而变化，字体按百分比或者rem等设置
    - 样式也可使用媒体查询
    - 窗口变化是刷新图表 `window.onresize = function () { chart.resize() }`

10. 我们使用echarts的时候，想让 `legend` 的内容自动使用元素的宽高来铺满的情况下，就可以通过元素的` offsetWidth offsetHeight`来计算，但是要注意的是 `lengend` 即便是设置了上下左右距离为0， 但是还是各有 `5px`的默认距离要减去，如果涉及到自己通过元素的宽高来设置的话，`itemWidth itemGap left top bottom right` 文字的高度就需要显示的设置，如果不显示设置就会有默认的值，导致结果不准确
    - Math.floor((elemHeght - 10 - legendData.length * 文字的高度) / (legendData.length - 1))
    - 如果同时图例设置了`itemHeight`  文字设置了`height` 则以值大的为准
    - 如果lengend图例只有文字, `icon: none` 设置的话，那文字前面还有 **5px** 的距离设置默认值 

11. 在IE中引入`es6.promise.polyfill.js` 文件可以支持 Promise，但是IE中一个页面只能使用一次`Promise.all`， 再使用时就报错。

12. 本地启动`webpack`访问另一个服务器注入的cookie的时候，有跨域限制，可以在请求头设置 `withCredential:true`   但是此时后端的 `Access-Control-Allow-Origin`不能设置为 *  否则请求会被拦截下来 ，此时直接用URL访问是没问题的，

13. 项目的图片名字最好使用英文，不要加 `@` 符号，安全检测的时候会提示`发生电子邮件地址模式`

14. 页面书写的时候会需要form表单禁止自动填充，否则公司检测会报错，`autocomplete="new-password"`，参数在Chrome能生效，但在`Firefox`无效，需要设置 `hidden` ，所以将两者结合起来就可以实现了

    > 文档里说 autocomplete=”off“可以禁止浏览器自动填充，亲测无效

    ```html
    <input type="password" hidden autocomplete="new-password" /> 
    ```

15.  数组的`concat`方法，后面添加的类型不同，结果也不同，需要注意直接在方法中添加，数组会被扁平化添加,如果超过一维数组的时候，则只会拆解一层

     ```js
     var a = [1,2,3,4], b = [3,4,5,['feagr','aa']];
     var c = a.concat(b); // [1,2,3,4,3,4,5,['feagr','aa']]
     
     var d = a.concat(3,4,5,['feagr','aa'])  // [1,2,3,4,3,4,5,'feagr','aa']
     var e = a.concat(2,3,['fea','feage'],['aaa',['bbb','ccc']])
     // [ 1, 2, 3, 4, 2, 3, 'fea', 'feage', 'aaa', [ 'bbb', 'ccc' ] ]
     ```

     

