## ES6 使用中注意的一些点

### 箭头函数（ => ）定义函数
1. 箭头函数没有arguments （可以使用剩余运算符替代）
2. 箭头函数没有prototype属性，不能作为构造函数，不能绑定自己的 `super` 或 `new.target`
3. 没有this，它使用的this是词法的是引用的上下文的 this（只是等于外层的上下文this，并不是固定不变的）
```js
let controller = {
    a:1,
    makeRequest:function(){
        setTimeout(function(){
            console.log(this.a)
        })
    },
    testReturn:()=>{
        setTimeout(()=>{
            console.log(this.a)
        })
    }
}
// testReturn 中的this指向的是window，所以为 undefined

// 还需要注意箭头函数相比普通的函数，受操作符优先级影响
let callback;
callback = callback || function(){};    // 表达式成立
callback = callback || ()=>{};  // Malformed arrow function parameter list 
// 修改为 callback = callback || (()=>{}) 
```
> 不要在可能改变this指向的函数中使用箭头函数，类似Vue中的methods,computed中的方法,生命周期函数，Vue将这些函数的this绑定了当前组件的vm实例，如果使用箭头函数会强行改变this，因为箭头函数优先级最高（无法再使用call,apply,bind改变指向）


### iterator迭代器
iterator迭代器是ES6非常重要的概念，它却是另外4个ES6常用特性的实现基础（**解构赋值，剩余/扩展运算符，生成器，for of循环**），了解迭代器的概念有助于了解另外4个核心语法的原理，另外ES6新增的Map,Set数据结构也有使用到它。

对于可迭代的数据解构，ES6在内部部署了一个[Symbol.iterator]属性，它是一个函数，执行后会返回iterator对象（也叫迭代器对象，也叫iterator接口），拥有[Symbol.iterator]属性的对象即被视为可迭代的

默认的部署了迭代器的数据结构只有几个，**普通对象是没有部署迭代器接口的**，迭代器是一个对象，它具有一个next方法可以调用（返回值和Generator函数返回一致）。
- Array
- Map
- Set
- String
- TypedArray(类数组)
- 函数的 arguments对象
- NodeList 对象