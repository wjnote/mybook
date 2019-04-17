## ES6 扩展运算符 ... 和 rest参数
**扩展运算符 (…) 用于取出参数对象中（对象或者数组）的所有可遍历属性，拷贝到当前对象之中**

### 对象的扩展运算符
- 对象的扩展运算符（...） 用于取出参数对象中的所有可遍历属性，拷贝到当前对象之中，同时还可以过滤某些属性，因为后面的会覆盖前面的同名属性的值，常用与redux众返回 state 的值
- Object.assign 方法可以用于对象的合并，实现的效果是类似的
- 但是注意上面 2 种方式都是对象的浅拷贝
```javascript
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
console.log(b)
// { title: 'feagreg', content: 'contentvalue', visibal: '' }
```

### 数据的扩展运算符
- 可以将数组转换为参数序列
- 可以复制数组
- 可以求取数组的最大值，不需要使用 apply 方法
```javascript
	Math.max.apply(null, [14, 3, 77])
	Math.max(...[14, 3, 77])
	arr1.push(...arr2);  // 复制数组
```



### rest 参数
rest参数用于获取函数多余的参数，这样就可以不用使用 arguments 对象了，rest 参数搭配的是一个数组
```javascript
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

#### rest 和arguments 的区别 
- rest 参数只包含哪些没有对应形参的实参；而 arguments 对象包含了传递给函数的所有参数
- arguments 对象不是一个真正的数组，而 rest 参数是真实的数组实例，
- arguments 对象还有一些附加的属性， callee 属性(不建议使用)
- rest 参数之后不能再有其他参数了
- 函数的 length 属性，不包括 rest 参数