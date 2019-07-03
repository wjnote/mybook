## 编程小技巧

1. 快速将数字转换为字符串：可以使用连接符 + 后面跟一组空引号 “”
2. 字符串转为数字，可以使用 加法运算符 + 或者是 -0 两种方式

```js
let int = '15'
int = +int;
// or
int = int - 0;
```

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

