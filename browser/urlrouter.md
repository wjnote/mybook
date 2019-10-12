# 浏览器URL路由

浏览器提供了两种模式  hash  history 模式

vue是渐进式开发框架，需要引入前端路由，前端路由的核心就是：**改变视图的同时不会向后端发出请求**

- hash: hash虽然出现在URL中，但不会被包含在HTTP请求中，对后端完全没有影响，因此改变hash不会重新加载页面，不需要后端的配合也可以
- history: history 利用了 html5 history interface 中新增的 pushState() 和 replaceState() 方法。这两个方法应用于浏览器记录栈，在当前已有的 back、forward、go 基础之上，它们提供了对历史记录修改的功能。只是当它们执行修改时，虽然改变了当前的 URL ，但浏览器不会立即向后端发送请求。

>  hash 模式和 history 模式都属于浏览器自身的属性，vue-router 只是利用了这两个特性（通过调用浏览器提供的接口）来实现路由。

#### 实现原理
1. hash模式的原理是 onhashchange 事件，可以在window对象上监听
2. history ：hashchange 只能改变 # 后面的代码片段，history api （pushState、replaceState、go、back、forward） 则给了前端完全的自由，通过在window对象上监听popState()事件。

```js
pushState()、replaceState() 方法接收三个参数：stateObj、title、url。

// 设置状态
history.pushState({color: "red"}, "red", "red");

// 监听状态
window.onpopstate = function(event){
    console.log(event.state);
    if(event.state && event.state.color === "red"){
        document.body.style.color = "red";
    }
}
```


#### 优劣势

- 调用 history.pushState() 比使用 hash 存在的优势：
    1. pushState 设置的 url 可以是同源下的任意 url ；而 hash 只能修改 # 后面的部分，因此只能设置当前 url 同文档的 url
    2. pushState 设置的新的 url 可以与当前 url 一样，这样也会把记录添加到栈中；hash 设置的新值不能与原来的一样，一样的值不会触发动作将记录添加到栈中
    3. pushState 通过 stateObject 参数可以将任何数据类型添加到记录中；hash 只能添加短字符串
    4. pushState 可以设置额外的 title 属性供后续使用

- history 的劣势
    1. history 在刷新页面时，如果服务器中没有相应的响应或资源，就会出现404。因此，如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面
    2. hash 模式下，仅 # 之前的内容包含在 http 请求中，对后端来说，即使没有对路由做到全面覆盖，也不会报 404