## 面试题采集

> 在平常的过程中 收集一些面试题，每个面试题弄清除原理也是不小的收获

1. 实现一个 `Promise.retry` 成功后 `resolve` 结果 失败后重试，超过一定次数才真正 `reject`

   ```js
   const PromiseObj = {};
   PromiseObj.retry = function(promFn, resolve1, reject1, max = 3) {
     console.log('剩余次数', max);
     return new Promise((resolve, reject) => {
       reject1 = reject1 || reject;
       resolve1 = resolve1 || resolve;
       promFn().then(resolve1, (err) => {
         if (!max) {
           reject1(err);
         } else {
           // this === PromiseObj  =》 true
           this.retry(promFn, resolve1, reject1, --max)
         }
       })
     })
   }
   const getProm = function() {
     let n = Math.random();
     return new Promise((resolve, reject) => {
       setTimeout(() => n > 0.8 ? resolve(n) : reject(n))
     }, 1000)
   }
   PromiseObj.retry(getProm)
     .then(res => console.log('success fn', res), err => console.log('fail fn', err))
   ```

2. 写出下面的输出结果，涉及了 变量定义提升、this指针指向、运算符优先级、原型、继承、全局变量污染、对象属性及原型属性优先级等等。
    ```js
    function Foo() {
      getName = function() {
        console.log(1)
      }
      return this
    }

    Foo.getName = function() { console.log(2)}
    Foo.prototype.getName = function() { console.log(3)}
    var getName = function() { console.log(4)}
    function getName() { console.log(5)}

    Foo.getName();   // 2

    // 变量声明提升 函数表达式提升
    getName(); // 4

    // 隐式声明  this指针问题
    Foo().getName(); // 1

    // 调用的方法是window上的方法
    getName(); // 1

    // JS运算符优先级考察 . 高于 new   相当于 new (Foo.getName)();
    new Foo.getName() // 2

    // 运算符优先级  实际执行  (new Foo()).getName();
    // 构造函数的返回值问题： 没有返回值/返回值为非引用类型 就返回构造函数的实例对象   返回值为引用类型则实际返回这个引用类型
    // 构造函数中的this指向的是当前实例化对象
    new Foo().getName() // 3

    // 运算符优先级， 实际执行 new ((new Foo()).getName)();
    // 先实例化对象，然后将其原型链上的getName方法作为构造函数再次 new，new操作符做了哪些操作
    new new Foo().getName() // 3
    ```

3. 前端工程师如何处理10W条数据，并实现搜索功能
   处理过程中需要考虑页面的性能和加载的性能，不能一次性将所有内容全部加载，大概的思路就是
    1. 采用懒加载的方式 + 分页
    2. 使用虚拟滚动技术加载select长列表
    3. 通过防抖函数来实现页面性能优化

    > 如果是高级前端的话还需要考虑更加优雅的实现方式，比如**组件化，算法优化，多线程**这类问题，比如大数据渲染可以使用虚拟列表来解决， 大量计算的逻辑的时候，可以使用 web worker 线程来计算，

4. 下面的代码输出结果是什么，怎么改为每隔1秒输出一个值
    ```js
      let list = [1,2,3]
      const square = num=>{
        return new Promise((resolve, reject)=>{
          setTimeout(()=>{
            resolve(num*num)
          }, 1000)
        })
      }
      function test(){
        list.forEach(async x=>{
          let res = await square(x);
          console.log(res)
        })
      }
      test();  // 最后的结果是隔了1秒后同时输出 1 4 9  
      /* 因为forEach是不能阻塞的，默认的请求是并行发起的，所以是同时输出 1 4 9  
         要实现每隔1秒输入一个值 可以使用下面几种方式 */
      
      // 1. 串行解决
      async function test(){
        for(let i=0; i <list.length;i++>){
          let x = list[i];
          let res = await square(x);
          console.log(res)
        }
      };
      async function test(){
        for(let x of list){
          let res = await square(x);
          console.log(res)
        }
      }
      // 2. 利用 Promise 本身的链式调用来实现串行  也是 axios 源码的实现方式
      let promise = Promise.resolve();
      function test(i=0){
        if(i===list.length) return;
        promise = promise.then(()=> square(list[i]));
        test(i+1);
      }
    ```



---

面试题收集参考的地址

- [每日一道面试题](https://github.com/Advanced-Frontend/Daily-Interview-Question)