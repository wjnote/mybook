## node爬坑

1. vue的时候在页面定义了一个定时器，然后切换到别的模块在回来时定时器会刷新多次， 并且在切换到别的模块时定时器依然在执行，比较消耗性能。

   ```vue
   /* 解决办法通过$once这个事件侦听器器在定义完定时器之后的位置来清除定时器。以下是完整代码：*/
   const timer = setInterval(() =>{                    
   
       // 某些定时器操作                
   }, 500);            
   // 通过$once来监听定时器，在beforeDestroy钩子可以被清除。
   this.$once('hook:beforeDestroy', () => {            
       clearInterval(timer);                                    
   })
   ```

   