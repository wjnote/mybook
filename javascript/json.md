## 深入理解JSON
- [JSON官网](http://www.json.org/json-zh.html)
- [JSON-MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON)

我们可以先来看一下下面的例子，转化为JSON字符串是怎么样的
```js
var friend={
  firstname:'good',
  'lastname':'Man',
  adress: undefined,
  phone: ['123456', undefined],
  fullname:function(){
    return this.firstname + '' + this.lastname
  }
}
var friends={
  firstname:'good',
  'lastname':'Man',
  adress: undefined,
  phone: ['123456', undefined],
  fullname:function(){
    return this.firstname + '' + this.lastname
  },
  toJSON:function(){
    return 'this is test value'
  }
}
console.log(JSON.stringify(friend))
console.log(JSON.stringify(friends))
//  注意上述两个结果的区别
```
如果我想把其中的姓和名都转变为大写，该怎么实现 ？

### JSON是一种数据格式，基于文本，优于轻量
**任何单独的一个10进制数值、双引号字符串、布尔值和null都是有效符合JSON格式的**，  并不一定是对象的形式，并且JSON不是JS的子集，只是语法是基于JS的，JSON的语法表达更加严格，传输的时候占用带宽更小。
> 在JSON之前都是使用XML来传输数据，但是其中有很多无意义的HTML标签

> 需要注意的是JSON的属性值只能是:  **数值（10进制）、字符串（双引号）、布尔值和null，也可以是数组或者符合JSON要求的对象，不能是函数、NaN, Infinity, -Infinity和 undefined**

### 常用方法
在JS中我们主要会接触到两个和JSON相关的函数，一个叫JSON.stringify，你写的不符合JSON格式的JS对象都能帮你处理成符合JSON格式的字符串，所以你得知道它序列化的内部顺序

1. 如果存在 toJSON() 方法，并且能通过它取得有效的值，则调用该方法，否则返回对象本身（可以利用这个实现自定义的序列化内容）
2. 如果提供了第二个参数，应用这个函数过滤器，传入过滤器的值是第一步返回的值
3. 对第二步返回的值进行相应的序列化
4. 如果提供了第三个参数，执行相应的格式化

另一个叫JSON.parse，用于转化json字符串到JS数据结构，它很严格，你的JSON字符串如果构造地不对，是没办法解析的。

### 函数签名
`JSON.stringify(value[, replacer [, space]])`

- 第一个参数就是要序列化的对象
- 第二个参数可以是函数也可以是数组
    + 函数: 那么序列化过程中的每个属性都会被这个函数转化和处理,必须要返回值否则返回undefined
    + 数组: 那么只有包含在这个数组中的属性才会被序列化到最终的JSON字符串中
- 第三个参数是格式化的设置，如果只想设置第三个参数，则第二个参数可以设置为 `null`

`JSON.parse(text[, reviver])`
这里有一个可选的第二个参数，这个参数必须是一个函数，这个函数作用在属性已经被解析但是还没返回前，将属性处理后再返回。
```js
var friend = {
    "firstName": "Good",
    "lastName": "Man",
    "phone": { "home": "1234567", "work": ["7654321", "999000"] }
};
//我们先将其序列化
var friendAfter = JSON.stringify(friend);
//再将其解析出来，在第二个参数的函数中打印出key和value
// 第二个参数只能是函数，返回的结果是其中每个值经过函数处理后的值 遍历也是由内而外的，
JSON.parse(friendAfter, function(k, v) { 
    console.log('this key:',k,'---this value:', v);
});
```
> **注意查看结果的输出顺序，这个遍历是深度优先的遍历，遇到值是JSON格式的先遍历值这个JSON，在遍历外层的**

### 序列化的时候内部所做的操作
1. 键名不是双引号的（包括没有引号或者是单引号），会自动变成双引号；
2. 最后一个属性后面有逗号的，会被自动去掉
3. 非数组对象的属性不能保证以特定的顺序出现在序列化后的字符串中
4. 布尔值、数字、字符串的包装对象在序列化过程中会自动转换成对应的原始值 
5. undefined、任意的函数（除了toJSON函数）以及 symbol 值（symbol详见ES6对symbol的介绍）
    - 出现在非数组对象的属性值中：在序列化过程中会被忽略
    - 出现在数组中时：被转换成 null
```js
JSON.stringify({x: undefined, y: function(){return 1;}, z: Symbol("")});
//出现在非数组对象的属性值中被忽略："{}"
JSON.stringify([undefined, Object, Symbol("")]);
//出现在数组对象的属性值中，变成null："[null,null,null]"
```
6. NaN、Infinity和-Infinity，不论在数组还是非数组的对象中，都被转化为null 
7. 所有以 symbol 为属性键的属性都会被完全忽略掉，即便 replacer 参数中强制指定包含了它们
8. 不可枚举的属性会被忽略

开始的测试的结果
```js
{"firstname":"good","lastname":"Man","phone":["1234567",null]}
"this is test value"
```

