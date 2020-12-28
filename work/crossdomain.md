## 跨域方式

### 同源策略及其限制的内容
同源策略是一种约定，它是浏览器最核心最基本的安全功能，可以防止浏览器 XSS，CSRF 攻击等。

**协议+域名+端口** 三者要完全相同，即便是2个不同的域名指向同一个IP地址也是非同源

同源策略限制的内容
1. Cookie、 LocalStorage、 IndexedDB 等存储内容
2. DOM 节点
3. AJAX请求发送后，结果被浏览器拦截了

前端有几种可以不受同源策略限制的(也是CDN加速的原因)
1. <img src=""/>
2. <link href=""/>
3. <script src="">

### 跨域请求资源
当协议，子域名，主域名，端口号任意一个不同时，都算作不同域；

```shell
http://www.a.com/a.js
http://a.com/b.js

# 上面的同一域名，不同二级域名 是不能通信的，cookie这种情况下也不能访问
```


> 需要特别注意的2点

1. 如果是协议和端口造成的跨域，前端是无能为力的
2. 在跨域问题上，仅仅是通过URL首部来识别的，而不会根据域名对应的IP地址是否相同来判断，


> 请求跨域了 请求发出去了吗？ 
**跨域并不是请求发不出，请求能发出去，服务端能收到请求并能正常返回结果，只是结果被浏览器拦截了** 跨域就是为了阻止用户读取另一个域名下的内容，ajax可以获取响应，浏览器认为这不安全就拦截了响应，表单是个例外，表单并不会获取新的内容，所以可以发起跨域请求，同时说明了跨域并不能完全阻断CSRF，毕竟请求是发出去了


### 跨域解决方案

#### cors
CORS 需要浏览器和后端同时支持，IE8、IE9需要通过 `XDomainRequest` 来实现

浏览器会自动进行CORS通信，实现CORS通信的关键是后端，后端需要设置请求头的字段，服务器设置 `Access-Control-Allow-Origin` 就可以开启cors，该属性表示那些域名可以访问资源。

虽然设置CORS和前端没多大关系，但是通过这种方式解决跨域问题的话，会在发送请求时出现**简单请求和复杂请求**


简单请求需要同时满足2大条件
1. 请求方式是  GET 、 HEAD 、 POST 中的一个
2. `Content-Type` 的值： `text/plain` /  `multipart/form-data` / `application/x-www-form-urlencoded`

> 请求中的任意 `XMLHttpRequestUpload` 对象均没有注册任何事件监听器， `XMLHttpRequestUpload` 对象可以使用 XMLHttpRequest.upload 属性访问

复杂请求
不符合以上条件的请求就肯定是复杂请求了。
复杂请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求，称为"预检"请求,该请求是 option 方法的，通过该请求来知道服务端是否允许跨域请求。


#### JSONP
利用 `<script>` 标签没有限制的漏洞，网页可以得到从其他来源动态产生的JSON数据， **JSONP一定要对方的服务器做支持才可以**

JSONP 和AJAX 相同，都是客户端向服务器端发送请求，从服务器断案获取数据，但AJAX属于同源，JSONP属于非同源策略

JSONP 优点是兼容性好，可用于解决主流浏览器的跨域访问问题，**缺点是仅支持GET方法具有局限性，不安全可能遭受XSS攻击**


1. 声明一个回调函数，其函数名(如show)当作参数值，要传递给跨域请求数据的服务器，函数形参为要获取目标数据（服务器返回的data）
2. 创建一个`script`标签，把那个跨域API数据接口地址，赋值给script的src,还要向这个地址中服务器传递该函数名
3. 服务器接收到请求后，需要进行特殊的处理： 把传递进来的函数名和它需要的给你的数据拼接成一个字符串
4. 最后服务器把准备的数据通过HTTP协议返回给客户端，客户端再调用执行之前声明的函数，对返回的数据进行操作

```js
function jsonp({url, params, callback}){
  return new Promise((resolve, reject)=>{
    let script = document.createElement('script');
    window[callback] = function(data){
      resolve(data);
      document.body.removeChild(script)
    };
    params = {...params, callback};
    let arrs = [];
    for(let key in params){
      arrs.push(`${key}=${params[key]}`)
    };
    script.src= `${url}?${arrs.join('&')}`
    document.body.appendChild(script)
  })
}
```


#### postMessage
postMessage是`HTML5 XMLHttpRequest Level 2`中的API，且是为数不多可以跨域操作的window属性之一，它可用于解决以下方面的问题：

1. 页面和其打开的新窗口的数据传递
2. 多窗口之间消息传递
3. 页面与嵌套的iframe消息传递
4. 上面三个场景的跨域数据传递

**postMessage()方法允许来自不同源的脚本采用异步的方式进行有限的通信，可以实现跨文本档，多窗口，跨域消息传递**

```js
  otherWindow.postMessage(message, targetOriin, [transfer])

// message: 将要发送到其他 window的数据。
// targetOrigin:通过窗口的origin属性来指定哪些窗口能接收到消息事件，其值可以是字符串"*"（表示无限制）或者一个URI。在发送消息的时候，如果目标窗口的协议、主机地址或端口这三者的任意一项不匹配targetOrigin提供的值，那么消息就不会被发送；只有三者完全匹配，消息才会被发送。
// transfer(可选)：是一串和message 同时传递的 Transferable 对象. 这些对象的所有权将被转移给消息的接收方，而发送一方将不再保有所有权。
```


#### websocket
Websocket是html5的一个持久化的协议，实现了浏览器和服务器全双工的通信，同时也是跨域的解决方案之一，websocket和HTTP都是应用层协议，都是基于 TCP 协议的，但是**websocket是一种双向通信协议，在建立连接之后，websocket的客户端和服务器都能主动发送或接收消息**，websocket在建立的时候需要借助HTTP协议，连接建立好了之后 client 与 server 之间的双向通信就于HTTP无关了

> 原生的Websocket  API使用不是很方便，我们一般使用 Socket.io 插件

#### Node中间件代码（两次跨域）
同源策略是浏览器需要准守的标准，但是服务器无需准守同源策略，所以可以设置一个代理服务器

1. 代理服务器接收客户端请求
2. 将请求 转发给服务器
3. 拿到服务器响应数据
4. 将响应数据转发给客户端

> webpack的中间代理设置就是利用这个原理


#### nginx反向代理
实现原理类似于node中间代理，需要搭建一个中转 Nginx 服务器，用于转发请求

使用Nginx反向代理实现跨域，是最简单的跨域方式，只需要修改Nginx配置就可以解决跨域问题，支持所有浏览器，支持session，不需要修改任何代码。

实现思路： 通过Nginx配置一个代理服务器(域名与domain1相同，端口不同)做跳板机，反向代理访问domain2接口，并且可以顺便修改coolie中的domain信息，方便当前域cookie写入，实现跨域登录


#### window.name + iframe
window.name属性的独特之处：name值在不同的页面，甚至不同的域名加载后依旧存在，并且可以支持非常长的name值(2MB)

其中a.html  b.html是同域的(localhost:3000)，c.html是一个域的(locahost:4000)
```html
a.html

<iframe src="http://localhost:4000/c.html" id="firame"></iframe>
<script>
  let first =true;
  function load (){
    if(first){
      let iframe = document.getElmentById('firame');
      iframe.src='http://localhost:3000/b.html'
      first =false;
    }else{
      // 第二次加载时 b.html加载成功后，读取同域widnow.name中的数据
      console.log(iframe.contentWindow.name)
    }
  }
</script>
```
> b.html页面为中间代理页面，内容为空

**通过iframe 的src属性由外域转向本地域，跨域数据即由iframe的window.name从外域传递到内域，这个就巧妙的绕过了浏览器的跨域访问限制，同时又是安全操作**


### 总结
- CORS支持所有类型的HTTP请求，是跨域HTTP请求的根本解决方案
- JSONP只支持GET请求，JSONP的优势在于支持老式浏览器，以及可以向不支持CORS的网站请求数据。
- 不管是Node中间件代理还是nginx反向代理，主要是通过同源策略对服务器不加限制。
- 日常工作中，用得比较多的跨域方案是cors和nginx反向代理
