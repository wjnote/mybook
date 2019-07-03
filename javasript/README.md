## js开发中注意的点
总结一些在开发中需要注意点的点或者是隐藏的坑

- 数组的 sort 方法，注意返回的应该是 1/-1，而不是 true/false 的形式
```js
const arr = [ 0, 1, 5, 10, 15, 10, 100, 99, 100 ];
arr.sort((v1,v2)=>{
    return v1 > v2
})
//  上面这种方式看似是正确的，但是如果数据一多的情况下就会出错
// 如果将数据改为  [0, 1, 5, 10, 15, 10, -2, -2, 100, 99, 100]
[0, 1, 5, 10, 15, 10, -2, -2, 100, 99, 100]  // 浏览器输出
[ 10, 0, -2, -2, 1, 5, 10, 15, 99, 100, 100 ] // node输出

// 所以应该使用
arr.sort((v1,v2)=>{
    return v1 > v2 ? 1: -1;
});
// 浏览器和node环境都能正确输出
```

- 数组中可以使用map和forEach、for来循环数组，但是应该使用forEach或者for来循环数组，而map作为数组关系的映射
```js
let sum = 0,arr = [];
for (let i = 0; i < 10 * 1000 * 1000; i++) {
  arr.push(i)
}
// 是在浏览器环境下测试，map花费的时间是其余两种的4倍，这种在node环境就很差了
console.time('p')
// arr.map(v => {
//   sum += v
// })
// 2266.562255859375ms

// arr.forEach(v=>{sum += v;})
// 528.845947265625ms

for(var i=0;i<arr.length;i++){
  sum += arr[i];
}
//  472.293212890625ms
console.timeEnd('p')
```

- 在js运算中，+0 / -0 是不同的，在加法中没有区别，但是在除法的时候要注意了，忘记检测 -0 会得到负无穷的情况经常导致错误，而区分 +0 -0 的方式就是检测 1/x 是Infinity  还是 -Infinity
- 在js中运算浮点数等于的情况可能会得到意想不到的值` 0.1 +0.2 == 0.3` 这个等式在js中会返回 false，这是由于js浮点运算的特性决定的，比较浮点数的计算，应该使用`Math.abs(0.1+0.2-0.3) <= Number.EPSILON` 检查两边的差值是否小于最小精度，在开发中也可以使用 `*100` 的方式来计算和比较

- 之前我们在函数方法参数的时候，不传其中一个，但是需要传后面的参数是
```js
method(para1, , para2) // 这样的形式就会报错

// 我们之前可以使用 null 、 undefined , 这两种形式都是可以的
method(para1, null, para2)
method(para1, undefined, para2)

// 在es6有了扩展运算符之后，可以利用 数组是松散的 结构来实现
method(...[para1, , para2]);
```

- 简单数组的拷贝可以通过 slice方法 concat方法 [...arr] 来实现，对于多维数组的实现，必须通过`JSON.parse(JSON.stringify(obj))`方法来实现

- 对象转基本类型，调用优先级 `Symbol.toPrimitive > valueOf() > toString()` 几个方法都可以重写