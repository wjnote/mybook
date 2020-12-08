## js数据加密

开发中我们常常有些数据是需要加密的， 然后传给后端，然后后端再加密来匹配数据库数据，前端常用的加密方法

#### base64加密
从IE10+ 浏览器开始，浏览器就原生提供了Base64编码解码的方法，不仅浏览器，Service Worker环境也可以用的，对于IE8/9 引入一个垫片脚本就可以了

> 之前的方式是用的开源的 base64.js

1. Base64编码
    ```js
        let encodedData = window.btoa('wujun');
        // 在js Worker线程中
        let encodedData = self.btoa('wujun');
        // d3VqdW4=
        let encodedData1 = window.btoa(decodeURI(encodeURIComponent('吴军')));
        // 5ZC05Yab
    ```
2. Base64解码 使用 `atob` 方法
    ```js
        let decodeData = window.atob(encodedData);
        let decodeData = self.atob(encodedData);
    ```
3. 如果需要兼容IE8 IE9 ，可以专门针对浏览器引入ployfill脚本
    ```html
        <!--[if IE]>
        <script src="./base64-polyfill.js"></script>
        <![endif]-->
    ```
> `[if IE]`表示所有IE浏览器，但是 IE10 已经放弃使用这种表达式，所以可以完美衔接IE9及以下


#### md5加密
在web前端js中使用md5加密，可以有效的保障信息在传向后端服务器过程中的安全，同时也可以减少后端二次md5加密计算资源的消耗。由于md5加密的不可破解性，相对来说md5方式的加密还是很安全的。可以用于网站密码的传输
```js
// 加载md5.js 插件
var hash = hex_md5("wujun");
console.log(hash)  // 0945c8489c9810d30258d670e10c07f3
```
> MD5加密后是一个表示，基本不能解密


#### SHA1加密(安全哈希算法)
```js
// sha1.js
var sha = sha1('wujun')
console.log(sha)  // a7abbe2d2c21d960f8f70aa1844e17440e0276f7
```
> SHA1 很容易就能解密，但是对 SHA1 加密过的密码，再加密一次，是非常安全的


---
[加密参考博客](https://www.liaoxuefeng.com/wiki/1022910821149312/1023025778520640)