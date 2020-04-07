## 主要是ES6 开发中记录的一些点
对象的扩展运算符（...），ES2018 将这个运算符引入了对象。

### 扩展运算符作为解构赋值 用法
**对象的解构赋值用于从一个对象取值，相当于将目标对象自身的所有可遍历的（enumerable）、但尚未被读取的属性，分配到指定的对象上面。所有的键和它们的值，都会拷贝到新对象上面。扩展运算符一般在等号左边**
```js
let {x, y, ...z} = {x:1,y:2,c:3,d:4}
z // {c:3,d:4}
```
- 变量 z 是结构赋值的对象，获取等号右边的所有未读取的键，将他们全部拷贝
- 解构复制要求等号右边必须是一个对象，undefined,null不能转为对象的就会报错
- 解构赋值必须是最后一个参数，否则报错 `let {...x, y, z}`
- 解构赋值的拷贝是浅拷贝，复杂对象时拷贝的是对象的引用，而不是副本
- 扩展运算符的解构赋值，不能复制继承自原型对象的属性
- 解构赋值可以扩展某个函数的参数，引入其他操作
```js
const o = Object.create({x:1, y:2});
o.z = 3;

let {x, ...newObj} = o;
let {y, z} = newObj;

x //1
y // undefined
z // 3
// x 是读取值可以读取到，但是newObj复制的时候不能复制继承的属性，
```

### 扩展运算符 用法
**对象的扩展运算符（...） 用于取出参数对象的所有可遍历的对象，拷贝到当前对象之中.扩展运算符一般在等号左边**
```js
let z= {a:1, b:2}
let n= {...z}
n // {a: 1,b:2}

let foo = { ...['a','b','c']}
foo // {0:a, 1: b,2:c}   数据是特殊的对象，对象的扩展运算符也可以作用与数组
```
- 扩展运算符后面是空对象，则没有任何效果
- 扩展运算符后面不是对象，则会自动将其转换为对象
- 扩展运算符后面是字符串，它会转换成一个类似数组的对象，
- 扩展运算符等同于使用了 `Object.assign({}, temp)`方法
- 扩展运算符可以合并多个对象， `let a ={...a, ...b}`
- 后面的属性会覆盖前面的属性
- 与数组的扩展运算符一样，对象的扩展运算符后面可以跟表达式，一般是三元表达式
- 扩展运算符的参数对象之中，如果有取值函数get，这个函数是会执行的

```js
let runtime ={
    ...{
        get x(){
            throw new Error('throw now')
        }
    }
}
```

### 数组的扩展运算符
**扩展运算符(...)，类似于 rest 参数的逆运算，将一个数组转为用','隔开的参数序列**

```js
console.log(...[1,23,3])  // 1,23,3
console.log(5,...[1,23,3],6)  // 5,1,23,3,6

const number = [4,38]
function add (x,y){return x+y;}
add(...number)  //42
```

- 该运算符主要用于函数调用，将数组转变为参数序列
- 扩展运算符可以和正常的函数参数结合使用。
- 扩展运算符后面还可以放置表达式
- 如果后面是空数组，则没有任何效果
- 扩展运算符如果放在括号中，JS会认为这是函数的调用，如果不是则会报错`(...[1, 2])` 会报错
- 可以替代 apply 方法，也可以了解扩展运算符实现的功能，之前用 apply 也可以实现
- 扩展运算符可以将字符串转为真正的数组
- 任何实现了 Iterator 接口的对象，都可以转为真正的数组

> 其实扩展运算符内部调用的是数据结构的 Iterator 接口，因此主要具有 Iterator接口的对象，都可以使用扩展运算符，比如 Map，Set 结构，Generator函数


### 扩展运算符和解构赋值的结合使用
扩展运算符可以和解构复制结合起来，用于生成数组
```js
a = list[0] ,rest = list.slice(1)

[a, ...rest]  = list;

const [first, ...rest] = [] 
first // undefined
res // []
```

### Object.assign 多个对象合并
- Object.assign 是浅拷贝，应用类型的值，拷贝的是它的引用
- 可以拷贝 Symbol 属性
- 不能拷贝不可枚举的属性
- Object.assign保证第一个参数是对象，如果是基本类型会转换为基本包装类型，null/undefined没有基本包装类型就会报错
- 使用等号赋值，如果被赋值的对象的属性有setter函数会触发setter函数,同理如果有getter函数,也会调用赋值对象的属性的getter函数(这就是为什么Object.assign无法合并对象属性的访问器,因为它会直接执行对应的getter/setter函数而不是合并它们,如果需要合并对象属性的getter/setter函数,可以使用ES7提供的Object.getOwnPropertyDescriptors和Object.defineProperties这2个API实现)

> 需要特别注意的一点是，第一个参数如果是字符串，内部会转换为基本包装类型，而字符串基本包装类型的属性是只读的，不能修改 `Object.assign('abc','cfs')` 这样就会报错

### `Object.assign()` 函数会触发 setters，而展开运算符不会，但是并不是所有都能替换assign函数，有时会不能得到想要的结果
```js
// 需求是实现对象的合并
var obj1 = { foo: 'bar', x: 42 };
var obj2 = { foo: 'baz', y: 13 };
const merge = ( ...objects ) => ( { ...objects } );

var mergedObj = merge ( obj1, obj2);
// { 0: { foo: 'bar', x: 42 }, 1: { foo: 'baz', y: 13 } }

var mergedObj = merge ( {}, obj1, obj2);
// { 0: {}, 1: { foo: 'bar', x: 42 }, 2: { foo: 'baz', y: 13 } }

```

### 执行上下文： 执行的基础设施
JavaScript函数的主要复杂性来自于它携带的'环境部分'， 发展到今天的JavaScript，它所定义的环境部分已经比最初定义的复杂多了，JavaScript中与闭包‘环境部分’ 相对应的术语是 ‘词法环境’ ， 在JavaScript的设计中，词法环境只是JavaScript执行上下文的一部分。

> 执行上下文在发展中经历了社区的演变，定义比较混乱

**执行上下文在 ES3 中，包含了3个部分**
1. scope: 作用域，由于有一个链式的指针，所以常常被叫做作用域链
2. variable object: 变量对象，用于存储变量的对象
3. this value: this 的值

**执行上下文在ES5中，改进来命名方式**
1. lexical environment: 词法环境，当获取变量时使用
2. variable environment: 变量环境，当声明变量时使用
3. this value: this的值

> 上面的 1，2 两点在大部分情况是一样的

**执行上下文环境在ES2018中**
1. this 值被归入 `lexical environment` 中了
2. lexical environment：词法环境，当获取变量或者 this 值时使用。
3. variable environment：变量环境，当声明变量时使用
4. code evaluation state：用于恢复代码执行位置。
5. Function：执行的任务是函数时使用，表示正在被执行的函数。
6. ScriptOrModule：执行的任务是脚本或者模块时使用，表示正在被执行的代码。
7. Realm：使用的基础库和内置对象实例。
8. Generator：仅生成器上下文有这个属性，表示当前生成器。