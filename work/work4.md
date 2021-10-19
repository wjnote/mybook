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
    
8. `history.go()    histroy.back()`的用法和区别，go(-1)返回上一页，原页面表单中的内容会丢失；back() 返回上一页，原页面表单中的内容会保留，  history.go(-1) :后退 + 刷新。    history.back()： 后退

9. HTML5页面搜索的时候，可以将输入法的回车键变为搜索按钮直接搜索，注意下面的form表单需要添加 action 这个属性 才能实现效果

    ```html
    <form action="javascript:return true;" >
     <input type="search" id="searchval" placeholder="搜索事项名称" />
     <input type="text" style="display: none" />
    </form>
    ```

    ```js
    $('#searchval').on('keydown', function (e) {
      var value = $('#searchval').val().trim();
      var len = value.length;
      if (e.keyCode == 13) {
        getInfo(value);
      } else if (e.keyCode == 8 && len > 0) {
        var newValue = value.substring(0, len - 1);
        $('#searchval').val(newValue);
      }
    });
    // 监听键盘输入的keycode判断输入的键 之前开发的时候有个问题： 输入法的回车键不起效果
    ```

10. iphoneX 等手机顶部刘海两侧的内容会出现遮挡，开发中可以设置安全区域，填充危险区域，危险区域不做操作和内容展示

    1. 将页面的`<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes, viewport-fit=cover">`  将`viewport-fit`设置为`cover`

    2. 增加适配层 使用 `safe area inset` 变量

        ```less
        /* 适配iphoneX 顶部填充  */
        @supports(top: env(safe-area-inset-top)){
          body, .header{
            padding-top: constant(safe-area-inset-top, 40px);
            padding-top: env(safe-area-inset-top, 40px);
            padding-top: var(safe-area-inset-top, 40px);
          }
        }
        /* 判断iphonex 将footer的填充 */
        @supports(bottom: env(safe-area-inset-bottom)){
          body,.footer{
            padding-bottom: constant(safe-area-inset-bottom, 20px)
            padding-bottom: env(safe-area-inset-bottom, 20px)
            padding-bottom: var(safe-area-inset-bottom, 20px)
          }
        }
        ```

    3. > `safe-area-inset-*`由四个定义了视口边缘内矩形的 `top`, `right`, `bottom` 和 `left` 的环境变量组成，这样可以安全地放入内容，而不会有被非矩形的显示切断的风险。对于矩形视口，例如普通的笔记本电脑显示器，其值等于零。

11. Android手机中，点击输入框会弹出键盘，将页面样式错乱，键盘收起时，未恢复本来页面样式，因为输入法弹出后页面的高度变化了，导致可视区域变小，解决办法主要是通过监听页面的高度变化

     ```javascript
     // 记录原来的视口高度
     const originaHeight = document.body.clientHeight || document.documentElement.clientHeight;
     window.onresize = function(){
       var resizeHeight = document.documentElement.clientHeight || document.body.clientHeight;
       if(resizeHeight < originalHeight ){
         // 恢复内容区域高度
         // const container = document.getElementById("container")
         // 例如 container.style.height = originalHeight;
       }
     }
     ```

12. 键盘不能回落问题在微信H5开发中比较常见， 兼容原理，1. 判断版本类型，  2. 更改滚动的可视区域

     ```js
     // window.scrollTo(x-coord, y-coord)，其中window.scrollTo(0, clientHeight)恢复成原来的视口
     // 判断是否是微信浏览器
     const isWechat = window.navigator.userAgent.match(/MicroMessenger\/([\d\.]+)/i);
     if (!isWechat) return;
     const wechatVersion = wechatInfo[1];
     const version = (navigator.appVersion).match(/OS (\d+)_(\d+)_?(\d+)?/);
     
      // 如果设备类型为iOS 12+ 和wechat 6.7.4+，恢复成原来的视口
     if (+wechatVersion.replace(/\./g, '') >= 674 && +version[1] >= 12) {
       window.scrollTo(0, Math.max(document.body.clientHeight, document.documentElement.clientHeight));
     }
     ```

13. 
