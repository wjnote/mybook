## 深拷贝
Object.assign 都是浅拷贝，本文来介绍一下深拷贝的实现，对象，数组，循环引用，引用丢失，Symbol和递归爆栈等情况
其实深拷贝可以分为2个步骤： 浅拷贝 + 递归的方式。

#### 第一步:简单实现
先写出简单浅拷贝的
```js
function cloneShallow(source){
    var target = {};
    for(var key in source){
        if(Object.prototype.hasOwnProperty.call(source,key)){
            target[key] = source[key];
        }
    }
    return target;
}
// 测试用例
var a = {
    name: "muyiy",
    book: {
        title: "You Don't Know JS",
        price: "45"
    },
    a1: undefined,
    a2: null,
    a3: 123
}
```
上面的浅拷贝的实现，只需要稍微改动一下，加上对象的判断，并使用递归调用就可以实现简单的深拷贝。
```js
function cloneDeep1(source) {
    var target = {};
    for(var key in source) {
        if (Object.prototype.hasOwnProperty.call(source, key)) {
            if (typeof source[key] === 'object') {
                target[key] = cloneDeep1(source[key]); // 注意这里
            } else {
                target[key] = source[key];
            }
        }
    }
    return target;
}
var b = cloneDeep1(a)
// { 
//   name: 'muyiy', 
//   book: { title: 'You Don\'t Know JS', price: '45' }, 
//   a1: undefined,
//   a2: {},
//   a3: 123
// }
```
简单的深拷贝，但是这个实现还存在一些很多问题
1. 没有对传入的参数校验，传入null应该返回null，而不是 ｛｝
2. 对于对象没有严谨的判断， `typeof null === 'object'`
3. 没有考虑数组的兼容

#### 第二步:拷贝数组
我们要判断数组,对象和 null 的区别，需要增加判断条件;
```js
function isObject(obj){
    return typeof obj === 'object' && obj != null;
}

// 兼容数组的写法
function cloneDeep2(source){
    if(!isObject(source)) return source; // 非对象

    var target = Array.isArray(source) ? [] : {};
    for(var key in source){
        if(Object.prototype.hasOwnProperty.call(source, key)){
            if(isObject(source[key])){
                target[key] = cloneDeep2(source[key])
            }else{
                target[key] = source[key];
            }
        }
    }
    return target;
}

// 测试用例
var b = cloneDeep2(a);
// { 
//   name: 'muyiy', 
//   book: { title: 'You Don\'t Know JS', price: '45' },
//   a1: undefined,
//   a2: null,
//   a3: 123
// }
```
上面的形式可以使用拷贝，但是会产生一个问题就是引用丢失；使用 cloneDeep2 进行深拷贝后就丢失了引用关系变成了两个不同的对象;
```js
var obj1 = {};
var obj2 = {a: obj1, b:obj1};
obj2.a === obj2.b   // true

var temp = cloneDeep2(obj2)
temp.a === temp.b   // false
```

#### 第三步： 循环引用
我们知道 JSON 无法深拷贝循环引用，遇到这种会报错(平常开发中的深拷贝使用JSON的方式是适用的)。
1、使用哈希表
解决方案就是使用哈希表，循环检测，我们设置一个数组或者哈希表来存储已拷贝过的对象，当检测到当前对象已存在于哈希表中时，取出该值并返回即可；这样也可以解决上面提到的引用丢失的问题。
```js
function cloneDeep3(source, hash=new WeakMap()){
    if(!isObject(source)) return source;
    if(hash.has(source)) return hash.get(source); // 查哈希表

    var target = Array.isArray(source) ? []:{};
    hash.set(source, target); // 哈希表设置值

    for(var key in source){
        if(Object.prototype.hasOwnProperty.call(source, key)){
          if(isObject(source[key])){
              target[key] = cloneDeep3(source[key],hash);  // 需要传入hash值
          }else{
              target[key] = source[key]
          }
        }
    }
    return target;
}
// 上面的代码使用了ES6的WeakMap实现，我们也可以使用数组来实现
function cloneDeep4(source, uniqueList){
    if(!isObject(source)) return source;
    if(!uniqueList) uniqueList = [];

    var target = Array.isArray(source) ? []:{};
    // 数据已经存在则返回保存的数据
    var uniqueData = find(uniqueList, source);
    if(uniqueData){
        return uniqueData.target;
    }
    // 数据不存在
    uniqueList.push({source: source, target: target});

    for(var key in source){
        if(Object.prototype.hasOwnProperty.call(source, key)){
          if(isObject(source[key])){
              target[key] = cloneDeep3(source[key],hash);  // 需要传入hash值
          }else{
              target[key] = source[key]
          }
        }
    }
    return target;
}
// 需要使用的外部函数 用于数组的查找
function find(arr, item){
    for(var i=0; i<arr.length; i++){
        if(arr[i].source === item){
            return arr[i]
        }
    }
    return null;
}
// 测试用例
a.circleRef = a;
var b = cloneDeep3(a);
// {
// 	name: "muyiy",
// 	a1: undefined,
//	a2: null,
// 	a3: 123,
// 	book: {title: "You Don't Know JS", price: "45"},
// 	circleRef: {name: "muyiy", book: {…}, a1: undefined, a2: null, a3: 123, …}
// }
```

#### 第四步：拷贝 Symbol
Symbol 在 ES6 下才有，我们需要一些方法来检测出 Symble 类型
1. `Object.getOwnPropertySymbols(...)` 返回一个给定对象自身的所有 Symbol 属性的数组，所以这个数组可能为空
2. `Reflect.ownKeys(...)` 返回一个由目标对象自身的属性键组成的数组

先查找有没有 Symbol属性，有的话就先遍历处理 Symbol 属性，然后在正常处理
```js
function cloneDeep4(source, hash = new WeakMap()) {
    if (!isObject(source)) return source; 
    if (hash.has(source)) return hash.get(source); 
      
    let target = Array.isArray(source) ? [] : {};
    hash.set(source, target);
    
    // ============= 新增代码
    let symKeys = Object.getOwnPropertySymbols(source); // 查找
    if (symKeys.length) { // 查找成功	
        symKeys.forEach(symKey => {
            if (isObject(source[symKey])) {
                target[symKey] = cloneDeep4(source[symKey], hash); 
            } else {
                target[symKey] = source[symKey];
            }    
        });
    }
    // =============
    
    for(let key in source) {
        if (Object.prototype.hasOwnProperty.call(source, key)) {
            if (isObject(source[key])) {
                target[key] = cloneDeep4(source[key], hash); 
            } else {
                target[key] = source[key];
            }
        }
    }
    return target;
}

// 测试用例
var sym1 = Symbol("a"); // 创建新的symbol类型
var sym2 = Symbol.for("b"); // 从全局的symbol注册?表设置和取得symbol

a[sym1] = "localSymbol";
a[sym2] = "globalSymbol";

var b = cloneDeep4(a);
// {
// 	name: "muyiy",
// 	a1: undefined,
//	a2: null,
// 	a3: 123,
// 	book: {title: "You Don't Know JS", price: "45"},
// 	circleRef: {name: "muyiy", book: {…}, a1: undefined, a2: null, a3: 123, …},
//  [Symbol(a)]: 'localSymbol',
//  [Symbol(b)]: 'globalSymbol'
// }
```

#### 破解递归爆栈
上面四个方法都是使用的递归调用的方式，会存在一个问题在于爆栈
> // RangeError: Maximum call stack size exceeded

破解递归爆栈的方法有2个，第一种是消除尾递归，第二个是不用递归改用循环。
```js
var  a ={
    a1: 1,
    a2: {
        b1: 1,
        b2: {
            c1:1
        }
    }
}
可以改为下面这种方式查看，就是一颗树的结构
    a
  /   \
 a1   a2        
 |    / \         
 1   b1 b2     
     |   |        
     1  c1
         |
         1       
```
用循环遍历一棵树，需要借助一个栈，当栈为空时就遍历完了，栈里存储了下一个需要拷贝的节点
首先我们往栈里放入种子数据，key 用来存储放哪一个父元素的哪一个子元素拷贝对象
然后遍历当前节点下的子元素，如果是对象就放到栈里，否则直接拷贝
```js
function cloneLoop(x) {
    const root = {};

    // 栈
    const loopList = [
        {
            parent: root,
            key: undefined,
            data: x,
        }
    ];

    while(loopList.length) {
        // 深度优先
        const node = loopList.pop();
        const parent = node.parent;
        const key = node.key;
        const data = node.data;

        // 初始化赋值目标，key为undefined则拷贝到父元素，否则拷贝到子元素
        let res = parent;
        if (typeof key !== 'undefined') {
            res = parent[key] = {};
        }

        for(let k in data) {
            if (data.hasOwnProperty(k)) {
                if (typeof data[k] === 'object') {
                    // 下一次循环
                    loopList.push({
                        parent: res,
                        key: k,
                        data: data[k],
                    });
                } else {
                    res[k] = data[k];
                }
            }
        }
    }

    return root;
}
```



[掘金参考博客](https://juejin.im/post/5c45112e6fb9a04a027aa8fe)
[深拷贝的终极探索](https://segmentfault.com/a/1190000016672263#articleHeader4)
