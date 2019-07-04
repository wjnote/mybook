## 编程小技巧

3. 判断一个数组中有一个特定的值，可以使用find函数，但是IE中需要使用 `indexOf` 来判断返回值和 `-1` 的关系，还可以使用 `~index`的方式来判断

```js
let arrrvar arr = ['geag', 1, 'geagr']
let temp = 'geag'
let index = arr.indexOf(temp)
if (index > -1) {
  console.log(111, '其中有该值')
}
if (~index) {
  console.log(222, '其中有该值')
}
// 只有 ~-1 = 0 不会进入函数，其他的任何值都会进入函数
```

4. 目前政府的项目中使用iframe的方式， iframe 的通信中，父子iframe通信的方法和属性只能存在html文件中，如果是存在js文件中的方法和属性是访问不到的
```html
  <iframe name="myiframe" src="./iframe1.html" frameborder="0" class="myiframe"></iframe>
  iframeName.window.document.XXX // 调用子iframe的方法，

  parent.window.XXX  // 调用父iframe的方法
```

5. 在 JS 文件中有时候使用开关的方式来作为判定条件，设置默认值的时候最好设置为 false ，如果设置为true的话，当有值且为 false 的时候，在 `||` 运算的时候会默认取后面的值，这样就不能达到想要的效果


6. 页面中为一个元素绑定事件，事件执行后页面未刷新且元素还在，然后你再次点击，元素又被绑定个一次点击事件，这样第二次点击就会执行两次，以此类推。如何解决累加绑定 ??  元素是已经存在页面上的则可以使用 off 取消绑定的事件，但是如果是动态添加到页面上的元素呢 ？？

7. ES6在不同的浏览器有不同的表现，例如对象的属性简写在IE中不能实现，对象的key值为对象时，在谷歌中算一个属性，而在IE中不算属性会忽略

8. 对象字面量的方式来获取不同类型的变量（IE9也可以实现）
```js
const temp = 'dev';
const temptest = 'test';
const status = {
  'dev': 'this is dev',
  'prod': 'this is prod',
  test: function(){
    return 111;
  }
}[temp]

console.log(status)  // 'this is dev'
// 可以修改 temp的值来改变 status的结果值，这种在条件判断是可以适用的
```

9. 函数自执行的几种方式
```js
const Func = function(){}();  // 赋值给一个变量常用的

(function(){})()
(function(){}())
// 上面两种都是常用的， 还有可能会看到下面四种方式

+ function(){}();
- function(){}();
! function(){}();
~ function(){}();
```

10. 开发中函数内部有时候有很多比较复杂的分支，我们可以使用惰性载入函数，只是在第一次进入的时候进行判断，可以节省资源开支
```js
function Func(){
  if(a ===b ){
    Func = function(){
      console.log(x)
    }
  }else{
    Func = function(){
      console.log(Y)
    }
  }
  return Func();
}
```

11. 过滤数组中的`false`值，比如`0, false , null, undefined`等可以使用简易方法
```js
const arr = [0,1,'0','1',undefined, null, '1'+0];
arr.filter(Boolean);
// [1,'0','1','10'];
```
> 其中 Boolean 是一个函数，会判断传入的值返回 true 、 false；


12. 过滤数组的重复的值,利用了`indexOf`返回的是第一次出现的位置
```js
arr.filter((key,index)=>arr.indexOf(key) === index)
```


13. 利用扩展运算符，将多维数组变为一维数组
```js
//  如果只是二维数组
let arr = [1, [2,3,4], 5, [6,7]];
[].concat(...arr) // [1,2,3,4,5,6,7]

// 如果是多维数组 可以利用递归调用的方式, 一次一次的降维 直到一维数组
function flattenArray(arr) {
  const flattened = [].concat(...arr);
  return flattened.some(item => Array.isArray(item)) ? flattenArray(flattened) : flattened;
}
```

14. 利用`...`运算符来产生根据条件的不同的对象，下面的方式，不用`...`会报格式错误 ？？
```js
const getUser = (temp)={
  return {
    name: 'wuj',
    ...temp && {age: 26}
  }
}
//  结果就是传入为true时，返回两个属性
```