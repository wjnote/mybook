# js对URL编码解码
在`JavaScript`中对URL编码解码主要是三种方法


### encodeURI  和  decodeURI
`encodeURI()`是`JavaScript`中真正用来对URL编码的函数，它着眼于对整个URL进行编码，除了常见的符号以外，对其他的一些在网址中有特殊含义的符号： `; / ? : @ & = + $ , #` 也不进行编码。编码后，它输出符号的 `UTF-8` 形式，并且在每个字节前面加上`%`, 它对应的解码函数是 `decodeURI()`

**需要注意的是它不对单引号编码**
```js
encodeURI('http://www.baidu.com?name=zhang@xiao@jie&order=1')
// "http://www.baidu.com?name=zhang@xiao@jie&order=1"  和编码之前没有变化

let url='http://locahost:8080/index.html?time=2019-10-9&title=60元钱测试数据'

// URL编码  其中有汉字，就会编码
let encodeURI_url = encodeURI(url);
// http://locahost:8080/index.html?time=2019-10-9&title=60%E5%85%83%E9%92%B1%E6%B5%8B%E8%AF%95%E6%95%B0%E6%8D%AE

// 解码
let startStr = decodeURI(encodeURI_url);
//http://locahost:8080/index.html?time=2019-10-9&title=60元钱测试数据
```


### encodeURIComponent  和  decodeURIComponent
该方法与`encodeURI()`的区别就是，它用于对整个URL进行编码, `; / : @ & = + $ # `这些在`encodeURI()`中不被编码的符号，在`encodeURIComponent()`方法中会被全部编码。 对应的解码函数 `decodeURIComponent()`  会将整个URL全部编码

```js
let url='http://locahost:8080/index.html?time=2019-10-9&title=60元钱测试数据'
let encodeURIComponent_url = encodeURIComponent(url)
// http%3A%2F%2Flocahost%3A8080%2Findex.html%3Ftime%3D2019-10-9%26title%3D60%E5%85%83%E9%92%B1%E6%B5%8B%E8%AF%95%E6%95%B0%E6%8D%AE
```


### escape()   和  unescape()
`escape()`不能直接用于URL编码，它的真正作用是返回一个字符的Unicode编码值，**eacape()函数已经被弃用，并被encodeURIComponent替换**

它的具体规则是，除了 ASCII 字母，数字，标点符号`@ * _ + - . / `以外，对其他所有字符进行编码，转化为 Unicode 值

```js
const time = '2019-10-9'
const tilte = '60元钱测试数据'
let url = escape('http://www.baidu.com?name=zhang@xiao@jie&order=1');
// "http%3A//www.baidu.com%3Fname%3Dzhang@xiao@jie%26order%3D1"

let unescape_url = unescape("http%3A//www.baidu.com%3Fname%3Dzhang@xiao@jie%26order%3D1")
// http://www.baidu.com?name=zhang@xiao@jie&order=1

escape('吴')
// "%u5434"
```

需要注意：

1. 无论网页的原始编码是什么，一旦被Javascript编码，就都变为unicode字符。也就是说，Javascipt函数的输入和输出，默认都是Unicode字符。这一点对上面全部函数适用。
2. 其次，escape()不对"+"编码。但是我们知道，网页在提交表单的时候，如果有空格，则会被转化为+字符。服务器处理数据的时候，会把+号处理成空格。所以，使用的时候要小心。

---
[参考博客 | 阮一峰](http://www.ruanyifeng.com/blog/2010/02/url_encoding.html)