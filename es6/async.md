## async函数
async是'异步'的简写，而await可以认为是async wait的简写，async用于声明一个函数是异步的，而await用于等待一个异步方法执行完成。
> await只能出现在async函数中

async 函数的所起的作用就是它处理返回值的方式：将返回值封装成一个 Promise 对象。如果在函数中直接返回一个变量，async 也会将这个变量用 `Promise.resolve()`封装成promise对象
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

#### await 在等待什么
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


#### async/await函数和之前函数的比较
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

#### 注意点
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
2. 并发执行和继发执行
```js
async function dbFuc(db) {
  let docs = [{}, {}, {}];

  // 可能得到错误结果
  docs.forEach(async function (doc) {
    await db.post(doc);
  });
}
// 上面的代码可能不会正常执行，因为这三个post请求将是并发执行的，也就是同时执行，而不是继发执行的，需要使用下面这种写法
async function dbFuc(db) {
  let docs = [{}, {}, {}];

  for (let doc of docs) {
    await db.post(doc);
  }
}

// 如果也许需求是并发的 最好使用下面方式
async function fn(db){
    let docs=[{},{},{}]
    let promises = docs.map((doc)=>db.post(doc))
    let result = await Promise.all(promises)
}
```

##### [async函数、promise函数和setTimeout函数执行顺序](../accumulation/eventloop.md)

----
参考链接：
- [理解 JavaScript 的 async/await](https://segmentfault.com/a/1190000007535316)
- [JavaScript异步编程](https://mp.weixin.qq.com/s?__biz=MzUxMzcxMzE5Ng==&mid=2247489913&idx=1&sn=1871be99b1505ebddef0e6739c22c7dd&chksm=f951ac3ace26252ce604775aa5b088e755ec4db60239bd2f4cb75710b2bc1dc5f91069ced91a&mpshare=1&scene=24&srcid=&key=fc93c054c797e24a9825efe44e4162eb807b81e9e23def9ac5f9a48f69230bd5817458d62dc5485f10ea722dfb9a2fddbf7e9e48530a4237eb59bf7afdcf3b90af8b94748be8d5f608fc0dfaa235f0ff&ascene=14&uin=MTU0NjgxNDgyMQ%3D%3D&devicetype=Windows+10&version=62060739&lang=zh_CN&pass_ticket=1lWRrrcw1iDMHByhDO4Uq%2B9V3ZN4YJZRJD5CN3C778ntyYlhyqSgD16d9tzM2DFm)

