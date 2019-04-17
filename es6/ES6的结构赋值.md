### ES6的结构赋值

- 如果解构赋值不成功，变量的值就等于 undefined
- 两边的等式不完全一样，也可以解构成功
- 原生具有 Iterator  接口的，都可以采用解构赋值
- 解构赋值的时候，如果等号右边不是对象或者数组，会先转化为对象，null 和 undefined 不能转换就会报错


```javascript
function* fibs(){
	let a = 0;
	let b = 1;
	while(true){
		yield a;
		[a,b] = [b, a+b]
	}
}
let [first, second, third, fourth, fifth, sixth] = fibs();
sixth  // 5

let {toString: s} = 123;
s === Number.prototype.toString // true
```

### 默认值
1.  解构赋值允许指定默认值
2.  ES6 内部使用了  === 来判定，只有完全等于undefined才能使用默认值（null 不全等 undefined）
3.  对象也可以使用解构赋值，其中有个模式和变量的区别，不是按照顺序来的
4.  默认值生效的条件是对象的属性严格等于 undefined

```javascript
const node = {
  loc: {
	start:{
	    line: 1,
	    colum: 5,
	}
  }
}
let {loc, loc:{ start }, loc:{ start: {line }}} = node;

line //1
loc // Object {start: Object}
start // Object {line: 1, colum:5 }
```
> 上面代码中有三次解构赋值，分别是对loc . start. line 三个变量，最后一次对 line 属性的解构赋值中，只有 line 是变量，loc   start  都是模式，不是变量

> 对象的解构赋值也可以制定默认值
> var {x: y = 3} = {}      //y =3
> var {x: y =3} = {x: 5}   // y=5

### 函数参数的解构赋值
- 函数的参数也可以使用解构赋值
- 函数的参数也可以使用默认值
- 使用函数的默认值一般放在参数最后，否则不能实现省略
- 使用了函数参数的默认值，参数length属性表示没有默认值的参数个数，将失真，同时 (...args) 也不会计入length属性
- 如果可能，不要在模式中使用圆括号
- 在变量声明的时候使用圆括号会报错
- 参数有默认值的时候，函数进行初始化的时候，参数会形成一个单独的作用域，初始化结束，作用域消失

```javascript
function move({x=0,y=0} = {}){
	return[x,y]
}
move({x:3, y:5})
move({x:3})   //[3,0]
move({}) // [0,0]
move() // [0,0]
// 函数move的参数是一个对象，通过对这个对象进行解构赋值，得到变量

// **注意和下面写法的区别**
function move({x, y} ={x: 0, y: 0}){
	return [x,y]
}
move({x:3,y:8})  //[3,8]
move({x:3})   // [3, undefined]
move({}) // [undefined, undefined]
move()  // [0,0]
// 该函数的参数制定默认值，而不是为变量x,y指定默认值，和前一种得到不一样的结果
```

```javascript
function fetch(url, {body='', method='GET', headers={}}){}
fetch('url')  // 报错，因为第二个值是必传的

// 注意和下面这种写法的区别，下面是双重默认值
function fetch(url, { body= '', method="GET", headers={} } = {}){
	console.log(method)
}
fetch('url')
// **上面的 fetch 没有第二个参数时，函数参数的默认值就会生效，然后才是解构赋值的默认值生效 **
```

```javascript
var x = 1;
function foo(x, y=function(){x=2;}){
  var x =3;
  y();
  console.log(x)
}
foo();     // 3
x		// 1
```
函数foo的参数形成一个单独作用于，该作用域声明了变量 x, y ，Y中的x指向了同一个作用域的第一个参数X，函数内部又声明了内部变量X ，该变量与第一个参数不是同一个作用域，所以不是同一个变量
如果将  `var x = 3 变为 x=3`  则结果又不同