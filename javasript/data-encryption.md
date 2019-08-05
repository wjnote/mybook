## js数据加密

开发中我们常常有些数据是需要加密的， 然后传给后端，然后后端再加密来匹配数据库数据


1. base64加密
```js
// base64.js
   var b = new Base64();
   var str = b.encode("admin:admin");
   alert("base64 encode:" + str);
　　　　　//解密
   str = b.decode(str);
   alert("base64 decode:" + str);
```


2. md5加密
```js
// 记在 md5 文件
 var hash = hex_md5("123dafd");
    alert(hash)
```

3. sha1加密，据说这是最安全的加密
```js
var sha = hex_sha1('mima123465')
    alert(sha)   
```
