## JS编程中常见的错误
> 对于相同的逻辑错误，不同的浏览器可能具有不同的错误消息

### TypeError 类型的错误

1. Uncaught TypeError: Cannot read property`  当读取一个未定义的对象的属性或者调用其方法时，还可能在UI渲染时对初始化的状态操作不当。
2. ` TypeError: ‘undefined’ is not an object `  Safari 中读取属性或调用未定义对象上的方法时发生的错误。这个与第一点类似，只是不同的浏览器提示不同
3. `TypeError: null is not an object`  Safari 中读取属性或调用空对象上的方法时发生的错误。 需要注意的是 null 和 undefined 是不同的2个错误，null表示为空，undefined表示未赋值
> 如果在加载元素之前尝试在 JavaScript 中使用元素。 因为 DOM API 对于空白的对象引用返回值为 null。
> 任何执行和处理 DOM 元素的 JS 代码都应该在创建 DOM 元素之后执行。

4. `(unknown): Script error`  当未捕获的 JavaScript 错误(通过 window.onerror 处理程序引发的错误，而不是捕获在 try-catch 中)被浏览器的跨域策略限制时，会产生这类的脚本错误。(如果您将您的 JavaScript 代码托管在 CDN 上，则任何未被捕获的错误将被报告为“脚本错误” 而不是包含有用的堆栈信息)

5. `TypeError: Object doesn’t support property`  调用未定义的方法时发生在 IE 中的错误。
```js
// JS 中有一个命名空间 Rollbar 以及方法 isAwesome。 
this.isAwesome();   // 调用命令空间的方法
// 上面的方式在谷歌，火狐都没问题，但是在有些IE浏览器则会报错，找不到this的值，
Rollbar.isAwesome();  // 直接使用这种方式是保险的
```

6. `TypeError: ‘undefined’ is not a function` 调用未定义的函数时，这是 Chrome 中产生的错误。
7. `Uncaught RangeError ` 调用一个不终止的递归函数就会发生这种错误。 或者是给函数传递了超出特定范围的数字的时候
    - toExponential(digits) 和 toFixed(digits) 接受 0 到 100
    - toPrecision(digits) 接受 1 到 100
8. `TypeError: Cannot read property ‘length’`  
    - 读取未定义变量的长度属性而发生的错误。
    - 数组中找到定义的长度，但是如果数组未初始化或者变量在另一个上下文中，则可能会遇到此错误。
9. `Uncaught TypeError: Cannot set property` 访问一个未定义的变量时，它总是返回 undefined，我们不能获取或设置任何未定义的属性。

ReferenceError 类型的错误 - 引用错误

> 在所嵌套的作用域中遍寻不到所需的变量，引擎就会抛出 ReferenceError 即引用错误，这是说一个未申明的变量

1. ### `ReferenceError: event is not defined`  在使用 event 时遇到此错误，请确保使用传入的事件对象作为参数。像 IE 这样的旧浏览器提供了一个全局变量事件，但并不是所有浏览器都支持。