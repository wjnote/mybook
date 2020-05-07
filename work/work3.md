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

   





