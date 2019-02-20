## 异步函数
异步函数是js编程中一个重要的组成部分，在es6之前有常用的setTimeout、 setInterval、 requestAnimationFrame. 上述的方式都存在一定的缺点
> requestAnimationFrame 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行

#### [发布/订阅模式](../designmode/观察者模式.md)
常用的设计模式，采用一个中间对象来封装处理函数，采用消息列队的形式实现回调。

### Generator
Generator函数是ES6提供的一种异步编程解决方案，与传统的函数完全不同。可以理解为一个状态机，内部封装了多个内部状态，结合 `yield` 使用，**执行Generator函数是返回一个遍历器对象，可以依次遍历Generator函数内部的每一个状态**
```js
// 该函数有三个状态：hello，world 和 return 语句
function *test(){
    yield 'hello';
    yield 'world';
    return 'ending'
}
let hw = test();
```
函数执行之后不像普通函数直接执行，这个是返回一个指向内部状态的指针对象即遍历器对象(Iterator Object),如果要调用函数就需要调用 next 方法，使指针移向下一个状态，直到遇到下一个 yield表达式。next方法可以传参作为 yield表达式的返回值。

### Promise 对象
Promise是异步编程的一种解决方案，比回调函数更加合理和强大，Promise可以理解为一个容器，保存着未来才会结束的事情(通常就是我们采用的异步操作)
```js
const promise = new Promise(function(resolve,reject){
    if(/*异步操作*/){
        resolve(value);
    }else{
        reject(error)
    }
})
```
需要注意的是构造函数的`new Promise()`是立即执行的，只是其中的函数是等异步结束才执行。
> Promise的使用API很简单，主要是需要了解错误捕获机制，两种错误捕获机制的区别

#### [async 函数](./async.md)
在es2017标准中引入了 async 函数，async + await的方式很好的解决了异步的问题，可以理解 async 就是generator函数的语法糖，内置了函数执行器，并且返回的是一个Promise对象

---
**需要了解异步执行的实质，就需要了解[Event Loop(事件循环)](../accumulation/eventloop.md)，浏览器的事件循环和node的事件循环是不一样的。**

---
参考内容：
- [你需要知道的requestAnimationFrame](https://juejin.im/post/5a82f0626fb9a06358657c9c)
- [阮一峰--Generator](http://es6.ruanyifeng.com/#docs/generator)
- [阮一峰 -- Promise](http://es6.ruanyifeng.com/#docs/promise)
- [Promise迷你书](http://liubin.org/promises-book/)
- [阮一峰 -- async函数](http://es6.ruanyifeng.com/#docs/async)