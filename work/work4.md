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