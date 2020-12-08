# 主要是ES6 开发中记录的一些点

### Object.assign 多个对象合并
- Object.assign 是浅拷贝，应用类型的值，拷贝的是它的引用
- 可以拷贝 Symbol 属性
- 不能拷贝不可枚举的属性
- Object.assign保证第一个参数是对象，如果是基本类型会转换为基本包装类型，null/undefined没有基本包装类型就会报错
- 使用等号赋值，如果被赋值的对象的属性有setter函数会触发setter函数,同理如果有getter函数,也会调用赋值对象的属性的getter函数(这就是为什么`Object.assign`无法合并对象属性的访问器,因为它会直接执行对应的`getter/setter`函数而不是合并它们,如果需要合并对象属性的`getter/setter`函数,可以使用ES7提供的`Object.getOwnPropertyDescriptors`和`Object.defineProperties`这2个API实现)

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