## 模块加载
在 ES6 之前，社区制定了一些模块加载方案，最主要的有 CommonJS 和 AMD 两种。前者用于服务器，后者用于浏览器。ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案。

ES6 模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在运行时确定这些东西。

### ES6的模块输出 export 命令
**模块功能主要由两个命令构成：export和import。export命令用于规定模块的对外接口，import命令用于输入其他模块提供的功能。**

```js
export const firstname = 'wujun'
export const age = 26

export {firstname, age}  // 上面的写法还可以用这种方式，简单显而易见

export function add(x,y){return x + y};

function v1(){}
function v2(){}
export {v1 as namev1, v1 as namev2, v2 as thorenamev2} 
// 还可以改变暴露出去的名字, v2还可以用不同的名字暴露两次

export 1 // 报错
function f(){}
export f; // 报错
```
- export 可以对外暴露变量，函数，类class
- export 规定对外的的接口，必须与模块内部的变量建议对应的关系
- export 语句输出的值，与其对应的接口是动态绑定关系，可以取到模块内实时的值，也就是内部的值是可以变化的
- export 可以位于模块的任何位置，但是必须在模块顶层作用域

### ES6的模块默认输出 export default 命令
上面的使用import命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载。为了给用户提供方便，让他们不用阅读文档就能加载模块，就要用到export default命令，为模块指定默认输出。
```js
export default function(){ console.log('foo') }
import allname from './moduleName'  
// 加载上面的模块可以指定任意的名字

export function fnname(){}
import {fnname} from './fnname.js'

// 正确
export default 42;
// 错误
export default var a = 1;

// 可以同时输入默认方法和其他接口,下面的模块写法
import _, { each, forEach } from 'lodash';
export default function(obj){} 
export function each(){}
export function forEach(){}
```
- export default 也可以用在非匿名函数
- export default 一个模块只能由一个默认输出
- export default 本质上就是输出一个 default 的变量，然后系统允许为它取任意名字
- export default也可以用来输出类。 `export default class{}`

### ES6的模块加载 import 命令
```js
import {firstName, lastName, year} from './profile.js';
import  * as all from './profile.js'; //同上面的写法，整体加载
import { lastName as surname } from './profile.js'; // 可以重命名

foo();
import {foo} from 'my_module' //是能够运行的
```
- import 命令输入的变量是只读的，不允许在加载模块的脚本里面改写接口
- 如果 `import {a} form './xxxx'` 其中的a是个对象，是可以改写的，但是不要在实际中使用，不易查错
- import 命令具有提升效果，会提升到整个模块的头部，首先执行
- import 命令是在编译阶段运行的，在代码运行之前（提升的原因）
- import 是静态执行，所以不能使用表达式和变量，这些只有在运行时才能确定结果的语法结构
- import 语句会执行所加载的模块， `import 'lodash'` 是可以的
- 如果多次重复执行加载同一模块，那么只会执行一次
- 可以实现一个模块的整体加载,采用 * 号的方式

### import()
前面介绍过，import命令会被 JavaScript 引擎静态分析，先于模块内的其他语句执行
```js
if(x ===3 ){
    import Mtmodule from './xxx'
}
```
上面的代码会报错，因为import是在编译时运行的，这个时候不会执行if语句，所以报语法错误，import和export命令只能在模块的顶层，不能在代码块之中，这样做有利于编码器提高效率，但是导致无法运行时加载模块，条件加载无法实现，require 是运行时加载的，import无法取代require的动态加载功能，所以有个提案就是使用 `import()` 来实现动态加载
```js
const path = `./${fileName}`;
const mymodule = require(path)
// 上面就是动态加载，require加载那个模块只能运行时才知道
```


### ES6 模块和CommonJS 模块的差异
ES6 模块与 CommonJS 模块完全不同。
1. CommonJS模块加载输出的是一个值的拷贝，ES6模块输出的是值的引用
2. CommonJS模块是运行时加载，ES6模块是编译时输出接口

第二个差异是因为 CommonJS 加载的是一个对象（module.exports属性），该对象只有在脚本运行完才会生成，而ES6模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成

CommonJS 模块输出的是值的拷贝，也就是说，一旦输出一个值，模块内部的变化就影响不到这个值
```js
var counter = 3;
function inCounter(){
    counter++;
}
module.exports ={
    counter:counter,
    inCounter:inCounter
}

var mod = require('./name') //上面代码
console.log(mod.counter)   //3 
mod.inCounter();
console.log(mod.counter)  //3
```
上面的模块加载之后，内部的变化就不会影响到外部的输出 mod.counter的值，因为它是一个原始类型的值，会被缓存起来，除非是函数才能得到变动后的值
```js
module.exports={
    get counter(){
        return counter;
    },
    inCounter:inCounter
}
// 这种写法才可以获取到变动后的值 利用取值函数
```
ES6 模块的运行机制与 CommonJS 不一样，JS引擎遇到import命令时，只会生成一个引用，等到脚本执行，再根据引用去读取值，这样就不会缓存值都是动态引用的
```js
export var  foo = 'bar'
setTimeout(()=>foo = 'baz', 500);

import  {foo } from './path'
console.log(foo)
setTimeout(()=> console.log(foo), 500);

// bar
// baz
```


### Node加载
Node 对 ES6 模块的处理比较麻烦，因为它有自己的 CommonJS 模块格式，与 ES6 模块格式是不兼容的。目前的解决方案是，将两者分开，ES6 模块和 CommonJS 采用各自的加载方案，关键字**require 与 exports**。**一个模块首先要判断是 CommonJS模块还是 ES6 模块，加载方式就不同，暴露接口的方式也不同**。

node中的exports和module.exports的区别一句话概括就是：require方法能看到的只有module.exports这个对象，它是看不到exports对象的，而我们写模块的时候用到的exports对象实际上只是对module.exports的引用，exports和module.exports都属于Object类型，属于引用类型

> Node 要求 ES6 模块采用.mjs后缀文件名。也就是说，只要脚本文件里面使用import或者export命令，那么就必须采用.mjs后缀名。require命令不能加载.mjs文件，会报错，只有import命令才可以加载.mjs文件。反过来，.mjs文件里面也不能使用require命令，必须使用import。

```js
// 返回一个变量、JSON对象
var exp ={ "version":"1.0", "module":"1"}
module.exports = exp;

var MATH = { "PI":3.14, "e": 2.72}
module.exports = MATH;

var fn = function(){}
module.exports = fn;

// 一次返回多个函数，变量，
var fn1 = function(){}
var fn2 = function(){}
exports.fn1 = fn1;
exports.fn2 = fn2;
// 引用 ,两种方式都可以
var fnall = require('./path')
var {fn1, fn2} = require('./path')


// 一次 require 多个模块
var module_collection = {
    "module1": require('./module1'),
    "module2": require('./module2')
}
module.exports = module_collection;
// 引用
var module_collection  = require('./module.collection')
var module1 = module_collection.module1;
```

```js
const Router = require('koa-router'); // 采用这种加载方式
```
不能使用 import ，因为import要求在编译时就指定引用，而 require 是运行时才加载