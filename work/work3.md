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
     var img = event.srcElement;
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

   