## 踩坑记录4

1. jsonp 跨域需要设置`crossDomin=true`  才能携带`cookie`; 所有的跨域方式都必须设置`crossDomain=true`,才能携带coolie。

2. `localStorage.setItem()`  不能存储对象，否则全部为` [object,object] `并不是我们要的结果，在存储的时候需要使用 `JSON.stringify` 转变一下，且不能存储二进制内容，或者图片等

3. class类的`constructor`构造方法只能有一个，否则解析会报错,可以没有会自动添加

4. 我们可以自定义对象的遍历器属性，让对象实现 `for of`的循环，注意必须返回一个对象，其中含有`value done`2个属性，`done：true` 表示循环结束
    ```js
      var obj = {name: 'wu', test: 10};
      obj[Symbol.iterator] = function(){
        var v = 0;
        return {
          next: function(){
            return {value: wu.test++, done: wu.test > 15}
          }
        }
      }
      for(let i of obj){console.log(i)}
    ```

5. 之前项目中下载文件的时候使用`window.loaction.href=''` 的方式，其中有些参数是动态拼接的，所以需要使用js的URL转码，否则IE浏览器会报错。

6. 使用vue开发项目的时候，如果需要自己修改源码的话，可以直接copy一个出来修改之后，在element注册之后再注册，就可以覆盖ui框架自己的了。 或者去github上找到对应版本的源码，将整个源码复制到本地进行修改，之后直接替换使用组件

7. HTML解析文件，生成DOM Tree，解析CSS文件生成CSSOM Tree, 将Dom Tree和CSSOM Tree结合，生成Render Tree(渲染树), 根据Render Tree渲染绘制，将像素渲染到屏幕上.
    - DOM解析和CSS解析是两个并行的进程，所以这也解释了为什么CSS加载不会阻塞DOM的解析。
    - 然而，由于Render Tree是依赖于DOM Tree和CSSOM Tree的，所以他必须等待到CSSOM Tree构建完成，也就是CSS资源加载完成(或者CSS资源加载失败)后，才能开始渲染。因此，CSS加载是会阻塞Dom的渲染的。
    - 由于js可能会操作之前的Dom节点和css样式，因此浏览器会维持html中css和js的顺序。因此，样式表会在后面的js执行前先加载执行完毕。所以css会阻塞后面js的执行。

每一轮 Event Loop 都会伴随着渲染吗？
requestAnimationFrame 在哪个阶段执行，在渲染前还是后？在 microTask 的前还是后？
requestIdleCallback 在哪个阶段执行？如何去执行？在渲染前还是后？在 microTask 的前还是后？
resize、scroll 这些事件是何时去派发的。