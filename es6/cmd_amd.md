## 模块化规范：CMD/AMD/ES module

目前流行的js模块化规范有CommonJS、AMD、CMD以及ES6的模块系统，[具体的使用方式](./moduleupload.md)

### CommonJS 规范

CommonJS 规范是为了解决 JavaScript 的作用域问题而定义的模块形式，可以使每个模块它自身的命名空间中执行。该规范的主要内容是，模块必须通过 `module.exports` 导出对外的变量或接口，通过 `require()` 来导入其他模块的输出到当前模块作用域中，

Node.js是commonJS规范的主要实践者，它有四个重要的环境变量为模块化的实现提供支持： `module`、`exports`、`require`、`global`

```js
module.exports = function(value){
  return value * 2;
}
const result = require('./module.js')
```

> CommonJS 是同步加载模块，但其实也有浏览器端的实现，其原理是先将所有模块都定义好并通过 `id` 索引，这样就可以方便的在浏览器环境中解析了

commonJs 规范的优缺点

1. 同步的方式加载模块，一般用于服务器端
2. npm 上已经有大量的优秀的包，简单并容易使用
3. 缺点是： 同步的模块加载方式不适合在浏览器环境中，同步意味着阻塞加载，浏览器资源是异步加载的，不能非阻塞的加载多个模块

### AMD 规范

AMD（异步模块定义）是为浏览器环境设计的，因为 CommonJS 模块系统是同步加载的，当前浏览器环境还没有准备好同步加载模块的条件。AMD 定义了一套 JavaScript 模块依赖异步加载标准

模块通过 `define` 函数定义在闭包中

```js
define(id?: String, dependencies?: String[], factory: Function|Object);

// eg:  定义一个 mymodule 模块 依赖jQuery 、 a模块， b模块
define('mymodule', ['jquery', 'a', 'b'], function($,a,b){
  // $ 是 jQuery 模块的输出
  $('body').text('hello world')
  var basic = 0;
  var add =function(x, y){
    return x+y;
  }
  if(false){
    b.fn();  // 该行代码是不会执行的 但是在函数执行之前，还是会执行 b模块的内容
  }
  return {
    add: add,
    basic: basic
  }
})
// 使用方式
require(['mymodule'], function(mymodule){})
```

AMD 优缺点

1. 异步加载模块，可以并行加载多个模块
2. 适合浏览器端开发
3. 缺点是提高了开发成本，代码不利于阅读，不符合通用模块的定义
4. 在函数执行之前先将所有模块加载执行，即便是后续没有用到的也执行了

### CMD 规范

CMD是另一种js模块化方案，它与 AMD 很类似，不同点在于AMD 推崇依赖前置，提前执行，而 CMD 推崇依赖就近，延迟执行。此规范是 sea.js 推广过程产生的。

```js
// AMD 的模块写法
define(['a', 'b', 'c', 'd'],function(a,b,c,d){
  // 在最前面就声明并初始化了要用到的所有模块
  a.dosomethings();
  if(false){
    b.fn();  // 即便没有用到该模块 也会提前执行
  }
})

// CMD 的模块写法
define(function(require, exports, module){
  var a = require('./a')   // 在需要时申明
  a.dosomething();
  if(false){
    var b = require('./b') // 就不会加载B模块
    b.fn();
  }
})

/* 实际开发中 使用sea.js 定义一个 math.js  */
define(function(require, exports, module){
  var $ = require('jquery.js')
  var add  = function(a,b){
    return a + b
  }
  exports.add = add
})
seajs.use(['math.js'],function(math){
  var sum = math.add(1, 2)
})
```

CMD 优缺点

1. 依赖就近，延迟执行，不会执行不需要的模块
2. 可以很容易在 node 中运行
3. 缺点是依赖 spm 打包，模块的加
4. 载逻辑偏重



### ES6 Module 

ES6 在语言标准的层面上，实现了模块功能，实现的方式很简单并希望成为浏览器和服务器通用的解决方案，主要由2个命令构成 `export  import`。ES6 模块的设计思想是尽量的静态化，使得编译时能确定模块的依赖关系，以及输入和输出的变量， `commonJS / AMD` 模块，都只能在运行时确定这些东西

```js
/** 定义模块 math.js **/
var basicNum = 0;
var add = function (a, b) {
    return a + b;
};
export { basicNum, add };

/** 引用模块 **/
import { basicNum, add } from './math';
function test(ele) {
    ele.textContent = add(99 + basicNum);
}
```

