# ES6 扩展运算符 `...` 和 rest参数
**扩展运算符 `...` 用于取出参数对象中（对象或者数组）的所有可遍历属性，拷贝到当前对象之中**，ES2018将这个运算符引入了对象

### 对象的扩展运算符
- 对象的扩展运算符 `...` 用于取出参数对象中的所有可遍历属性（enumerable），拷贝到当前对象之中，同时还可以过滤某些属性，因为后面的会覆盖前面的同名属性的值，浅拷贝的形式
- `Object.assign` 方法可以用于对象的合并，实现的效果是类似的，浅拷贝的形式
- 扩展运算符后面是空对象，则没有任何效果
- 扩展运算符后面不是对象，则会自动将其转换为对象
- 扩展运算符后面是字符串，它会转换成一个类似数组的对象，
- 扩展运算符等同于使用了 `Object.assign({}, temp)`方法
- 扩展运算符可以合并多个对象， `let a={...a, ...b}`
- 后面的属性会覆盖前面的属性
- 与数组的扩展运算符一样，对象的扩展运算符后面可以跟表达式，一般是三元表达式
- 扩展运算符的参数对象之中，如果有取值函数get，这个函数是会执行的
- 扩展运算符的解构赋值，不能复制继承自原型对象的属性
- 解构赋值可以扩展某个函数的参数，引入其他操作
```js
let a = {
  title: 'feagreg',
  content: 'feahgth',
  visibal: false
}

let b = {
  title: 'changevalue',
  ...a,
  visibal: '',
  content: 'contentvalue'
}
// { title: 'feagreg', content: 'contentvalue', visibal: '' }

let runtime = {
  ...{
    get x(){
      return 111
    }
  }
}
// {x: 111}
```

### 数据的扩展运算符
**扩展运算符(...)，类似于 rest 参数的逆运算，将一个数组转为用','隔开的参数序列**
- 可以将数组转换为参数序列
- 可以复制数组 `b=[...a]` 
- 可以求取数组的最大值，不需要使用 apply 方法
```js
	Math.max.apply(null, [14, 3, 77])
	Math.max(...[14, 3, 77])
  arr1.push(...arr2);  // 复制数组
  let arr = [5,...[1,23,3],6]  // 5,1,23,3,6
  let a = (...[1,2])  // 报错 Unexpected token '...'
  let b = ...[2,34]  // 报错 Unexpected token '...'
```

- 该运算符主要用于函数调用，将数组转变为参数序列
- 扩展运算符可以和正常的函数参数结合使用。
- 扩展运算符后面还可以放置表达式
- 如果后面是空数组，则没有任何效果
- 扩展运算符如果放在括号中，JS会认为这是函数的调用，上面的 `let a` 的形式
- 可以替代 apply 方法，也可以了解扩展运算符实现的功能，之前用 apply 也可以实现
- 扩展运算符可以将字符串转为真正的数组
- 任何实现了 Iterator 接口的对象，都可以转为真正的数组

> 其实扩展运算符内部调用的是数据结构的 Iterator 接口，因此主要具有 Iterator接口的对象，都可以使用扩展运算符，比如 Map，Set 结构，Generator函数


### rest 参数
rest参数用于获取函数多余的参数，这样就可以不用使用 `arguments` 对象了，rest 参数搭配的是一个数组
```js
function add(...values){
	let sum = 0;
	for(var i of values){
	  sum += i;
	}
	return sum;
}
add(1,2,3,4) // 10
```
> 传递给add函数的一组参数，被整合成了数组values 
> 在类的继承中  super(...params)


### rest 和 `arguments` 的区别 
- rest 参数只包含哪些没有对应形参的实参；而 `arguments` 对象包含了传递给函数的所有参数
- `arguments` 对象不是一个真正的数组，而 rest 参数是真实的数组实例，
- `arguments` 对象还有一些附加的属性， `callee` 属性(不建议使用)
- rest 参数之后不能再有其他参数了
- 函数的 length 属性，不包括 rest 参数


### 扩展运算符和解构赋值的结合使用
扩展运算符可以和解构复制结合起来，用于生成数组
```js
a = list[0] ,rest = list.slice(1)

[a, ...rest]  = list;

const [first, ...rest] = [] 
first // undefined
res // []
```