## async函数
async是'异步'的简写，而await可以认为是`async wait`的简写，async用于声明一个函数是异步的，而await用于等待一个异步方法执行完成,如果有返回值则可以赋值给变量。 async函数的强大之处在于可以嵌套的，我们可以定义一批原子操作，然后利用函数组合出新的函数。

> await只能出现在async函数中

async 函数所起的作用就是它处理返回值的方式：将返回值封装成一个 Promise 对象。如果在函数中直接返回一个变量，async 也会将这个变量用 `Promise.resolve()` 封装成promise对象
```js
async function test(){
    return 'this is test'
}
const temp = test();
console.log(temp)  // 这个时候输出的是一个promise对象
```
> 如果没有返回值的时候就返回`promise.resolve(undefined)`

因为promise是无需等待的，所以没有await的话，async函数就会立即执行返回一个promise对象，并不会阻塞后面的语句，就和普通的promise对象没区别。
```js
async function test(){
  console.log(111)
}
test();

new Promise(function(){
  console.log(333)
  setTimeout(()=>{
    console.log(444)
  },0)
})
console.log(222)

/* 上面的输出顺序
111
333
222
444
*/
```

## await 在等待什么
await是在等待后面的表达式，这个表达式计算的结果是promise对象或者其他值，因为async是返回一个promise对象，可以说await是在等待一个async函数完成，但是await等待的是一个返回值，这个返回值可以是任意值的，不一定是promise对象。
```js
// 下面就说明了await后面的代码不一定是异步函数
function getSomething() {
  console.log(222)
  return "something";
}
async function testAsync() {
  console.log(333)
  return Promise.resolve("hello async");
}
async function test() {
  const v1 = await getSomething();
  const v2 = await testAsync();
  console.log(444)
}
test();
console.log(111)

let a = 1;
a++
console.log(a)

/* 输出的顺序结果
222
111
2
333
444
在执行程过中 async 函数会立即执行，遇到 await 的时候会执行右边的函数，直接打印 222，然后在 await 处让出函数执行权，执行后续的同步代码 111、2，等同步代码执行完毕 在返回到 await 处执行后续的代码
*/
```

await 可以理解为一个运算符，用于组成表达式，这个表达式的结果取决于它等待的东西，右边表达式的返回值如果不是promise对象的话，表达式的结果就是右边表达式返回的值，**如果是promise对象的话，它就会阻塞后面的代码，等待promise对象resolve，然后得到resolve的值，作为await表达式的结果，这就是将异步写成同步代码的重要一步**

> 这就是await必须用在async函数中的原因: async函数调用不会造成阻塞，遇到await的时候先执行右边的函数，然后交出函数的执行权，它内部的所有阻塞都被封装在一个promise对象中异步执行


## async/await函数和之前函数的比较
```js
// 一般的异步写法
function test(){
    return new Promise(function(resolve,reject){
        setTimeout(()=>resolve('hello'), 1000)
    })
}
test().then( v => console.log(v) );

// 用async写法
function test1(){
    return new Promise(function(resolve,reject){
        setTimeout(()=>resolve('hello'), 1000)
    })
}
async function test2(){
    var v = await test1();
    console.log(v)
}
test2();
```
test1 函数因为是直接返回的promise对象，所以可以不用申明为async函数。
上面两种写法实现的效果是一样的，下面的写法看着代码还要多一些，但是下面的写法在处理多个promise对象的then链的时候就能体现优势，如果只是单一的一个promise，可以直接使用上面的写法实现。

## 错误处理
await后面的promise对象如果变为了reject状态时，则reject的参数会被catch方法的回调函数接收到,即便是没有return，如果是resolve状态的话，then的参数就是`undefined`
```js
async function f(){
  await Promise.reject('error msg')
}
f().then(v=>console.log(v)).catch(e=>console.log(e))
```
> 任何一个await后面的promise对象变为reject状态时，那个整个async函数都会中断执行,如果不想中断执行可以使用 try catch


## 注意点

1. await 命令后面的 Promise 对象，运行结果可能是 rejected，所以最好把 await 命令放在 try...catch 代码块中。
```js
async function myFunction() {
  try {
    await fuPromise();
  } catch (err) {
    console.log(err);
  }
}
```
2. 多个`await`命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。(并发执行和继发执行)
```js
//并发执行的常用写法
let [foo, bar] = awati Promise.all([getFoo(), getBar()]);

function dbFuc(db) {
  let docs = [{}, {}, {}];
  docs.forEach(async function (doc) {
    await db.post(doc);
  });
}
// 上面的代码可能不会正常执行，因为这三个post请求将是并发执行的，也就是同时执行，
// 如果需要继发执行，需要使用下面这种写法
async function dbFuc(db) {
  let docs = [{}, {}, {}];
  for (let doc of docs) {
    await db.post(doc);
  }
}

// 并发执行也可以使用下面的写法   map是并发执行的，只有在async函数内部才是继发执行，外部不受影响
async function fn(db){
    let docs=[{},{},{}]
    let promises = docs.map((doc)=>db.post(doc))
    let result = await Promise.all(promises)
}
async function getOrder(urls){
  const testPromises = urls.map(async(url)=>{
    const response = await fetch(url)
    return response;
  })
}
```

3. await 只能写在 `async` 函数内，否则报错，中间不能使用类似forEach的包装await
4. 按顺序完成异步操作

```js
async function login(urls){
  const testPromise = urls.map(async url =>{
    const response = await fetch(url);
    return response.text();
  })
  for(let item of textPromise){
    console.log(await item)
  }
}
// 上面的map方法的参数是 async 函数，但是它是并发执行的，因为在 async函数内部是继发执行，外部不受影响，后面的for...of 循环内部使用了 await 因此实现了按顺序输出
```

5. `async` 函数可以保留运行堆栈，在函数中执行异步任务的时候，函数会继续执行，当异步任务执行完毕的时候，a() 已经执行完毕并退出函数执行栈，这个b() 报错的时候，函数的上下文已经不是 a 了。 可以使用 `async` 函数来保留运行堆栈

```js
const a = ()=>{ b().then(()=>c() )}
const a = async()=>{ 
  await b(); 
  c();
}
```





`generator/iterator` 也常常被跟异步一起来讲，我们必须说明 `generator/iterator`  并非异步代码，只是在缺少 `async/await` 的时候，一些框架（最著名的要数 co ）使用这样的特性来模拟 `async/await`, 使用`async/await`之后，就应该让 `generator/iterator` 回归本来的遍历器功能


## [async函数、promise函数和setTimeout函数执行顺序](../accumulation/eventloop.md)

---
参考链接：
- [理解 JavaScript 的 async/await](https://segmentfault.com/a/1190000007535316)
- [JavaScript异步编程](https://mp.weixin.qq.com/s?__biz=MzUxMzcxMzE5Ng==&mid=2247489913&idx=1&sn=1871be99b1505ebddef0e6739c22c7dd&chksm=f951ac3ace26252ce604775aa5b088e755ec4db60239bd2f4cb75710b2bc1dc5f91069ced91a&mpshare=1&scene=24&srcid=&key=fc93c054c797e24a9825efe44e4162eb807b81e9e23def9ac5f9a48f69230bd5817458d62dc5485f10ea722dfb9a2fddbf7e9e48530a4237eb59bf7afdcf3b90af8b94748be8d5f608fc0dfaa235f0ff&ascene=14&uin=MTU0NjgxNDgyMQ%3D%3D&devicetype=Windows+10&version=62060739&lang=zh_CN&pass_ticket=1lWRrrcw1iDMHByhDO4Uq%2B9V3ZN4YJZRJD5CN3C778ntyYlhyqSgD16d9tzM2DFm)

