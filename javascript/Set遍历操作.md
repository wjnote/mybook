
ES6 提供了新的数据结构 Set，类似于数组，主要特点是成员都是唯一的，没有重复的值。是具有 iterable 接口的数据格式。

Set 本身是一个构造函数，用来生成Set 数据结构。也可以接受具有 iterable 接口的其他数据结构作为参数来初始化

上面两点就可以来实现数组和字符串的快速去重
`[…new Set(array)]   […new Set(‘abbcc’)].join('')`

1. Set 结构不会有重复的值
2. 向 Set 添加值得时候，不会发生类型转换（特别注意 NaN 等于自身）
3. 成员可以是任意的值
4. 两个对象总是不相等的


**Set 结构的实例具有以下属性 / 方法**
- Set.prototype.constructor:  构造函数，默认是 Set 函数
- Set.prototype.size :  返回实例成员总数
- add(value) ： 添加某个值，返回 Set 结构本身
- delete(value) : 删除某个值，返回一个布尔值，表示删除是否成功
- has(value) : 返回一个布尔值，表示该值是否为 Set 的成员
- clear() : 清楚所有成员，没有返回值

> 和数组的转换， Ayyay.from 方法可以将 Set 结构转为数组，就提供了数组去重
```javascript
function dedupe(array){ return Array.from(new Set(array)) }
```

### **Set 遍历操作**

**Set 的遍历顺序就是插入顺序，Set结构的实例默认可遍历，默认遍历器就是它的 values 方法**
- keys() : 返回键名的遍历器
- values() : 返回键值的遍历器
- entries() : 返回键值对的遍历器
- forEach() : 使用回调函数遍历每个成员
- 扩展运算符（ ... ）使用 `for of`循环，所以可以直接适用于 Set 结构
> 由于 Set 结构没有键名，只有键值（或者说键名和键值是同一个值），所以keys方法和values方法的行为完全一致


### WeakSet
WeakSet 是一个构造函数，可以使用new命令，创建 WeakSet 数据结构。
WeakSet 结构与 Set 类似，也是不重复的值的集合。但是，它与 Set 有几个区别
1. WeakSet 的成员只能是对象，而不能是其他类型的值。
2. WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中。
3. WeakSet 的成员是不适合引用的，因为它会随时消失。
4. WeakSet 不可遍历。


### ES6 提供了 Map 数据结构
JavaScript之前的对象只能用字符串当作键，Map 结构类似于对象，也是键值对的集合，但是键的范围 可以是各种类型的值，是一种更加完善的 Hash 结构实现，

任何具有Iterator 接口，且每个成员都是一个双元素的数组的数据结构，都可以当做 Map 构造函数的参数，Set  和 Map 都可以用来生成新的 Map
```javascript
// 实例化的实际流程 set 和 map 结构的数据也是如此
const items = [['name', 'wujun'], ['title', 'author']];
const map = new Map()
items.forEach(([key, valus]) => map.set(key, value));
```
1. 如果对一个键连续赋值，后面的会覆盖前面的
2. 读取到未知的键，返回 undefined
3. 只有对同一个对象的引用，Map结构才认为是同一个键，要注意对象内存地址
4. undefined 和 null 是不同的两个键

### Map结构具有的属性和方法
- size 属性，返回Map结构的成员总数
- set(key, valus) ,设置键和值，返回整个Map结构
- get(key) : 读取key对应的值
- has(key) : 返回一个布尔值，表示是否存在
- delete(key) : 删除某个键， 返回布尔值
- clear()  ： 清除Map结构所有成员
- keys()：返回键名的遍历器。
- values()：返回键值的遍历器。
- entries()：返回所有成员的遍历器。
- forEach()：遍历 Map 的所有成员。
> Map 结构的遍历顺序就是插入顺序

Map 结构转为数组结构，比较快速的是 ... , 这样就可以结合数组的 map 方法，filter 方法
```javascript
const map0 = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c');

const map1 = new Map(
  [...map0].filter(([k, v]) => k < 3)
);
// 产生 Map 结构 {1 => 'a', 2 => 'b'}

const map2 = new Map(
  [...map0].map(([k, v]) => [k * 2, '_' + v])
);
// 产生 Map 结构 {2 => '_a', 4 => '_b', 6 => '_c'}
```


### 与其他数据结构的互相转换
1. Map 转为数组 ： 最方便是 （ ...）
2. 数组转为Map： 将数组传入 Map 构造函数，
3. Map 转为对象： Map所有的键都是字符串就可以直接转为对象
4. 对象转为Map： 
```javascript
function objToStrMap(obj){
	let strMap = new Map();
	for(let k of Object.keys(obj)){
		strMap.set(k, obj[k])
	}
	return strMap;
}
```
5. Map转为JSON： Map键名都是字符串就和对象转JSON一致，如果Map键名有非字符串，这时可以转换为数组JSON
```javascript
function mapToArrayJson(map){
	return JSON.stringify([...map]);
}
let mymap = new Map().set(true, 7).set({'foo': 2}, ['abc'])
mapToArrayJson(mymap);
// Map [[true,7],[{"foo":2},["abc"]]]
```

### WeakMap 和 Map结构类似，也是用于生成键值对的集合，主要区别
1. WeakMap 直接受对象作为键名（null除外），不接受其他类型
2. WeakMap 的键名指向的对象，不计入垃圾回收机制，位置放置内存泄漏
3. WeakMap 弱引用的只是键名，而不是键值。键值依然是正常引用。
> 要往对象上添加数据，又不想干扰垃圾回收机制，就可以使用 WeakMap。一个典型应用场景是，在网页的 DOM 元素上添加数据，就可以使用WeakMap结构。当该 DOM 元素被清除，其所对应的WeakMap记录就会自动被移除。

### WeakMap 的语法
只有四个方法可用：get()、set()、has()、delete()。 没有遍历和size属性

WeakMap可以用来部署私有属性
```javascript
const _counter = new WeakMap();
const _action = new WeakMap();
class Countdown {
 constructor(counter, action){
	 _counter.set(this, counter)
	 _action.set(this, action)
 }
}
// 上面代码中两个内部属性，是实例的弱引用，所以如果实例删除，他们也会消失不会产生内存泄漏
```