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

