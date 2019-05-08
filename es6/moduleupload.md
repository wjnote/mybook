## 模块加载
在 ES6 之前，社区制定了一些模块加载方案，最主要的有 CommonJS  CMD和 AMD 。前者用于服务器，后2者用于浏览器。ES6 在语言标准的层面上，实现了模块功能，完全可以取代 CommonJS CMD  和 AMD 规范，成为浏览器和服务器通用的模块解决方案。

ES6 模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在运行时确定这些东西。

### ES6的模块输出 export 命令
**ES6 模块功能主要由两个命令构成：export 和 import。export 命令用于规定模块的对外接口，import 命令用于输入其他模块提供的功能。**

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
- export 可以位于模块的任何位置，但是必须在模块顶层作用域内

### ES6的模块默认输出 export default 命令
export 输出方式使的用 import 命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载。为了给用户提供方便，让他们不用阅读文档就能加载模块，就要用到`export default`命令，为模块指定默认输出。
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
1. CommonJS 模块加载输出的是一个值的拷贝，ES6模块输出的是值的引用
2. CommonJS 模块是运行时加载，ES6模块是编译时输出接口
3. ES6 模块和 CommonJS 模块的运行机制不一样，ES6模块中JS引擎遇到import命令，就会生成一个只读引用，等到脚本执行了再去引用值，不会存在缓存行为，而CommonJS模块是会缓存基本类型的值
4. export 导出的是同一个值，不同的脚本加载这个接口，得到的都是同一个实例，一个脚本改变了这个实例，在另一个脚本加载的就是改变后的实例了。
5. CommonJS 支持动态导入，也就是 `require(${path}/xx.js)`，后者目前不支持，但是已有提案, import 语句必须位于顶层作用域中
6. CommonJS 是同步导入，因为用于服务端，文件都在本地，同步导入即使卡住主线程影响也不大。而后者是异步导入，因为用于浏览器，需要下载文件，如果也采用同步导入会对渲染有很大影响
7. ES6 模块中顶层的 this 指向 undefined，CommonJS 模块的顶层 this 指向当前模块。

**第二个差异是因为 CommonJS 加载的是一个对象(即module.exports属性)**，require 命令第一次执行的时候就会执行整个脚本，然后在内存中生成一个对象,而ES6模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成

```js
// CommonJS模块格式的加载原理

{
    id: 'modulename',
    exports: {...},
    loaded: true,
    ...
}
// 这就是Node内部加载模块生成一个对象，该对象的id表示模块的名字，
// exports属性就是模块输出的各个接口，loaded表示脚本是否执行完毕，还有其他一些属性，
// 只会在第一次加载时运行一次，以后需要用到就会从 exports上取值，就返回第一次运行的结果(缓存)
```

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

ES6 module 和 commonJS 在路径加载的时候，`import` 不能设置变量，因为`import`只能做静态导入不能动态的导入，如果是想在路径中增加变量，目前只能使用 `require`的方式

```js
const baseurl = 'xxx'
const img = require( baseurl + '.png')
```

> 注意： require 中不能是一个变量，必须是字符串拼接的形式



### Node加载(Node使用了CommonJS 规范)

Node 对 ES6 模块的处理比较麻烦，因为它有自己的 CommonJS 模块格式，与 ES6 模块标准是不兼容的。目前的解决方案是：将两者分开使用，ES6 模块和 CommonJS 采用各自的加载方案。一个模块首先要判断是 CommonJS 模块还是 ES6 模块，加载方式不同，暴露接口的方式也不同。

node 中的 exports 和 module.exports 的区别一句话概括就是：require 方法能看到的只有 module.exports 这个对象，它是看不到 exports 对象的，而我们写模块的时候用到的 exports 对象实际上只是对 module.exports 的引用，exports 和 module.exports 都属于 Object 类型，属于引用类型，在 node 中 module.exports 初始的设置为 {} , exports 也指向这个对象。
```js
// 下面两种写法是一样的
exports.name = function(){}

module.exports.name = function(){}

// 这个funtion是一块新的内存地址，导致exports和module.exports不存在关系
// 而require这个对象只能看到 module.exports这个对象，导致导不出去对象
exports = function(){}

module.exports = function(){}   // 这样就是正确的

var exports = module.exports
var load = function (module) {
    // 导出的东西
    var a = 1
    module.exports = a
    return module.exports
};
```

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
不能使用 import ，因为import要求在编译时就指定引用，而 require 是运行时才加载，如果是Node中使用 import的话，文件的后缀名必须是 .mjs;


### 内部变量
ES6 模块应该设计为通用的，同一个模块可以适用于浏览器和服务器，所以Node规定ES6模块不能使用CommonJS的一些特有的内部变量
1. 首先就是this关键字，ES6模块中顶层的this指向undefined；CommonJS顶层this指向当前模块
2. `arguments / require / module / exports` 这些顶层变量
3. `__filename`(当前模块文件的带有完整绝对路径的文件名)
4. `__dirname`(当前文件所在目录的完整目录名), 等同于 `path.dirname(__filename)`

>  如果想在ES6 模块中是用这些顶层变量，可以写一个 CommonJS 模块输出这些变量，ES6模块再加载这个变量，但是这样一下，ES6模块就不能同时适用于浏览器和服务器了，不建议使用。


### 循环加载(ES6模块和CommonJS模块)
CommonJS 模块的重要特性就是加载时执行，即脚本代码在require的时候，就会全部执行，**一旦出现某个模块被循环加载，就只会输出已经执行的部分，还未执行的部分不会加载**
```js
// a.js
exports.done = false;
var b = require('./b.js');
console.log(`a.js之中,b.done = %j...${b.done}`);
exports.done = true;
console.log(`a.js执行完毕`)

// b.js
exports.done = false;
var a = require('./a.js');
console.log(`b.js之中，a.done = ${a.done} `)
exports.done = true;
console.log(`b.js执行完毕`)

// main.js
var a = require('./a.js')
var b = require('./b.js')
```
上面代码中执行 a.js 执行到第二行的时候，会去加载 b.js 的内容，b.js中执行到第二行就回去加载 a.js的内容，这样就是循环加载，在 b.js中系统会去 a.js模块中加载对应对象exports属性取值，可以是a.js还没执行完，只会加载已经执行的部分，而不是最后的值，所以在b.js中加载的 `a.done = false`, 然后b.js执行完了，再把执行权交还给a.js往下执行
1. 在b.js之中。a.js还没能执行完，只执行了第一行
2. 在main.js执行到第二行的时候，不会再去执行b.js了，会直接取缓存中的exports的值
3. CommonJS输入的是被输出值的拷贝，不是引用，遇到模块加载时，返回的是已经执行的值，而不是代码全部执行的结果，两者可能会有差异

```js
var a = require('a')  // 安全的写法
var foo = require('a').foo;  // 危险的写法

exports.good = function(arg){
    return a.foo('good',arg)  // 使用a.foo的最新值
}
exports.bad = function(arg){
    return foo('bad',arg)  // 使用的是加载时已经执行的那部分的值，该值后面可能会被改写
}
```

ES6处理循环加载与CommonJS有本质不同，ES6模块是动态加载的，所以需要开发者保证，真正取值的时候能取到值
```js
// a.mjs
import {bar} from './b'
console.log('a.mjs')
console.log(bar)
export let foo = 'foo'

// b.mjs
import {foo} from './a'
console.log('b.mjs')
console.log(foo)
export let bar = 'bar'
```
上面就是循环加载，执行 a.mjs的时候结果如下
```shell
node --experimental-modules a.mjs
b.mjs
ReferenceError: foo is not defined
```
首先，执行a.mjs以后，发现引用了b.mjs，就会去执行b.mjs，在b.mjs中，已知它从a.mjs输入了foo接口，这时不会执行a.mjs，而是认为这个接口以及存在，继续往下执行，到第三行`console.log(foo)`,会发现这个接口没定义就会报错。下面的这种写法因为函数具有提升作用，在执行 `import {bar} from './b'` 之前，foo就已经定义了，就不会报错了(同样函数表示式也会报错的)。
```js
import {bar} from './b'
console.log('a.mjs')
console.log(bar)
function foo(){return 'foo'}
export {foo}

// b.mjs
import {foo} from './a'
console.log('b.mjs')
console.log(foo)
function bar(){return 'bar'}
export {bar}
```



### 一个模块适用于各种标准

同一个模块可以运行在前后端，在开发过程中需要考虑兼容前后端问题

```js
;(function(name, definition) {
  // 检测上下文环境是否为AMD或CMD
  var hasDefine = typeof define === 'function',
    // 检查上下文环境是否为Node
    hasExports = typeof module !== 'undefined' && module.exports;

  if (hasDefine) {
    // AMD环境或CMD环境
    define(definition);
  } else if (hasExports) {
    // 定义为普通Node模块
    module.exports = definition();
  } else {
    // 将模块的执行结果挂在window变量中，在浏览器中this指向window对象
    this[name] = definition();
  }
})('hello', function() {
  var add = function(a, b) {
    return a + b;
  }
  var subtraction = function(a, b) {
    return a - b;
  }
  return { add, subtraction };
});
```

1. 利用不同的标准都向环境中提供了几个不同的变量来判断适用什么标准
2. 上面代码中的 `hello` 表示如果不是浏览器环境和服务器环境，就将暴露的方法挂载到window上（本地`script`的方式可以测试）
3.  用 require  import 都可以引入